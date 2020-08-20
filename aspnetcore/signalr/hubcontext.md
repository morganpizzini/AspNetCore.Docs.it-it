---
title: SignalR HubContext
author: bradygaster
description: Informazioni su come usare il SignalR servizio ASP.NET Core HubContext per l'invio di notifiche ai client dall'esterno di un hub.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/hubcontext
ms.openlocfilehash: c6a4926be008feb2c9b708c56597070b96d8bd3f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633019"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="f7537-103">Inviare messaggi dall'esterno di un hub</span><span class="sxs-lookup"><span data-stu-id="f7537-103">Send messages from outside a hub</span></span>

<span data-ttu-id="f7537-104">Di [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="f7537-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="f7537-105">L' SignalR Hub è l'astrazione principale per l'invio di messaggi ai client connessi al SignalR Server.</span><span class="sxs-lookup"><span data-stu-id="f7537-105">The SignalR hub is the core abstraction for sending messages to clients connected to the SignalR server.</span></span> <span data-ttu-id="f7537-106">È anche possibile inviare messaggi da altre posizioni nell'app usando il `IHubContext` servizio.</span><span class="sxs-lookup"><span data-stu-id="f7537-106">It's also possible to send messages from other places in your app using the `IHubContext` service.</span></span> <span data-ttu-id="f7537-107">Questo articolo illustra come accedere a SignalR `IHubContext` per inviare notifiche ai client dall'esterno di un hub.</span><span class="sxs-lookup"><span data-stu-id="f7537-107">This article explains how to access a SignalR `IHubContext` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="f7537-108">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(procedura per il download)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="f7537-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-ihubcontext"></a><span data-ttu-id="f7537-109">Ottenere un'istanza di IHubContext</span><span class="sxs-lookup"><span data-stu-id="f7537-109">Get an instance of IHubContext</span></span>

<span data-ttu-id="f7537-110">In ASP.NET Core SignalR , è possibile accedere a un'istanza di `IHubContext` tramite l'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="f7537-110">In ASP.NET Core SignalR, you can access an instance of `IHubContext` via dependency injection.</span></span> <span data-ttu-id="f7537-111">È possibile inserire un'istanza di `IHubContext` in un controller, in un middleware o in un altro servizio di.</span><span class="sxs-lookup"><span data-stu-id="f7537-111">You can inject an instance of `IHubContext` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="f7537-112">Utilizzare l'istanza di per inviare messaggi ai client.</span><span class="sxs-lookup"><span data-stu-id="f7537-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="f7537-113">Questo comportamento è diverso da quello di ASP.NET 4. x SignalR che usava GlobalHost per fornire l'accesso a `IHubContext` .</span><span class="sxs-lookup"><span data-stu-id="f7537-113">This differs from ASP.NET 4.x SignalR which used GlobalHost to provide access to the `IHubContext`.</span></span> <span data-ttu-id="f7537-114">ASP.NET Core dispone di un Framework di inserimento delle dipendenze che elimina la necessità di questo singleton globale.</span><span class="sxs-lookup"><span data-stu-id="f7537-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a><span data-ttu-id="f7537-115">Inserire un'istanza di IHubContext in un controller</span><span class="sxs-lookup"><span data-stu-id="f7537-115">Inject an instance of IHubContext in a controller</span></span>

<span data-ttu-id="f7537-116">È possibile inserire un'istanza di `IHubContext` in un controller aggiungendola al costruttore:</span><span class="sxs-lookup"><span data-stu-id="f7537-116">You can inject an instance of `IHubContext` into a controller by adding it to your constructor:</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="f7537-117">Ora, con l'accesso a un'istanza di `IHubContext` , è possibile chiamare i metodi dell'hub come se si trovassero nell'hub.</span><span class="sxs-lookup"><span data-stu-id="f7537-117">Now, with access to an instance of `IHubContext`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a><span data-ttu-id="f7537-118">Ottenere un'istanza di IHubContext nel middleware</span><span class="sxs-lookup"><span data-stu-id="f7537-118">Get an instance of IHubContext in middleware</span></span>

<span data-ttu-id="f7537-119">Accedere all' `IHubContext` all'interno della pipeline del middleware come segue:</span><span class="sxs-lookup"><span data-stu-id="f7537-119">Access the `IHubContext` within the middleware pipeline like so:</span></span>

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<ChatHub>>();
    //...
    
    if (next != null)
    {
        await next.Invoke();
    }
});
```

> [!NOTE]
> <span data-ttu-id="f7537-120">Quando i metodi dell'hub vengono chiamati dall'esterno della `Hub` classe, non esiste alcun chiamante associato alla chiamata.</span><span class="sxs-lookup"><span data-stu-id="f7537-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="f7537-121">Non è pertanto possibile accedere alle `ConnectionId` `Caller` proprietà, e `Others` .</span><span class="sxs-lookup"><span data-stu-id="f7537-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="get-an-instance-of-ihubcontext-from-ihost"></a><span data-ttu-id="f7537-122">Ottenere un'istanza di IHubContext da IHost</span><span class="sxs-lookup"><span data-stu-id="f7537-122">Get an instance of IHubContext from IHost</span></span>

<span data-ttu-id="f7537-123">L'accesso a un `IHubContext` dall'host Web è utile per l'integrazione con aree esterne a ASP.NET Core, ad esempio usando Framework di inserimento di dipendenze di terze parti:</span><span class="sxs-lookup"><span data-stu-id="f7537-123">Accessing an `IHubContext` from the web host is useful for integrating with areas outside of ASP.NET Core, for example, using third-party dependency injection frameworks:</span></span>

```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();
            var hubContext = host.Services.GetService(typeof(IHubContext<ChatHub>));
            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder => {
                    webBuilder.UseStartup<Startup>();
                });
    }
```

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="f7537-124">Inserire un HubContext fortemente tipizzato</span><span class="sxs-lookup"><span data-stu-id="f7537-124">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="f7537-125">Per inserire un HubContext fortemente tipizzato, assicurarsi che l'hub erediti da `Hub<T>` .</span><span class="sxs-lookup"><span data-stu-id="f7537-125">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="f7537-126">Inserire l'oggetto usando l' `IHubContext<THub, T>` interfaccia anziché `IHubContext<THub>` .</span><span class="sxs-lookup"><span data-stu-id="f7537-126">Inject it using the `IHubContext<THub, T>` interface rather than `IHubContext<THub>`.</span></span>

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(message);
    }
}
```

## <a name="related-resources"></a><span data-ttu-id="f7537-127">Risorse correlate</span><span class="sxs-lookup"><span data-stu-id="f7537-127">Related resources</span></span>

* [<span data-ttu-id="f7537-128">Operazioni preliminari</span><span class="sxs-lookup"><span data-stu-id="f7537-128">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="f7537-129">Hub</span><span class="sxs-lookup"><span data-stu-id="f7537-129">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="f7537-130">Pubblicazione in Azure</span><span class="sxs-lookup"><span data-stu-id="f7537-130">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
