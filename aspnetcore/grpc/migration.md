---
title: Migrazione di servizi gRPC da C-core ad ASP.NET Core
author: juntaoluo
description: Informazioni su come spostare un'app gRPC basata su C-core esistente per l'esecuzione in ASP.NET Core stack.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/migration
ms.openlocfilehash: 1a230e470fa666b2aa6761b4d5dabd09264d2aae
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059832"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>Migrazione di servizi gRPC da C-core ad ASP.NET Core

A cura di [John Luo](https://github.com/juntaoluo)

A causa dell'implementazione dello stack sottostante, non tutte le funzionalità funzionano allo stesso modo tra le app [gRPC basate su C-core](https://grpc.io/blog/grpc-stacks) e le app basate su ASP.NET Core. Questo documento evidenzia le principali differenze per la migrazione tra i due stack.

## <a name="grpc-service-implementation-lifetime"></a>durata dell'implementazione del servizio gRPC

Nello stack ASP.NET Core, per impostazione predefinita, i servizi gRPC vengono creati con una [durata con ambito](xref:fundamentals/dependency-injection#service-lifetimes). Al contrario, gRPC C-core per impostazione predefinita viene associato a un servizio con una [durata singleton](xref:fundamentals/dependency-injection#service-lifetimes).

Una durata con ambito consente all'implementazione del servizio di risolvere altri servizi con durata con ambito. Ad esempio, una durata con ambito può essere risolta anche `DbContext` dal contenitore di inserimento delle dipendenze tramite l'inserimento del costruttore. Uso della durata con ambito:

* Per ogni richiesta viene costruita una nuova istanza dell'implementazione del servizio.
* Non è possibile condividere lo stato tra le richieste tramite i membri di istanza nel tipo di implementazione.
* Si prevede di archiviare gli stati condivisi in un servizio singleton nel contenitore DI inserimento delle dipendenze. Gli stati condivisi archiviati vengono risolti nel costruttore dell'implementazione del servizio gRPC.

Per ulteriori informazioni sulla durata del servizio, vedere <xref:fundamentals/dependency-injection#service-lifetimes> .

### <a name="add-a-singleton-service"></a>Aggiungere un servizio singleton

Per semplificare la transizione da un'implementazione di gRPC C-core a ASP.NET Core, è possibile modificare la durata del servizio dell'implementazione del servizio dall'ambito a Singleton. Ciò comporta l'aggiunta di un'istanza dell'implementazione del servizio al contenitore DI DI:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

Tuttavia, l'implementazione di un servizio con una durata singleton non è più in grado di risolvere i servizi con ambito tramite l'inserimento del costruttore.

## <a name="configure-grpc-services-options"></a>Configurare le opzioni dei servizi gRPC

Nelle app basate su C-core, le impostazioni come `grpc.max_receive_message_length` e `grpc.max_send_message_length` vengono configurate con `ChannelOption` quando si [costruisce l'istanza del server](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).

In ASP.NET Core, gRPC fornisce la configurazione tramite il `GrpcServiceOptions` tipo. Ad esempio, la dimensione massima del messaggio in ingresso di un servizio gRPC può essere configurata tramite `AddGrpc` . Nell'esempio seguente viene modificato il valore predefinito `MaxReceiveMessageSize` di 4 MB in 16 MB:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

Per ulteriori informazioni sulla configurazione, vedere <xref:grpc/configuration> .

## <a name="logging"></a>Registrazione

Le app basate su C-core si basano su `GrpcEnvironment` per [configurare il logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) a scopo di debug. Lo stack di ASP.NET Core fornisce questa funzionalità tramite l' [API di registrazione](xref:fundamentals/logging/index). Ad esempio, è possibile aggiungere un logger al servizio gRPC tramite l'inserimento del costruttore:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

Le app basate su C-core configurano HTTPS tramite la [Proprietà Server. Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports). Un concetto simile viene usato per configurare i server in ASP.NET Core. Ad esempio, gheppio usa la [configurazione dell'endpoint](xref:fundamentals/servers/kestrel#endpoint-configuration) per questa funzionalità.

## <a name="grpc-interceptors-vs-middleware"></a>gRPC intercettori vs middleware

ASP.NET Core [middleware](xref:fundamentals/middleware/index) offre funzionalità simili rispetto agli intercettori nelle app gRPC basate su C core. ASP.NET Core middleware e gli intercettori sono concettualmente simili. Both:

* Vengono usati per costruire una pipeline che gestisce una richiesta gRPC.
* Consentire l'esecuzione del lavoro prima o dopo il componente successivo nella pipeline.
* Fornire l'accesso a `HttpContext` :
  * Nel middleware `HttpContext` è un parametro.
  * Negli intercettori `HttpContext` è possibile accedere a tramite il `ServerCallContext` parametro con il `ServerCallContext.GetHttpContext` metodo di estensione. Si noti che questa funzionalità è specifica degli intercettori in esecuzione in ASP.NET Core.

differenze tra gli intercettori gRPC e il middleware ASP.NET Core:

* Intercettori
  * Operano sul livello gRPC dell'astrazione usando [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).
  * Fornire l'accesso a:
    * Messaggio deserializzato inviato a una chiamata.
    * Messaggio restituito dalla chiamata prima della serializzazione.
  * Consente di intercettare e gestire le eccezioni generate dai servizi gRPC.
* Middleware
  * Viene eseguito prima degli intercettori gRPC.
  * Opera sui messaggi HTTP/2 sottostanti.
  * Può accedere solo ai byte dai flussi di richiesta e risposta.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
