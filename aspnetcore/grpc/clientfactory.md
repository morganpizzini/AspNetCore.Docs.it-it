---
title: Integrazione di factory client gRPC in .NET Core
author: jamesnk
description: Informazioni su come creare client gRPC utilizzando la factory client.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 11/12/2019
no-loc:
- SignalR
uid: grpc/clientfactory
ms.openlocfilehash: 3042bb61367f8b9a9f3142217ad329270ab2cca5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667167"
---
# <a name="grpc-client-factory-integration-in-net-core"></a>Integrazione di factory client gRPC in .NET Core

l'integrazione `HttpClientFactory` gRPC con offre un modo centralizzato per creare client gRPC. Può essere utilizzato come alternativa alla configurazione di [istanze client gRPC autonome.](xref:grpc/client) L'integrazione di fabbrica è disponibile nel pacchetto [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet.

La fabbrica offre i seguenti vantaggi:

* Fornisce una posizione centrale per la configurazione di istanze logiche del client gRPC
* Gestisce la durata del`HttpClientMessageHandler`
* Propagazione automatica della scadenza e dell'annullamento in un servizio gRPC ASP.NET Core

## <a name="register-grpc-clients"></a>Registrare i client gRPC

Per registrare un client `AddGrpcClient` gRPC, è `Startup.ConfigureServices`possibile utilizzare il metodo di estensione generico all'interno di , specificando la classe client tipizzata gRPC e l'indirizzo del servizio:

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

Il tipo di client gRPC viene registrato come temporaneo con inserimento di dipendenze (DI). Il client può ora essere iniettato e utilizzato direttamente nei tipi creati da DI. ASP.NET controller MVC SignalR principali, hub e servizi gRPC sono luoghi in cui i client gRPC possono essere inseriti automaticamente:

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a>Configurare HttpClient

`HttpClientFactory`crea `HttpClient` l'oggetto utilizzato dal client gRPC. I `HttpClientFactory` metodi standard possono essere utilizzati per aggiungere `HttpClientHandler` middleware per richieste in uscita o per configurare il sottostante del: `HttpClient`

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

Per ulteriori informazioni, vedere [Effettuare richieste HTTP utilizzando IHttpClientFactory](xref:fundamentals/http-requests).

## <a name="configure-channel-and-interceptors"></a>Configurare il canale e gli intercettori

Sono disponibili metodi specifici gRPC per:

* Configurare il canale sottostante di un client gRPC.
* Aggiungere `Interceptor` istanze che verranno utilizzate dal client durante l'esecuzione di chiamate gRPC.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a>Scadenza e propagazione dell'annullamento

I client gRPC creati dalla factory in un `EnableCallContextPropagation()` servizio gRPC possono essere configurati con per propagare automaticamente la scadenza e il token di annullamento per le chiamate figlio. Il `EnableCallContextPropagation()` metodo di estensione è disponibile nel pacchetto [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet.

La propagazione del contesto delle chiamate funziona leggendo la scadenza e il token di annullamento dal contesto della richiesta gRPC corrente e propagandoli automaticamente alle chiamate in uscita effettuate dal client gRPC. La propagazione del contesto delle chiamate è un ottimo modo per garantire che scenari gRPC complessi e annidati propaghino sempre la scadenza e l'annullamento.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

Per ulteriori informazioni sulle scadenze e sull'annullamento RPC, vedere Ciclo di [vita RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
