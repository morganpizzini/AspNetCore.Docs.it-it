---
title: Test del middleware ASP.NET Core
author: tratcher
description: Informazioni su come testare ASP.NET Core middleware con TestServer.
ms.author: riande
ms.custom: mvc
ms.date: 5/6/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/middleware
ms.openlocfilehash: 06ff7167e32fbd613c18709e31ecd078b3dfc926
ms.sourcegitcommit: 30fcf69556b6b6ec54a3879e280d5f61f018b48f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82876437"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="25c7e-103">Test del middleware ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="25c7e-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="25c7e-104">Di [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="25c7e-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="25c7e-105">Il middleware può essere testato in isolamento <xref:Microsoft.AspNetCore.TestHost.TestServer>con.</span><span class="sxs-lookup"><span data-stu-id="25c7e-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="25c7e-106">Consente di:</span><span class="sxs-lookup"><span data-stu-id="25c7e-106">It allows you to:</span></span>

* <span data-ttu-id="25c7e-107">Creare un'istanza di una pipeline dell'app contenente solo i componenti che è necessario testare.</span><span class="sxs-lookup"><span data-stu-id="25c7e-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="25c7e-108">Inviare richieste personalizzate per verificare il comportamento del middleware.</span><span class="sxs-lookup"><span data-stu-id="25c7e-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="25c7e-109">Vantaggi:</span><span class="sxs-lookup"><span data-stu-id="25c7e-109">Advantages:</span></span>

* <span data-ttu-id="25c7e-110">Le richieste vengono inviate in memoria anziché essere serializzate sulla rete.</span><span class="sxs-lookup"><span data-stu-id="25c7e-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="25c7e-111">In questo modo si evitano ulteriori problemi, ad esempio la gestione delle porte e i certificati HTTPS.</span><span class="sxs-lookup"><span data-stu-id="25c7e-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="25c7e-112">Le eccezioni nel middleware possono fluire direttamente sul test chiamante.</span><span class="sxs-lookup"><span data-stu-id="25c7e-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="25c7e-113">È possibile personalizzare le strutture dei dati del server, ad <xref:Microsoft.AspNetCore.Http.HttpContext>esempio, direttamente nel test.</span><span class="sxs-lookup"><span data-stu-id="25c7e-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="25c7e-114">Configurare TestServer</span><span class="sxs-lookup"><span data-stu-id="25c7e-114">Set up the TestServer</span></span>

<span data-ttu-id="25c7e-115">Nel progetto di test creare un test:</span><span class="sxs-lookup"><span data-stu-id="25c7e-115">In the test project, create a test:</span></span>

* <span data-ttu-id="25c7e-116">Compilare e avviare un host che usa <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span><span class="sxs-lookup"><span data-stu-id="25c7e-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="25c7e-117">Aggiungere tutti i servizi necessari usati dal middleware.</span><span class="sxs-lookup"><span data-stu-id="25c7e-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="25c7e-118">Configurare la pipeline di elaborazione per l'uso del middleware per il test.</span><span class="sxs-lookup"><span data-stu-id="25c7e-118">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="25c7e-119">Inviare richieste con HttpClient</span><span class="sxs-lookup"><span data-stu-id="25c7e-119">Send requests with HttpClient</span></span>
<span data-ttu-id="25c7e-120">Invia una richiesta tramite <xref:System.Net.Http.HttpClient>:</span><span class="sxs-lookup"><span data-stu-id="25c7e-120">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="25c7e-121">Asserire il risultato.</span><span class="sxs-lookup"><span data-stu-id="25c7e-121">Assert the result.</span></span> <span data-ttu-id="25c7e-122">Prima di tutto, creare un'asserzione anziché il risultato previsto.</span><span class="sxs-lookup"><span data-stu-id="25c7e-122">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="25c7e-123">Un'esecuzione iniziale con un'asserzione falsa positiva conferma che il test ha esito negativo quando il middleware viene eseguito correttamente.</span><span class="sxs-lookup"><span data-stu-id="25c7e-123">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="25c7e-124">Eseguire il test e verificare che il test abbia esito negativo.</span><span class="sxs-lookup"><span data-stu-id="25c7e-124">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="25c7e-125">Nell'esempio seguente il middleware deve restituire un codice di stato 404 (*non trovato*) quando viene richiesto l'endpoint radice.</span><span class="sxs-lookup"><span data-stu-id="25c7e-125">In the following example, the middleware should return a 404 status code (*Not Found*) when the root endpoint is requested.</span></span> <span data-ttu-id="25c7e-126">Eseguire la prima esecuzione dei test `Assert.NotEqual( ... );`con, che dovrebbe avere esito negativo:</span><span class="sxs-lookup"><span data-stu-id="25c7e-126">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="25c7e-127">Modificare l'asserzione per testare il middleware in condizioni operative normali.</span><span class="sxs-lookup"><span data-stu-id="25c7e-127">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="25c7e-128">Il test finale utilizza `Assert.Equal( ... );`.</span><span class="sxs-lookup"><span data-stu-id="25c7e-128">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="25c7e-129">Eseguire di nuovo il test per verificare che venga superato.</span><span class="sxs-lookup"><span data-stu-id="25c7e-129">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="25c7e-130">Inviare richieste con HttpContext</span><span class="sxs-lookup"><span data-stu-id="25c7e-130">Send requests with HttpContext</span></span>

<span data-ttu-id="25c7e-131">Un'app di test può anche inviare una richiesta usando [SendAsync (\<Action HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span><span class="sxs-lookup"><span data-stu-id="25c7e-131">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="25c7e-132">Nell'esempio seguente vengono eseguiti diversi controlli quando `https://example.com/A/Path/?and=query` viene elaborato dal middleware:</span><span class="sxs-lookup"><span data-stu-id="25c7e-132">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

```csharp
[Fact]
public async Task TestMiddleware_ExpectedResponse()
{
    using var host = await new HostBuilder()
        .ConfigureWebHost(webBuilder =>
        {
            webBuilder
                .UseTestServer()
                .ConfigureServices(services =>
                {
                    services.AddMyServices();
                })
                .Configure(app =>
                {
                    app.UseMiddleware<MyMiddleware>();
                });
        })
        .StartAsync();

    var server = host.GetTestServer();
    server.BaseAddress = new Uri("https://example.com/A/Path/");

    var context = await server.SendAsync(c =>
    {
        c.Request.Method = HttpMethods.Post;
        c.Request.Path = "/and/file.txt";
        c.Request.QueryString = new QueryString("?and=query");
    });

    Assert.True(context.RequestAborted.CanBeCanceled);
    Assert.Equal(HttpProtocol.Http11, context.Request.Protocol);
    Assert.Equal("POST", context.Request.Method);
    Assert.Equal("https", context.Request.Scheme);
    Assert.Equal("example.com", context.Request.Host.Value);
    Assert.Equal("/A/Path", context.Request.PathBase.Value);
    Assert.Equal("/and/file.txt", context.Request.Path.Value);
    Assert.Equal("?and=query", context.Request.QueryString.Value);
    Assert.NotNull(context.Request.Body);
    Assert.NotNull(context.Request.Headers);
    Assert.NotNull(context.Response.Headers);
    Assert.NotNull(context.Response.Body);
    Assert.Equal(404, context.Response.StatusCode);
    Assert.Null(context.Features.Get<IHttpResponseFeature>().ReasonPhrase);
}
```

<span data-ttu-id="25c7e-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>consente la configurazione diretta di <xref:Microsoft.AspNetCore.Http.HttpContext> un oggetto anziché l'utilizzo <xref:System.Net.Http.HttpClient> delle astrazioni.</span><span class="sxs-lookup"><span data-stu-id="25c7e-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="25c7e-134">Usare <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> per modificare le strutture disponibili solo sul server, ad esempio [HttpContext. Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) o [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span><span class="sxs-lookup"><span data-stu-id="25c7e-134">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="25c7e-135">Come per l'esempio precedente che ha testato una risposta *404 non trovata* , controllare l'opposto per ogni `Assert` istruzione del test precedente.</span><span class="sxs-lookup"><span data-stu-id="25c7e-135">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="25c7e-136">Il controllo conferma che il test ha esito negativo correttamente quando il middleware funziona normalmente.</span><span class="sxs-lookup"><span data-stu-id="25c7e-136">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="25c7e-137">Dopo aver verificato il funzionamento del test falso positivo, impostare le istruzioni finali `Assert` per le condizioni e i valori previsti per il test.</span><span class="sxs-lookup"><span data-stu-id="25c7e-137">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="25c7e-138">Eseguirlo di nuovo per verificare che il test venga superato.</span><span class="sxs-lookup"><span data-stu-id="25c7e-138">Run it again to confirm that the test passes.</span></span>
