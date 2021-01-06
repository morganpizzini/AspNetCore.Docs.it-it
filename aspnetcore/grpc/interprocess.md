---
title: Comunicazione tra processi con gRPC
author: jamesnk
description: Informazioni su come usare gRPC per la comunicazione tra processi.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 09/16/2020
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
uid: grpc/interprocess
ms.openlocfilehash: d806a340d8540fce8af6ccc6ff68325e4b733922
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059884"
---
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="d1eb7-103">Comunicazione tra processi con gRPC</span><span class="sxs-lookup"><span data-stu-id="d1eb7-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="d1eb7-104">Di [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="d1eb7-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="d1eb7-105">le chiamate gRPC tra un client e un servizio vengono in genere inviate tramite socket TCP.</span><span class="sxs-lookup"><span data-stu-id="d1eb7-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="d1eb7-106">Il protocollo TCP è stato progettato per la comunicazione attraverso una rete.</span><span class="sxs-lookup"><span data-stu-id="d1eb7-106">TCP was designed for communicating across a network.</span></span> <span data-ttu-id="d1eb7-107">La [comunicazione interprocesso (IPC)](https://wikipedia.org/wiki/Inter-process_communication) è più efficiente rispetto a TCP quando il client e il servizio si trovano nello stesso computer.</span><span class="sxs-lookup"><span data-stu-id="d1eb7-107">[Inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient than TCP when the client and service are on the same machine.</span></span> <span data-ttu-id="d1eb7-108">Questo documento illustra come usare gRPC con i trasporti personalizzati negli scenari IPC.</span><span class="sxs-lookup"><span data-stu-id="d1eb7-108">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="d1eb7-109">Configurazione del server</span><span class="sxs-lookup"><span data-stu-id="d1eb7-109">Server configuration</span></span>

<span data-ttu-id="d1eb7-110">I trasporti personalizzati sono supportati da [gheppio](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="d1eb7-110">Custom transports are supported by [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="d1eb7-111">Il gheppio è configurato in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d1eb7-111">Kestrel is configured in *Program.cs*:</span></span>

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

<span data-ttu-id="d1eb7-112">L'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="d1eb7-112">The preceding example:</span></span>

* <span data-ttu-id="d1eb7-113">Configura gli endpoint di Gheppio in `ConfigureKestrel` .</span><span class="sxs-lookup"><span data-stu-id="d1eb7-113">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="d1eb7-114">Chiama <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> per ascoltare un [socket di dominio UNIX (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) con il percorso specificato.</span><span class="sxs-lookup"><span data-stu-id="d1eb7-114">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="d1eb7-115">Gheppio dispone del supporto incorporato per gli endpoint UDS.</span><span class="sxs-lookup"><span data-stu-id="d1eb7-115">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="d1eb7-116">I domini di aggiornamento sono supportati nelle versioni Linux, macOS e [moderne di Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span><span class="sxs-lookup"><span data-stu-id="d1eb7-116">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="d1eb7-117">Configurazione del client</span><span class="sxs-lookup"><span data-stu-id="d1eb7-117">Client configuration</span></span>

<span data-ttu-id="d1eb7-118">`GrpcChannel` supporta l'esecuzione di chiamate gRPC su trasporti personalizzati.</span><span class="sxs-lookup"><span data-stu-id="d1eb7-118">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="d1eb7-119">Quando viene creato, un canale può essere configurato con un oggetto con `SocketsHttpHandler` un oggetto personalizzato `ConnectCallback` .</span><span class="sxs-lookup"><span data-stu-id="d1eb7-119">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectCallback`.</span></span> <span data-ttu-id="d1eb7-120">Il callback consente al client di stabilire connessioni su trasporti personalizzati, quindi di inviare richieste HTTP tramite il trasporto.</span><span class="sxs-lookup"><span data-stu-id="d1eb7-120">The callback allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d1eb7-121">`SocketsHttpHandler.ConnectCallback` è una nuova API in .NET 5 Release Candidate 2.</span><span class="sxs-lookup"><span data-stu-id="d1eb7-121">`SocketsHttpHandler.ConnectCallback` is a new API in .NET 5 release candidate 2.</span></span>

<span data-ttu-id="d1eb7-122">Esempio di Factory di connessione socket di dominio UNIX:</span><span class="sxs-lookup"><span data-stu-id="d1eb7-122">Unix domain sockets connection factory example:</span></span>

```csharp
public class UnixDomainSocketConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
    {
        _endPoint = endPoint;
    }

    public async ValueTask<Stream> ConnectAsync(SocketsHttpConnectionContext _,
        CancellationToken cancellationToken = default)
    {
        var socket = new Socket(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified);

        try
        {
            await socket.ConnectAsync(_endPoint, cancellationToken).ConfigureAwait(false);
            return new NetworkStream(socket, true);
        }
        catch
        {
            socket.Dispose();
            throw;
        }
    }
}
```

<span data-ttu-id="d1eb7-123">Uso della factory di connessione personalizzata per creare un canale:</span><span class="sxs-lookup"><span data-stu-id="d1eb7-123">Using the custom connection factory to create a channel:</span></span>

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var connectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectCallback = connectionFactory.ConnectAsync
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

<span data-ttu-id="d1eb7-124">I canali creati usando il codice precedente inviano chiamate gRPC su socket di dominio UNIX.</span><span class="sxs-lookup"><span data-stu-id="d1eb7-124">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span> <span data-ttu-id="d1eb7-125">Il supporto per altre tecnologie IPC può essere implementato usando l'estendibilità in gheppio e `SocketsHttpHandler` .</span><span class="sxs-lookup"><span data-stu-id="d1eb7-125">Support for other IPC technologies can be implemented using the extensibility in Kestrel and `SocketsHttpHandler`.</span></span>
