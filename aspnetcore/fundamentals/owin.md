---
title: Open Web Interface for .NET (OWIN) con ASP.NET Core
author: ardalis
description: Informazioni su come ASP.NET Core supporta Open Web Interface for .NET (OWIN) che consente ai server Web di disaccoppiare le app Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/owin
ms.openlocfilehash: 3d90788fa300bf099f44aa284e77879698240a74
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407824"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="7ad03-103">Open Web Interface for .NET (OWIN) con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ad03-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="7ad03-104">Di [Steve Smith](https://ardalis.com/) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7ad03-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7ad03-105">ASP.NET Core supporta Open Web Interface for .NET (OWIN).</span><span class="sxs-lookup"><span data-stu-id="7ad03-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="7ad03-106">OWIN consente alle app Web di essere disaccoppiate dai server Web.</span><span class="sxs-lookup"><span data-stu-id="7ad03-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="7ad03-107">Definisce un modo standard per usare il middleware in una pipeline per gestire le richieste e le risposte associate.</span><span class="sxs-lookup"><span data-stu-id="7ad03-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="7ad03-108">Le applicazioni ASP.NET Core e il middleware possono interagire con middleware, server e applicazioni basati su OWIN.</span><span class="sxs-lookup"><span data-stu-id="7ad03-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="7ad03-109">OWIN specifica un livello di disaccoppiamento che consente di usare contemporaneamente due framework con modelli a oggetti diversi.</span><span class="sxs-lookup"><span data-stu-id="7ad03-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="7ad03-110">Il pacchetto `Microsoft.AspNetCore.Owin` offre due implementazioni dell'adattatore:</span><span class="sxs-lookup"><span data-stu-id="7ad03-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="7ad03-111">Da ASP.NET Core a OWIN</span><span class="sxs-lookup"><span data-stu-id="7ad03-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="7ad03-112">Da OWIN a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ad03-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="7ad03-113">In questo modo ASP.NET Core può essere ospitato in un server/host compatibile con OWIN o altri componenti compatibili con OWIN possono essere eseguiti su ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ad03-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="7ad03-114">L'uso di questi adattatori comporta una riduzione delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="7ad03-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="7ad03-115">Le app che usano solo componenti di ASP.NET Core non devono usare il pacchetto o gli adattatori `Microsoft.AspNetCore.Owin`.</span><span class="sxs-lookup"><span data-stu-id="7ad03-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="7ad03-116">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7ad03-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="7ad03-117">Esecuzione del middleware OWIN nella pipeline ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ad03-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="7ad03-118">Il supporto di OWIN per ASP.NET Core viene distribuito come parte del pacchetto `Microsoft.AspNetCore.Owin`.</span><span class="sxs-lookup"><span data-stu-id="7ad03-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="7ad03-119">Per importare il supporto OWIN nel progetto è necessario installare il pacchetto.</span><span class="sxs-lookup"><span data-stu-id="7ad03-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="7ad03-120">Il middleware OWIN è conforme alle [specifiche OWIN](https://owin.org/spec/spec/owin-1.0.0.html), che richiedono l'interfaccia `Func<IDictionary<string, object>, Task>` e che siano impostate chiavi specifiche, come ad esempio `owin.ResponseBody`.</span><span class="sxs-lookup"><span data-stu-id="7ad03-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="7ad03-121">Il semplice middleware OWIN illustrato di seguito visualizza "Hello World":</span><span class="sxs-lookup"><span data-stu-id="7ad03-121">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

