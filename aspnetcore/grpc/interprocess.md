---
title: Comunicazione tra processi con gRPC
author: jamesnk
description: Informazioni su come usare gRPC per la comunicazione tra processi.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 08/24/2020
no-loc:
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
uid: grpc/interprocess
ms.openlocfilehash: cfe8c98bb94817035437b2feb127a07bf5cad24b
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945701"
---
# <a name="inter-process-communication-with-grpc"></a>Comunicazione tra processi con gRPC

Di [James Newton-King](https://twitter.com/jamesnk)

le chiamate gRPC tra un client e un servizio vengono in genere inviate tramite socket TCP. TCP è ideale per la comunicazione attraverso una rete, ma la [comunicazione interprocesso (IPC)](https://wikipedia.org/wiki/Inter-process_communication) è più efficiente quando il client e il servizio si trovano nello stesso computer. Questo documento illustra come usare gRPC con i trasporti personalizzati negli scenari IPC.

## <a name="server-configuration"></a>Configurazione del server

I trasporti personalizzati sono supportati da gheppio. Il gheppio è configurato in *Program.cs*:

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(options =>
            {
                if (File.Exists(SocketPath))
                {
                    File.Delete(SocketPath);
                }
                options.ListenUnixSocket(SocketPath);
            });
        });
```

L'esempio precedente:

* Configura gli endpoint di Gheppio in `ConfigureKestrel` .
* Chiama <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> per ascoltare un [socket di dominio UNIX (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) con il percorso specificato.

Gheppio dispone del supporto incorporato per gli endpoint UDS. I domini di aggiornamento sono supportati nelle versioni Linux, macOS e [moderne di Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).

## <a name="client-configuration"></a>Configurazione del client

`GrpcChannel` supporta l'esecuzione di chiamate gRPC su trasporti personalizzati. Quando viene creato, un canale può essere configurato con un oggetto con `SocketsHttpHandler` un oggetto personalizzato `ConnectionFactory` . La factory consente al client di stabilire connessioni su trasporti personalizzati, quindi di inviare richieste HTTP tramite il trasporto.

> [!IMPORTANT]
> `ConnectionFactory` è una nuova API in .NET 5 Release Candidate 1.

Esempio di Factory di connessione socket di dominio UNIX:

```csharp
public class UnixDomainSocketConnectionFactory : SocketsConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
        : base(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified)
    {
        _endPoint = endPoint;
    }

    public override ValueTask<Connection> ConnectAsync(EndPoint? endPoint,
        IConnectionProperties? options = null, CancellationToken cancellationToken = default)
    {
        return base.ConnectAsync(_endPoint, options, cancellationToken);
    }
}
```

Uso della factory di connessione personalizzata per creare un canale:

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint)
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

I canali creati usando il codice precedente inviano chiamate gRPC su socket di dominio UNIX.
