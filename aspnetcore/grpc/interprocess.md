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
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="b5635-103">Comunicazione tra processi con gRPC</span><span class="sxs-lookup"><span data-stu-id="b5635-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="b5635-104">Di [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="b5635-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="b5635-105">le chiamate gRPC tra un client e un servizio vengono in genere inviate tramite socket TCP.</span><span class="sxs-lookup"><span data-stu-id="b5635-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="b5635-106">TCP è ideale per la comunicazione attraverso una rete, ma la [comunicazione interprocesso (IPC)](https://wikipedia.org/wiki/Inter-process_communication) è più efficiente quando il client e il servizio si trovano nello stesso computer.</span><span class="sxs-lookup"><span data-stu-id="b5635-106">TCP is great for communicating across a network, but [inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient when the client and service are on the same machine.</span></span> <span data-ttu-id="b5635-107">Questo documento illustra come usare gRPC con i trasporti personalizzati negli scenari IPC.</span><span class="sxs-lookup"><span data-stu-id="b5635-107">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="b5635-108">Configurazione del server</span><span class="sxs-lookup"><span data-stu-id="b5635-108">Server configuration</span></span>

<span data-ttu-id="b5635-109">I trasporti personalizzati sono supportati da gheppio.</span><span class="sxs-lookup"><span data-stu-id="b5635-109">Custom transports are supported by Kestrel.</span></span> <span data-ttu-id="b5635-110">Il gheppio è configurato in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b5635-110">Kestrel is configured in *Program.cs*:</span></span>

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

<span data-ttu-id="b5635-111">L'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="b5635-111">The preceding example:</span></span>

* <span data-ttu-id="b5635-112">Configura gli endpoint di Gheppio in `ConfigureKestrel` .</span><span class="sxs-lookup"><span data-stu-id="b5635-112">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="b5635-113">Chiama <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> per ascoltare un [socket di dominio UNIX (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) con il percorso specificato.</span><span class="sxs-lookup"><span data-stu-id="b5635-113">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="b5635-114">Gheppio dispone del supporto incorporato per gli endpoint UDS.</span><span class="sxs-lookup"><span data-stu-id="b5635-114">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="b5635-115">I domini di aggiornamento sono supportati nelle versioni Linux, macOS e [moderne di Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span><span class="sxs-lookup"><span data-stu-id="b5635-115">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="b5635-116">Configurazione del client</span><span class="sxs-lookup"><span data-stu-id="b5635-116">Client configuration</span></span>

<span data-ttu-id="b5635-117">`GrpcChannel` supporta l'esecuzione di chiamate gRPC su trasporti personalizzati.</span><span class="sxs-lookup"><span data-stu-id="b5635-117">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="b5635-118">Quando viene creato, un canale può essere configurato con un oggetto con `SocketsHttpHandler` un oggetto personalizzato `ConnectionFactory` .</span><span class="sxs-lookup"><span data-stu-id="b5635-118">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectionFactory`.</span></span> <span data-ttu-id="b5635-119">La factory consente al client di stabilire connessioni su trasporti personalizzati, quindi di inviare richieste HTTP tramite il trasporto.</span><span class="sxs-lookup"><span data-stu-id="b5635-119">The factory allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b5635-120">`ConnectionFactory` è una nuova API in .NET 5 Release Candidate 1.</span><span class="sxs-lookup"><span data-stu-id="b5635-120">`ConnectionFactory` is a new API in .NET 5 release candidate 1.</span></span>

<span data-ttu-id="b5635-121">Esempio di Factory di connessione socket di dominio UNIX:</span><span class="sxs-lookup"><span data-stu-id="b5635-121">Unix domain sockets connection factory example:</span></span>

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

<span data-ttu-id="b5635-122">Uso della factory di connessione personalizzata per creare un canale:</span><span class="sxs-lookup"><span data-stu-id="b5635-122">Using the custom connection factory to create a channel:</span></span>

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

<span data-ttu-id="b5635-123">I canali creati usando il codice precedente inviano chiamate gRPC su socket di dominio UNIX.</span><span class="sxs-lookup"><span data-stu-id="b5635-123">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span>
