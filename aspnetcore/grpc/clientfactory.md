---
title: integrazione delle factory client gRPC in .NET Core
author: jamesnk
description: Informazioni su come creare client gRPC usando la factory client.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
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
uid: grpc/clientfactory
ms.openlocfilehash: c63bf495f558237ed801881d378953119791b8ce
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060950"
---
# <a name="grpc-client-factory-integration-in-net-core"></a>integrazione delle factory client gRPC in .NET Core

l'integrazione di gRPC con `HttpClientFactory` offre un modo centralizzato per creare client gRPC. Può essere usato come alternativa alla configurazione di [istanze client gRPC](xref:grpc/client)autonome. L'integrazione di Factory è disponibile nel pacchetto NuGet [.NET. ClientFactory di Grpc](https://www.nuget.org/packages/Grpc.Net.ClientFactory) .

La Factory offre i vantaggi seguenti:

* Fornisce una posizione centralizzata per la configurazione di istanze client gRPC logiche
* Gestisce la durata dell'oggetto sottostante `HttpClientMessageHandler`
* Propagazione automatica della scadenza e dell'annullamento in un ASP.NET Core servizio gRPC

## <a name="register-grpc-clients"></a>Registrare i client di gRPC

Per registrare un client gRPC, `AddGrpcClient` è possibile usare il metodo di estensione generico in `Startup.ConfigureServices` , specificando la classe client tipizzata gRPC e l'indirizzo del servizio:

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

Il tipo DI client gRPC è registrato come temporaneo con l'inserimento DI dipendenze. Il client può ora essere inserito e utilizzato direttamente nei tipi creati da DI. ASP.NET Core i controller MVC, gli SignalR Hub e i servizi gRPC sono posti in cui è possibile inserire automaticamente i client gRPC:

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

`HttpClientFactory` Crea l'oggetto `HttpClient` utilizzato dal client gRPC. I `HttpClientFactory` metodi standard possono essere usati per aggiungere il middleware della richiesta in uscita o per configurare il sottostante `HttpClientHandler` di `HttpClient` :

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

Per altre informazioni, vedere [creare richieste HTTP con IHttpClientFactory](xref:fundamentals/http-requests).

## <a name="configure-channel-and-interceptors"></a>Configurare canale e intercettori

sono disponibili metodi specifici di gRPC per:

* Configurare il canale sottostante di un client gRPC.
* Aggiungere `Interceptor` le istanze che il client userà per eseguire chiamate gRPC.

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

i client gRPC creati dalla factory in un servizio gRPC possono essere configurati con `EnableCallContextPropagation()` per propagare automaticamente la scadenza e il token di annullamento alle chiamate figlio. Il `EnableCallContextPropagation()` metodo di estensione è disponibile nel pacchetto NuGet [Grpc. AspNetCore. Server. ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) .

La propagazione del contesto di chiamata funziona leggendo la scadenza e il token di annullamento dal contesto della richiesta gRPC corrente e propagando automaticamente le chiamate in uscita effettuate dal client gRPC. La propagazione del contesto di chiamata è un ottimo modo per garantire che gli scenari gRPC complessi e nidificati propaghino sempre la scadenza e l'annullamento.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

Per impostazione predefinita, `EnableCallContextPropagation` genera un errore se il client viene usato al di fuori del contesto di una chiamata gRPC. L'errore è stato progettato per segnalare che non è presente un contesto di chiamata da propagare. Se si desidera utilizzare il client all'esterno di un contesto di chiamata, non visualizzare l'errore quando il client è configurato con `SuppressContextNotFoundErrors` :

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

Per ulteriori informazioni sulle scadenze e sull'annullamento RPC, vedere [ciclo di vita RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