<span data-ttu-id="7ad03-122">La firma di esempio restituisce un oggetto `Task` e accetta un oggetto `IDictionary<string, object>` come richiesto da OWIN.</span><span class="sxs-lookup"><span data-stu-id="7ad03-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="7ad03-123">Il codice seguente illustra come aggiungere il middleware `OwinHello`, illustrato in precedenza, alla pipeline ASP.NET Core con il metodo di estensione `UseOwin`.</span><span class="sxs-lookup"><span data-stu-id="7ad03-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="7ad03-124">È possibile configurare altre azioni da eseguire all'interno della pipeline OWIN.</span><span class="sxs-lookup"><span data-stu-id="7ad03-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="7ad03-125">Le intestazioni di risposta devono essere modificate solo prima della prima scrittura nel flusso di risposta.</span><span class="sxs-lookup"><span data-stu-id="7ad03-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="7ad03-126">Le chiamate multiple al metodo `UseOwin` sono sconsigliate per non compromettere le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="7ad03-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="7ad03-127">I componenti OWIN funzionano meglio se raggruppati insieme.</span><span class="sxs-lookup"><span data-stu-id="7ad03-127">OWIN components will operate best if grouped together.</span></span>

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        return async environment =>
        {
            // Do something before.
            await next(environment);
            // Do something after.
        };
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a><span data-ttu-id="7ad03-128">Uso dell'hosting di ASP.NET Core in un server basato su OWIN</span><span class="sxs-lookup"><span data-stu-id="7ad03-128">Using ASP.NET Core Hosting on an OWIN-based server</span></span>

<span data-ttu-id="7ad03-129">I server basati su OWIN possono ospitare app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ad03-129">OWIN-based servers can host ASP.NET Core apps.</span></span> <span data-ttu-id="7ad03-130">Un server di questo tipo è [Nowin](https://github.com/Bobris/Nowin), un server Web OWIN per .NET.</span><span class="sxs-lookup"><span data-stu-id="7ad03-130">One such server is [Nowin](https://github.com/Bobris/Nowin), a .NET OWIN web server.</span></span> <span data-ttu-id="7ad03-131">Nell'esempio di questo articolo è stato incluso un progetto che fa riferimento a Nowin e lo usa per creare un oggetto `IServer` in grado di eseguire l'hosting automatico di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ad03-131">In the sample for this article, I've included a project that references Nowin and uses it to create an `IServer` capable of self-hosting ASP.NET Core.</span></span>

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

<span data-ttu-id="7ad03-132">`IServer` è un'interfaccia che richiede una proprietà `Features` e un metodo `Start`.</span><span class="sxs-lookup"><span data-stu-id="7ad03-132">`IServer` is an interface that requires a `Features` property and a `Start` method.</span></span>

<span data-ttu-id="7ad03-133">`Start` è responsabile della configurazione e dell'avvio del server, che in questo caso avvengono tramite una serie di chiamate API Fluent che impostano gli indirizzi analizzati da IServerAddressesFeature.</span><span class="sxs-lookup"><span data-stu-id="7ad03-133">`Start` is responsible for configuring and starting the server, which in this case is done through a series of fluent API calls that set addresses parsed from the IServerAddressesFeature.</span></span> <span data-ttu-id="7ad03-134">Si noti che la configurazione Fluent della variabile `_builder` specifica che le richieste vengano gestite dall'oggetto `appFunc` definito in precedenza nel metodo.</span><span class="sxs-lookup"><span data-stu-id="7ad03-134">Note that the fluent configuration of the `_builder` variable specifies that requests will be handled by the `appFunc` defined earlier in the method.</span></span> <span data-ttu-id="7ad03-135">L'oggetto `Func` viene chiamato su ogni richiesta per elaborare le richieste in ingresso.</span><span class="sxs-lookup"><span data-stu-id="7ad03-135">This `Func` is called on each request to process incoming requests.</span></span>

<span data-ttu-id="7ad03-136">Verrà aggiunta anche un'estensione `IWebHostBuilder` per semplificare l'aggiunta e la configurazione del server Nowin.</span><span class="sxs-lookup"><span data-stu-id="7ad03-136">We'll also add an `IWebHostBuilder` extension to make it easy to add and configure the Nowin server.</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting.Server;
using Microsoft.Extensions.DependencyInjection;
using Nowin;
using NowinSample;

namespace Microsoft.AspNetCore.Hosting
{
    public static class NowinWebHostBuilderExtensions
    {
        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder)
        {
            return builder.ConfigureServices(services =>
            {
                services.AddSingleton<IServer, NowinServer>();
            });
        }

        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder, Action<ServerBuilder> configure)
        {
            builder.ConfigureServices(services =>
            {
                services.Configure(configure);
            });
            return builder.UseNowin();
        }
    }
}
```

<span data-ttu-id="7ad03-137">Con questa configurazione, chiamare l'estensione in *Program.cs* per eseguire un'app ASP.NET Core usando questo server personalizzato:</span><span class="sxs-lookup"><span data-stu-id="7ad03-137">With this in place, invoke the extension in *Program.cs* to run an ASP.NET Core app using this custom server:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;

namespace NowinSample
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = new WebHostBuilder()
                .UseNowin()
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseIISIntegration()
                .UseStartup<Startup>()
                .Build();

            host.Run();
        }
    }
}
```

