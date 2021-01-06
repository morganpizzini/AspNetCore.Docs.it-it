---
title: Test del middleware ASP.NET Core
author: tratcher
description: Informazioni su come testare ASP.NET Core middleware con TestServer.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
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
uid: test/middleware
ms.openlocfilehash: 2dd5fa127af4432c612bb654d50eb4147aea6868
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "93051434"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="9b826-103">Test del middleware ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b826-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="9b826-104">Di [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="9b826-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="9b826-105">Il middleware può essere testato in isolamento con <xref:Microsoft.AspNetCore.TestHost.TestServer> .</span><span class="sxs-lookup"><span data-stu-id="9b826-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="9b826-106">Consente di:</span><span class="sxs-lookup"><span data-stu-id="9b826-106">It allows you to:</span></span>

* <span data-ttu-id="9b826-107">Creare un'istanza di una pipeline dell'app contenente solo i componenti che è necessario testare.</span><span class="sxs-lookup"><span data-stu-id="9b826-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="9b826-108">Inviare richieste personalizzate per verificare il comportamento del middleware.</span><span class="sxs-lookup"><span data-stu-id="9b826-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="9b826-109">Vantaggi:</span><span class="sxs-lookup"><span data-stu-id="9b826-109">Advantages:</span></span>

* <span data-ttu-id="9b826-110">Le richieste vengono inviate in memoria anziché essere serializzate sulla rete.</span><span class="sxs-lookup"><span data-stu-id="9b826-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="9b826-111">In questo modo si evitano ulteriori problemi, ad esempio la gestione delle porte e i certificati HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9b826-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="9b826-112">Le eccezioni nel middleware possono fluire direttamente sul test chiamante.</span><span class="sxs-lookup"><span data-stu-id="9b826-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="9b826-113">È possibile personalizzare le strutture dei dati del server, ad esempio <xref:Microsoft.AspNetCore.Http.HttpContext> , direttamente nel test.</span><span class="sxs-lookup"><span data-stu-id="9b826-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="9b826-114">Configurare TestServer</span><span class="sxs-lookup"><span data-stu-id="9b826-114">Set up the TestServer</span></span>

<span data-ttu-id="9b826-115">Nel progetto di test creare un test:</span><span class="sxs-lookup"><span data-stu-id="9b826-115">In the test project, create a test:</span></span>

* <span data-ttu-id="9b826-116">Compilare e avviare un host che usa <xref:Microsoft.AspNetCore.TestHost.TestServer> .</span><span class="sxs-lookup"><span data-stu-id="9b826-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="9b826-117">Aggiungere tutti i servizi necessari usati dal middleware.</span><span class="sxs-lookup"><span data-stu-id="9b826-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="9b826-118">Aggiungere il pacchetto NuGet [Microsoft. AspNetCore. TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) al progetto:</span><span class="sxs-lookup"><span data-stu-id="9b826-118">Add the [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) NuGet package to the project:</span></span>
  
  ```dotnetcli
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.TestHost" Version="3.1.*" />
  </ItemGroup>
  ```

* <span data-ttu-id="9b826-119">Configurare la pipeline di elaborazione per l'uso del middleware per il test.</span><span class="sxs-lookup"><span data-stu-id="9b826-119">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="9b826-120">Inviare richieste con HttpClient</span><span class="sxs-lookup"><span data-stu-id="9b826-120">Send requests with HttpClient</span></span>

<span data-ttu-id="9b826-121">Invia una richiesta tramite <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="9b826-121">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="9b826-122">Asserire il risultato.</span><span class="sxs-lookup"><span data-stu-id="9b826-122">Assert the result.</span></span> <span data-ttu-id="9b826-123">Prima di tutto, creare un'asserzione anziché il risultato previsto.</span><span class="sxs-lookup"><span data-stu-id="9b826-123">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="9b826-124">Un'esecuzione iniziale con un'asserzione falsa positiva conferma che il test ha esito negativo quando il middleware viene eseguito correttamente.</span><span class="sxs-lookup"><span data-stu-id="9b826-124">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="9b826-125">Eseguire il test e verificare che il test abbia esito negativo.</span><span class="sxs-lookup"><span data-stu-id="9b826-125">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="9b826-126">Nell'esempio seguente il middleware deve restituire un codice di stato 404 (*non trovato*) quando viene richiesto l'endpoint radice.</span><span class="sxs-lookup"><span data-stu-id="9b826-126">In the following example, the middleware should return a 404 status code (*Not Found*) when the root endpoint is requested.</span></span> <span data-ttu-id="9b826-127">Eseguire la prima esecuzione dei test con `Assert.NotEqual( ... );` , che dovrebbe avere esito negativo:</span><span class="sxs-lookup"><span data-stu-id="9b826-127">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="9b826-128">Modificare l'asserzione per testare il middleware in condizioni operative normali.</span><span class="sxs-lookup"><span data-stu-id="9b826-128">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="9b826-129">Il test finale utilizza `Assert.Equal( ... );` .</span><span class="sxs-lookup"><span data-stu-id="9b826-129">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="9b826-130">Eseguire di nuovo il test per verificare che venga superato.</span><span class="sxs-lookup"><span data-stu-id="9b826-130">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="9b826-131">Inviare richieste con HttpContext</span><span class="sxs-lookup"><span data-stu-id="9b826-131">Send requests with HttpContext</span></span>

<span data-ttu-id="9b826-132">Un'app di test può anche inviare una richiesta tramite [SendAsync (Action \<HttpContext> , CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span><span class="sxs-lookup"><span data-stu-id="9b826-132">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="9b826-133">Nell'esempio seguente vengono eseguiti diversi controlli quando `https://example.com/A/Path/?and=query` viene elaborato dal middleware:</span><span class="sxs-lookup"><span data-stu-id="9b826-133">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

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

<span data-ttu-id="9b826-134"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> consente la configurazione diretta di un <xref:Microsoft.AspNetCore.Http.HttpContext> oggetto anziché l'utilizzo delle <xref:System.Net.Http.HttpClient> astrazioni.</span><span class="sxs-lookup"><span data-stu-id="9b826-134"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="9b826-135">Usare <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> per modificare le strutture disponibili solo sul server, ad esempio [HttpContext. Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) o [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span><span class="sxs-lookup"><span data-stu-id="9b826-135">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="9b826-136">Come per l'esempio precedente che ha testato una risposta *404 non trovata* , controllare l'opposto per ogni `Assert` istruzione del test precedente.</span><span class="sxs-lookup"><span data-stu-id="9b826-136">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="9b826-137">Il controllo conferma che il test ha esito negativo correttamente quando il middleware funziona normalmente.</span><span class="sxs-lookup"><span data-stu-id="9b826-137">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="9b826-138">Dopo aver verificato il funzionamento del test falso positivo, impostare le istruzioni finali `Assert` per le condizioni e i valori previsti per il test.</span><span class="sxs-lookup"><span data-stu-id="9b826-138">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="9b826-139">Eseguirlo di nuovo per verificare che il test venga superato.</span><span class="sxs-lookup"><span data-stu-id="9b826-139">Run it again to confirm that the test passes.</span></span>

## <a name="testserver-limitations"></a><span data-ttu-id="9b826-140">Limitazioni di TestServer</span><span class="sxs-lookup"><span data-stu-id="9b826-140">TestServer limitations</span></span>

<span data-ttu-id="9b826-141">TestServer</span><span class="sxs-lookup"><span data-stu-id="9b826-141">TestServer:</span></span>

* <span data-ttu-id="9b826-142">È stato creato per replicare i comportamenti del server nel middleware di test.</span><span class="sxs-lookup"><span data-stu-id="9b826-142">Was created to replicate server behaviors to test middleware.</span></span>
* <span data-ttu-id="9b826-143">Does \***not** _ provare a replicare tutti i <xref:System.Net.Http.HttpClient> comportamenti.</span><span class="sxs-lookup"><span data-stu-id="9b826-143">Does \***not** _ try to replicate all <xref:System.Net.Http.HttpClient> behaviors.</span></span>
<span data-ttu-id="9b826-144">_ Tenta di concedere al client l'accesso a un maggior controllo sul server e con la massima visibilità su ciò che accade nel server.</span><span class="sxs-lookup"><span data-stu-id="9b826-144">_ Attempts to give the client access to as much control over the server as possible, and with as much visibility into what's happening on the server as possible.</span></span> <span data-ttu-id="9b826-145">Ad esempio, può generare eccezioni non generate normalmente da `HttpClient` per comunicare direttamente lo stato del server.</span><span class="sxs-lookup"><span data-stu-id="9b826-145">For example it may throw exceptions not normally thrown by `HttpClient` in order to directly communicate server state.</span></span>
* <span data-ttu-id="9b826-146">Per impostazione predefinita, alcune intestazioni specifiche del trasporto non vengono impostate perché non sono in genere rilevanti per il middleware.</span><span class="sxs-lookup"><span data-stu-id="9b826-146">Doesn't set some transport specific headers by default as those are not usually relevant to middleware.</span></span> <span data-ttu-id="9b826-147">Per ulteriori informazioni, vedere la sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="9b826-147">For more information, see the next section.</span></span>

### <a name="content-length-and-transfer-encoding-headers"></a><span data-ttu-id="9b826-148">Intestazioni Content-Length e Transfer-Encoding</span><span class="sxs-lookup"><span data-stu-id="9b826-148">Content-Length and Transfer-Encoding headers</span></span>

<span data-ttu-id="9b826-149">TestServer esegue \***not** _ imposta le intestazioni di richiesta o risposta relative al trasporto, ad esempio la [lunghezza del contenuto](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) o la [codifica di trasferimento](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding).</span><span class="sxs-lookup"><span data-stu-id="9b826-149">TestServer does \***not** _ set transport related request or response headers such as [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) or [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding).</span></span> <span data-ttu-id="9b826-150">Le applicazioni devono evitare a seconda di queste intestazioni perché l'utilizzo varia in base a client, scenario e protocollo.</span><span class="sxs-lookup"><span data-stu-id="9b826-150">Applications should avoid depending on these headers because their usage varies by client, scenario, and protocol.</span></span> <span data-ttu-id="9b826-151">Se `Content-Length` e `Transfer-Encoding` sono necessari per testare uno scenario specifico, è possibile specificarli nel test quando si compone <xref:System.Net.Http.HttpRequestMessage> o <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="9b826-151">If `Content-Length` and `Transfer-Encoding` are necessary to test a specific scenario, they can be specified in the test when composing the <xref:System.Net.Http.HttpRequestMessage> or <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span> <span data-ttu-id="9b826-152">Per ulteriori informazioni, vedere i seguenti problemi di GitHub:</span><span class="sxs-lookup"><span data-stu-id="9b826-152">For more information, see the following GitHub issues:</span></span>

<span data-ttu-id="9b826-153">_ [DotNet/aspnetcore # 21677](https://github.com/dotnet/aspnetcore/issues/21677)</span><span class="sxs-lookup"><span data-stu-id="9b826-153">_ [dotnet/aspnetcore#21677](https://github.com/dotnet/aspnetcore/issues/21677)</span></span>
* [<span data-ttu-id="9b826-154">DotNet/aspnetcore # 18463</span><span class="sxs-lookup"><span data-stu-id="9b826-154">dotnet/aspnetcore#18463</span></span>](https://github.com/dotnet/aspnetcore/issues/18463)
* [<span data-ttu-id="9b826-155">DotNet/aspnetcore # 13273</span><span class="sxs-lookup"><span data-stu-id="9b826-155">dotnet/aspnetcore#13273</span></span>](https://github.com/dotnet/aspnetcore/issues/13273)
