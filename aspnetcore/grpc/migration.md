---
title: Migrazione di servizi gRPC da C-core ad ASP.NET Core
author: juntaoluo
description: Scopri come spostare un'app gRPC basata su C-core esistente per l'esecuzione ASP.NET Stack Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
uid: grpc/migration
ms.openlocfilehash: 451171a041f7bbb3711babd73d2fa2e245aadd28
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664136"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>Migrazione di servizi gRPC da C-core ad ASP.NET Core

A cura di [John Luo](https://github.com/juntaoluo)

A causa dell'implementazione dello stack sottostante, non tutte le funzionalità funzionano allo stesso modo tra le app [gRPC basate su C-core](https://grpc.io/blog/grpc-stacks) e ASP.NET app basate su Core. Questo documento evidenzia le differenze principali per la migrazione tra i due stack.

## <a name="grpc-service-implementation-lifetime"></a>Durata dell'implementazione del servizio gRPC

Nello stack ASP.NET Core, i servizi gRPC, per impostazione predefinita, vengono creati con una [durata con ambito.](xref:fundamentals/dependency-injection#service-lifetimes) Al contrario, gRPC C-core per impostazione predefinita esegue il binding a un servizio con una [durata singleton](xref:fundamentals/dependency-injection#service-lifetimes).

Una durata con ambito consente all'implementazione del servizio di risolvere altri servizi con durata con ambito. Ad esempio, una durata con `DbContext` ambito può essere risolta anche dal contenitore DI tramite l'inserimento del costruttore. Utilizzo della durata con ambito:Using scoped lifetime:

* Viene costruita una nuova istanza dell'implementazione del servizio per ogni richiesta.
* Non è possibile condividere lo stato tra le richieste tramite membri di istanza nel tipo di implementazione.
* L'aspettativa consiste nell'archiviare gli stati condivisi in un servizio singleton nel contenitore DI. Gli stati condivisi archiviati vengono risolti nel costruttore dell'implementazione del servizio gRPC.

Per ulteriori informazioni sulle durate del servizio, vedere <xref:fundamentals/dependency-injection#service-lifetimes>.

### <a name="add-a-singleton-service"></a>Aggiungere un servizio singletonAdd a singleton service

Per facilitare la transizione da un'implementazione c-core gRPC a ASP.NET Core, è possibile modificare la durata del servizio dell'implementazione del servizio da con ambito a singleton. Ciò comporta l'aggiunta di un'istanza dell'implementazione del servizio al contenitore DI:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

Tuttavia, un'implementazione del servizio con una durata singleton non è più in grado di risolvere i servizi con ambito tramite l'inserimento del costruttore.

## <a name="configure-grpc-services-options"></a>Configurare le opzioni dei servizi gRPC

Nelle app basate su C-core, `grpc.max_send_message_length` le `ChannelOption` impostazioni come `grpc.max_receive_message_length` e vengono configurate con [durante la costruzione dell'istanza Server](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).

In ASP.NET Core, gRPC `GrpcServiceOptions` fornisce la configurazione tramite il tipo. Ad esempio, la dimensione massima dei messaggi in ingresso `AddGrpc`di un servizio gRPC può essere configurata tramite . Nell'esempio seguente `MaxReceiveMessageSize` viene modificato il valore predefinito di 4 MB a 16 MB:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

Per ulteriori informazioni sulla <xref:grpc/configuration>configurazione, vedere .

## <a name="logging"></a>Registrazione

Le app basate su C-core si basano su `GrpcEnvironment` di [configurare il logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) a scopo di debug. Lo stack ASP.NET Core fornisce questa funzionalità tramite [l'API di registrazione](xref:fundamentals/logging/index). Ad esempio, un logger può essere aggiunto al servizio gRPC tramite inserimento del costruttore:For example, a logger can be added to the gRPC service via constructor injection:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

Le app basate su C-core configurano HTTPS tramite la [proprietà Server.Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports). Un concetto simile viene utilizzato per configurare i server in ASP.NET Core. Ad esempio, Kestrel utilizza la [configurazione dell'endpoint](xref:fundamentals/servers/kestrel#endpoint-configuration) per questa funzionalità.

## <a name="grpc-interceptors-vs-middleware"></a>Intercettori gRPC e middleware

ASP.NET [middleware](xref:fundamentals/middleware/index) Core offre funzionalità simili rispetto agli intercettori nelle app gRPC basate su C-core. ASP.NET middleware Core e intercettori sono concettualmente simili. Both:

* Vengono utilizzati per costruire una pipeline che gestisce una richiesta gRPC.
* Consentire l'esecuzione del lavoro prima o dopo il componente successivo nella pipeline.
* Fornire l'accesso a `HttpContext`:
  * Nel middleware `HttpContext` è un parametro.
  * Negli intercettori `HttpContext` è possibile `ServerCallContext` accedere `ServerCallContext.GetHttpContext` al parametro con il metodo di estensione. Si noti che questa funzionalità è specifica per gli intercettori in esecuzione in ASP.NET Core.Note that this feature is specific to interceptors running in ASP.NET Core.

gRPC Interceptor differenze da ASP.NET Core Middleware:

* Intercettori:
  * Operare sul livello gRPC dell'astrazione utilizzando [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).
  * Fornire l'accesso a:
    * Messaggio deserializzato inviato a una chiamata.
    * Messaggio restituito dalla chiamata prima che venga serializzato.
  * Può intercettare e gestire le eccezioni generate dai servizi gRPC.
* Middleware:
  * Viene eseguito prima degli intercettori gRPC.
  * Opera sui messaggi HTTP/2 sottostanti.
  * Può accedere solo ai byte dei flussi di richiesta e risposta.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