<span data-ttu-id="7ad03-138">Altre informazioni sui [server ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="7ad03-138">Learn more about [ASP.NET Core Servers](xref:fundamentals/servers/index).</span></span>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="7ad03-139">Eseguire ASP.NET Core in un server basato su OWIN e usare il supporto di WebSocket</span><span class="sxs-lookup"><span data-stu-id="7ad03-139">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="7ad03-140">Un altro esempio di funzionalità dei server basati su OWIN che può essere sfruttata da ASP.NET Core è l'accesso a funzionalità quali i WebSocket.</span><span class="sxs-lookup"><span data-stu-id="7ad03-140">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="7ad03-141">Il server Web OWIN per .NET usato nell'esempio precedente ha il supporto per i WebSocket incorporato che può essere usato da un'applicazione ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ad03-141">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="7ad03-142">L'esempio seguente illustra una semplice app Web che supporta i WebSocket e restituisce tutto quanto inviato al server tramite WebSocket.</span><span class="sxs-lookup"><span data-stu-id="7ad03-142">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

<span data-ttu-id="7ad03-143">L'[esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) viene configurato usando lo stesso `NowinServer` del precedente, l'unica differenza è la modalità con cui l'applicazione viene configurata nel relativo metodo `Configure`.</span><span class="sxs-lookup"><span data-stu-id="7ad03-143">This [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) is configured using the same `NowinServer` as the previous one - the only difference is in how the application is configured in its `Configure` method.</span></span> <span data-ttu-id="7ad03-144">Un test che usa [un semplice client WebSocket](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) illustra l'applicazione:</span><span class="sxs-lookup"><span data-stu-id="7ad03-144">A test using [a simple websocket client](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) demonstrates  the application:</span></span>

![Client di test WebSocket](owin/_static/websocket-test.png)

## <a name="owin-environment"></a><span data-ttu-id="7ad03-146">Ambiente OWIN</span><span class="sxs-lookup"><span data-stu-id="7ad03-146">OWIN environment</span></span>

<span data-ttu-id="7ad03-147">È possibile creare un ambiente OWIN tramite `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="7ad03-147">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="7ad03-148">Chiavi OWIN</span><span class="sxs-lookup"><span data-stu-id="7ad03-148">OWIN keys</span></span>

<span data-ttu-id="7ad03-149">OWIN dipende da un oggetto `IDictionary<string,object>` per comunicare informazioni attraverso uno scambio di richiesta/risposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="7ad03-149">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="7ad03-150">ASP.NET Core implementa le chiavi elencate di seguito.</span><span class="sxs-lookup"><span data-stu-id="7ad03-150">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="7ad03-151">Vedere le [specifiche principali e le estensioni](https://owin.org/#spec), nonché la pagina [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html) (Linee guida sulle chiavi OWIN e chiavi comuni).</span><span class="sxs-lookup"><span data-stu-id="7ad03-151">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="7ad03-152">Dati della richiesta (OWIN versione 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="7ad03-152">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="7ad03-153">Chiave</span><span class="sxs-lookup"><span data-stu-id="7ad03-153">Key</span></span>               | <span data-ttu-id="7ad03-154">Valore (tipo)</span><span class="sxs-lookup"><span data-stu-id="7ad03-154">Value (type)</span></span> | <span data-ttu-id="7ad03-155">Descrizione</span><span class="sxs-lookup"><span data-stu-id="7ad03-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="7ad03-156">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="7ad03-156">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="7ad03-157">owin.RequestMethod</span><span class="sxs-lookup"><span data-stu-id="7ad03-157">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="7ad03-158">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="7ad03-158">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="7ad03-159">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="7ad03-159">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="7ad03-160">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="7ad03-160">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="7ad03-161">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="7ad03-161">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="7ad03-162">owin.RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="7ad03-162">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="7ad03-163">owin.RequestBody</span><span class="sxs-lookup"><span data-stu-id="7ad03-163">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="7ad03-164">Dati della richiesta (OWIN versione 1.1.0)</span><span class="sxs-lookup"><span data-stu-id="7ad03-164">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="7ad03-165">Chiave</span><span class="sxs-lookup"><span data-stu-id="7ad03-165">Key</span></span>               | <span data-ttu-id="7ad03-166">Valore (tipo)</span><span class="sxs-lookup"><span data-stu-id="7ad03-166">Value (type)</span></span> | <span data-ttu-id="7ad03-167">Descrizione</span><span class="sxs-lookup"><span data-stu-id="7ad03-167">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="7ad03-168">owin.RequestId</span><span class="sxs-lookup"><span data-stu-id="7ad03-168">owin.RequestId</span></span> | `String` | <span data-ttu-id="7ad03-169">Facoltativo</span><span class="sxs-lookup"><span data-stu-id="7ad03-169">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="7ad03-170">Dati della risposta (OWIN versione 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="7ad03-170">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="7ad03-171">Chiave</span><span class="sxs-lookup"><span data-stu-id="7ad03-171">Key</span></span>               | <span data-ttu-id="7ad03-172">Valore (tipo)</span><span class="sxs-lookup"><span data-stu-id="7ad03-172">Value (type)</span></span> | <span data-ttu-id="7ad03-173">Descrizione</span><span class="sxs-lookup"><span data-stu-id="7ad03-173">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="7ad03-174">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="7ad03-174">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="7ad03-175">Facoltativo</span><span class="sxs-lookup"><span data-stu-id="7ad03-175">Optional</span></span> |
| <span data-ttu-id="7ad03-176">owin.ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="7ad03-176">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="7ad03-177">Facoltativo</span><span class="sxs-lookup"><span data-stu-id="7ad03-177">Optional</span></span> |
| <span data-ttu-id="7ad03-178">owin.ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="7ad03-178">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="7ad03-179">owin.ResponseBody</span><span class="sxs-lookup"><span data-stu-id="7ad03-179">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="7ad03-180">Altri dati (OWIN versione 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="7ad03-180">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="7ad03-181">Chiave</span><span class="sxs-lookup"><span data-stu-id="7ad03-181">Key</span></span>               | <span data-ttu-id="7ad03-182">Valore (tipo)</span><span class="sxs-lookup"><span data-stu-id="7ad03-182">Value (type)</span></span> | <span data-ttu-id="7ad03-183">Descrizione</span><span class="sxs-lookup"><span data-stu-id="7ad03-183">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="7ad03-184">owin.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="7ad03-184">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="7ad03-185">owin.Version</span><span class="sxs-lookup"><span data-stu-id="7ad03-185">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="7ad03-186">Chiavi comuni</span><span class="sxs-lookup"><span data-stu-id="7ad03-186">Common keys</span></span>

| <span data-ttu-id="7ad03-187">Chiave</span><span class="sxs-lookup"><span data-stu-id="7ad03-187">Key</span></span>               | <span data-ttu-id="7ad03-188">Valore (tipo)</span><span class="sxs-lookup"><span data-stu-id="7ad03-188">Value (type)</span></span> | <span data-ttu-id="7ad03-189">Descrizione</span><span class="sxs-lookup"><span data-stu-id="7ad03-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="7ad03-190">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="7ad03-190">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="7ad03-191">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="7ad03-191">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="7ad03-192">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="7ad03-192">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="7ad03-193">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="7ad03-193">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="7ad03-194">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="7ad03-194">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="7ad03-195">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="7ad03-195">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="7ad03-196">server.IsLocal</span><span class="sxs-lookup"><span data-stu-id="7ad03-196">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="7ad03-197">server.OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="7ad03-197">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="7ad03-198">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="7ad03-198">SendFiles v0.3.0</span></span>

| <span data-ttu-id="7ad03-199">Chiave</span><span class="sxs-lookup"><span data-stu-id="7ad03-199">Key</span></span>               | <span data-ttu-id="7ad03-200">Valore (tipo)</span><span class="sxs-lookup"><span data-stu-id="7ad03-200">Value (type)</span></span> | <span data-ttu-id="7ad03-201">Descrizione</span><span class="sxs-lookup"><span data-stu-id="7ad03-201">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="7ad03-202">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="7ad03-202">sendfile.SendAsync</span></span> | <span data-ttu-id="7ad03-203">Vedere [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Firma delegato)</span><span class="sxs-lookup"><span data-stu-id="7ad03-203">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="7ad03-204">Per richiesta</span><span class="sxs-lookup"><span data-stu-id="7ad03-204">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="7ad03-205">Opaque v0.3.0</span><span class="sxs-lookup"><span data-stu-id="7ad03-205">Opaque v0.3.0</span></span>

| <span data-ttu-id="7ad03-206">Chiave</span><span class="sxs-lookup"><span data-stu-id="7ad03-206">Key</span></span>               | <span data-ttu-id="7ad03-207">Valore (tipo)</span><span class="sxs-lookup"><span data-stu-id="7ad03-207">Value (type)</span></span> | <span data-ttu-id="7ad03-208">Descrizione</span><span class="sxs-lookup"><span data-stu-id="7ad03-208">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="7ad03-209">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="7ad03-209">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="7ad03-210">opaque.Upgrade</span><span class="sxs-lookup"><span data-stu-id="7ad03-210">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="7ad03-211">Vedere [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Firma delegato)</span><span class="sxs-lookup"><span data-stu-id="7ad03-211">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="7ad03-212">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="7ad03-212">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="7ad03-213">opaque.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="7ad03-213">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="7ad03-214">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="7ad03-214">WebSocket v0.3.0</span></span>

| <span data-ttu-id="7ad03-215">Chiave</span><span class="sxs-lookup"><span data-stu-id="7ad03-215">Key</span></span>               | <span data-ttu-id="7ad03-216">Valore (tipo)</span><span class="sxs-lookup"><span data-stu-id="7ad03-216">Value (type)</span></span> | <span data-ttu-id="7ad03-217">Descrizione</span><span class="sxs-lookup"><span data-stu-id="7ad03-217">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="7ad03-218">websocket.Version</span><span class="sxs-lookup"><span data-stu-id="7ad03-218">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="7ad03-219">websocket.Accept</span><span class="sxs-lookup"><span data-stu-id="7ad03-219">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="7ad03-220">Vedere [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Firma delegato)</span><span class="sxs-lookup"><span data-stu-id="7ad03-220">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="7ad03-221">websocket.AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="7ad03-221">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="7ad03-222">Non specificata</span><span class="sxs-lookup"><span data-stu-id="7ad03-222">Non-spec</span></span> |
| <span data-ttu-id="7ad03-223">websocket.SubProtocol</span><span class="sxs-lookup"><span data-stu-id="7ad03-223">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="7ad03-224">Vedere [RFC6455 sezione 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) passaggio 5.5</span><span class="sxs-lookup"><span data-stu-id="7ad03-224">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="7ad03-225">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="7ad03-225">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="7ad03-226">Vedere [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Firma delegato)</span><span class="sxs-lookup"><span data-stu-id="7ad03-226">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="7ad03-227">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="7ad03-227">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="7ad03-228">Vedere [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Firma delegato)</span><span class="sxs-lookup"><span data-stu-id="7ad03-228">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="7ad03-229">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="7ad03-229">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="7ad03-230">Vedere [Delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) (Firma delegato)</span><span class="sxs-lookup"><span data-stu-id="7ad03-230">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="7ad03-231">websocket.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="7ad03-231">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="7ad03-232">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="7ad03-232">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="7ad03-233">Facoltativo</span><span class="sxs-lookup"><span data-stu-id="7ad03-233">Optional</span></span> |
| <span data-ttu-id="7ad03-234">websocket.ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="7ad03-234">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="7ad03-235">Facoltativo</span><span class="sxs-lookup"><span data-stu-id="7ad03-235">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="7ad03-236">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="7ad03-236">Additional resources</span></span>

* [<span data-ttu-id="7ad03-237">Middleware</span><span class="sxs-lookup"><span data-stu-id="7ad03-237">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="7ad03-238">Server</span><span class="sxs-lookup"><span data-stu-id="7ad03-238">Servers</span></span>](xref:fundamentals/servers/index)
