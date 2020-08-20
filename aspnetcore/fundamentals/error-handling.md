---
title: Gestire gli errori in ASP.NET Core
author: rick-anderson
description: Informazioni su come gestire gli errori nelle app ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: a1f40bdcdd4f2472aa86b311bfd9302e6aa8adc0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635099"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="d3053-103">Gestire gli errori in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d3053-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="d3053-104">Di [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="d3053-104">By [Tom Dykstra](https://github.com/tdykstra/) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d3053-105">Questo articolo illustra gli approcci comuni per la gestione degli errori nelle app Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d3053-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="d3053-106">Vedere <xref:web-api/handle-errors> per le API Web.</span><span class="sxs-lookup"><span data-stu-id="d3053-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="d3053-107">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="d3053-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="d3053-108">([Come scaricare](xref:index#how-to-download-a-sample)). L'articolo include istruzioni su come impostare le direttive per il preprocessore ( `#if` , `#endif` , `#define` ) nell'app di esempio per abilitare diversi scenari.</span><span class="sxs-lookup"><span data-stu-id="d3053-108">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="d3053-109">Pagina delle eccezioni per gli sviluppatori</span><span class="sxs-lookup"><span data-stu-id="d3053-109">Developer Exception Page</span></span>

<span data-ttu-id="d3053-110">In *Pagina delle eccezioni per gli sviluppatori* sono disponibili informazioni dettagliate sulle eccezioni delle richieste.</span><span class="sxs-lookup"><span data-stu-id="d3053-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="d3053-111">La pagina viene resa disponibile dall' `Microsoft.AspNetCore.Diagnostics` assembly, che si trova nel [ `Microsoft.AspNetCore.App` Framework condiviso](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="d3053-111">The page is made available by the `Microsoft.AspNetCore.Diagnostics` assembly, which is in the [`Microsoft.AspNetCore.App` shared framework](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="d3053-112">Aggiungere codice al metodo `Startup.Configure` per abilitare la pagina quando l'app è in esecuzione nell'[ambiente](xref:fundamentals/environments) di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="d3053-112">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="d3053-113">Posizionare la chiamata a <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> prima di qualsiasi middleware in cui si vogliono rilevare le eccezioni.</span><span class="sxs-lookup"><span data-stu-id="d3053-113">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="d3053-114">Abilitare la pagina delle eccezioni **per gli sviluppatori solo quando l'app è in esecuzione nell'ambiente di sviluppo**.</span><span class="sxs-lookup"><span data-stu-id="d3053-114">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="d3053-115">per non condividere pubblicamente le informazioni dettagliate sulle eccezioni quando l'app è in esecuzione nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="d3053-115">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="d3053-116">Per altre informazioni sulla configurazione di ambienti, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d3053-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d3053-117">La pagina include le informazioni seguenti sull'eccezione e sulla richiesta:</span><span class="sxs-lookup"><span data-stu-id="d3053-117">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="d3053-118">Analisi dello stack</span><span class="sxs-lookup"><span data-stu-id="d3053-118">Stack trace</span></span>
* <span data-ttu-id="d3053-119">Parametri della stringa di query (se presenti)</span><span class="sxs-lookup"><span data-stu-id="d3053-119">Query string parameters (if any)</span></span>
* <span data-ttu-id="d3053-120">Cookies (se presente)</span><span class="sxs-lookup"><span data-stu-id="d3053-120">Cookies (if any)</span></span>
* <span data-ttu-id="d3053-121">Intestazioni</span><span class="sxs-lookup"><span data-stu-id="d3053-121">Headers</span></span>

<span data-ttu-id="d3053-122">Per visualizzare la pagina delle eccezioni per gli sviluppatori nell'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), usare la direttiva del preprocessore `DevEnvironment` e selezionare **Trigger an exception** (Attiva un'eccezione) nella home page.</span><span class="sxs-lookup"><span data-stu-id="d3053-122">To see the Developer Exception Page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="d3053-123">Pagina del gestore di eccezioni</span><span class="sxs-lookup"><span data-stu-id="d3053-123">Exception handler page</span></span>

<span data-ttu-id="d3053-124">Per configurare una pagina di gestione degli errori personalizzata per l'ambiente di produzione, usare il middleware di gestione delle eccezioni.</span><span class="sxs-lookup"><span data-stu-id="d3053-124">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="d3053-125">Il middleware:</span><span class="sxs-lookup"><span data-stu-id="d3053-125">The middleware:</span></span>

* <span data-ttu-id="d3053-126">Intercetta e registra le eccezioni.</span><span class="sxs-lookup"><span data-stu-id="d3053-126">Catches and logs exceptions.</span></span>
* <span data-ttu-id="d3053-127">Esegue nuovamente la richiesta in una pipeline alternativa per la pagina o il controller indicati.</span><span class="sxs-lookup"><span data-stu-id="d3053-127">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="d3053-128">La richiesta non viene eseguita nuovamente se la risposta è stata avviata.</span><span class="sxs-lookup"><span data-stu-id="d3053-128">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="d3053-129">Nell'esempio seguente <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> aggiunge il middleware di gestione delle eccezioni in ambienti non di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="d3053-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="d3053-130">Il Razor modello di app pagine fornisce una pagina di errore (*cshtml*) e una <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> classe ( `ErrorModel` ) nella cartella *pagine* .</span><span class="sxs-lookup"><span data-stu-id="d3053-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="d3053-131">Per un'app MVC, il modello di progetto include un metodo di azione Error e una visualizzazione degli errori.</span><span class="sxs-lookup"><span data-stu-id="d3053-131">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="d3053-132">Ecco il metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="d3053-132">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="d3053-133">Non contrassegnare il metodo di azione del gestore errori con attributi di metodo HTTP, ad esempio `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="d3053-133">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="d3053-134">I verbi espliciti impediscono ad alcune richieste di raggiungere il metodo.</span><span class="sxs-lookup"><span data-stu-id="d3053-134">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="d3053-135">Consentire l'accesso anonimo al metodo in modo che gli utenti non autenticati possano ricevere la visualizzazione degli errori.</span><span class="sxs-lookup"><span data-stu-id="d3053-135">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="d3053-136">Accedere all'eccezione</span><span class="sxs-lookup"><span data-stu-id="d3053-136">Access the exception</span></span>

<span data-ttu-id="d3053-137">Usare <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> per accedere all'eccezione e al percorso della richiesta originale in un controller o in una pagina del gestore degli errori:</span><span class="sxs-lookup"><span data-stu-id="d3053-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="d3053-138">**Non** fornire informazioni sugli errori sensibili ai client.</span><span class="sxs-lookup"><span data-stu-id="d3053-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="d3053-139">Fornire informazioni sugli errori rappresenta un rischio di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="d3053-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="d3053-140">Per visualizzare la pagina di gestione delle eccezioni nell'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), usare le direttive del preprocessore `ProdEnvironment` e `ErrorHandlerPage` e selezionare **Trigger an exception** (Attiva un'eccezione) nella home page.</span><span class="sxs-lookup"><span data-stu-id="d3053-140">To see the exception handling page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="d3053-141">Espressione lambda per il gestore di eccezioni</span><span class="sxs-lookup"><span data-stu-id="d3053-141">Exception handler lambda</span></span>

<span data-ttu-id="d3053-142">Un'alternativa a una [pagina di gestione delle eccezioni personalizzata](#exception-handler-page) consiste nel fornire un'espressione lambda a <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="d3053-142">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="d3053-143">L'uso di un'espressione lambda consente l'accesso all'errore prima di restituire la risposta.</span><span class="sxs-lookup"><span data-stu-id="d3053-143">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="d3053-144">Di seguito è riportato un esempio dell'uso di un'espressione lambda per la gestione delle eccezioni:</span><span class="sxs-lookup"><span data-stu-id="d3053-144">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="d3053-145">Nel codice precedente `await context.Response.WriteAsync(new string(' ', 512));` viene aggiunto, in modo che il browser Internet Explorer visualizzi il messaggio di errore anziché un messaggio di errore di IE.</span><span class="sxs-lookup"><span data-stu-id="d3053-145">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="d3053-146">Per altre informazioni, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="d3053-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="d3053-147">**Non** fornire informazioni sugli errori sensibili da <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> o <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> ai client.</span><span class="sxs-lookup"><span data-stu-id="d3053-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="d3053-148">Fornire informazioni sugli errori rappresenta un rischio di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="d3053-148">Serving errors is a security risk.</span></span>

<span data-ttu-id="d3053-149">Per visualizzare il risultato dell'espressione lambda di gestione delle eccezioni nell'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), usare le direttive del preprocessore `ProdEnvironment` e `ErrorHandlerLambda` e selezionare **Trigger an exception** (Attiva un'eccezione) nella home page.</span><span class="sxs-lookup"><span data-stu-id="d3053-149">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="d3053-150">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="d3053-150">UseStatusCodePages</span></span>

<span data-ttu-id="d3053-151">Per impostazione predefinita, un'app ASP.NET Core non offre una tabella codici di stato per i codici di stato HTTP, ad esempio *404 - Non trovato*.</span><span class="sxs-lookup"><span data-stu-id="d3053-151">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="d3053-152">L'app restituisce un codice di stato e un corpo della risposta vuoto.</span><span class="sxs-lookup"><span data-stu-id="d3053-152">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="d3053-153">Per specificare le tabelle codici di stato, usare il middleware delle tabelle codici di stato.</span><span class="sxs-lookup"><span data-stu-id="d3053-153">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="d3053-154">Il middleware è disponibile nel pacchetto [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) che si trova nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="d3053-154">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="d3053-155">Per abilitare i gestori di solo testo predefiniti per i codici di stato di errore comuni, chiamare <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> nel metodo `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d3053-155">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="d3053-156">Chiamare `UseStatusCodePages` prima del middleware di gestione delle richieste (ad esempio, middleware dei file statici e middleware MVC).</span><span class="sxs-lookup"><span data-stu-id="d3053-156">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="d3053-157">Di seguito è riportato un esempio del testo visualizzato dai gestori predefiniti:</span><span class="sxs-lookup"><span data-stu-id="d3053-157">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="d3053-158">Per visualizzare uno dei vari formati di tabella codici di stato nell'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), usare una delle direttive del preprocessore che iniziano con `StatusCodePages` e selezionare **Trigger a 404** (Genera un 404) nella home page.</span><span class="sxs-lookup"><span data-stu-id="d3053-158">To see one of the various status code page formats in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="d3053-159">UseStatusCodePages con stringa di formato</span><span class="sxs-lookup"><span data-stu-id="d3053-159">UseStatusCodePages with format string</span></span>

<span data-ttu-id="d3053-160">Per personalizzare il tipo di contenuto della risposta e il testo, usare l'overload di <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> che accetta un tipo di contenuto e una stringa di formato:</span><span class="sxs-lookup"><span data-stu-id="d3053-160">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="d3053-161">UseStatusCodePages con espressione lambda</span><span class="sxs-lookup"><span data-stu-id="d3053-161">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="d3053-162">Per specificare la gestione degli errori personalizzata e il codice per la scrittura delle risposte, usare l'overload di <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> che accetta un'espressione lambda:</span><span class="sxs-lookup"><span data-stu-id="d3053-162">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="d3053-163">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="d3053-163">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="d3053-164">Metodo di estensione <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:</span><span class="sxs-lookup"><span data-stu-id="d3053-164">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="d3053-165">Invia un codice di stato *302 - Trovato* al client.</span><span class="sxs-lookup"><span data-stu-id="d3053-165">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="d3053-166">Reindirizza il client al percorso specificato nel modello di URL.</span><span class="sxs-lookup"><span data-stu-id="d3053-166">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="d3053-167">Il modello di URL può includere un segnaposto `{0}` per il codice di stato, come illustrato nell'esempio.</span><span class="sxs-lookup"><span data-stu-id="d3053-167">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="d3053-168">Se il modello di URL inizia con una tilde (~), la tilde viene sostituita con il valore `PathBase` dell'app.</span><span class="sxs-lookup"><span data-stu-id="d3053-168">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="d3053-169">Se si punta a un endpoint all'interno dell'app, creare una visualizzazione o una Razor pagina MVC per l'endpoint.</span><span class="sxs-lookup"><span data-stu-id="d3053-169">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="d3053-170">Per un Razor esempio di pagine, vedere *pages/StatusCode. cshtml* nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="d3053-170">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="d3053-171">Questo metodo viene usato comunemente quando l'app:</span><span class="sxs-lookup"><span data-stu-id="d3053-171">This method is commonly used when the app:</span></span>

* <span data-ttu-id="d3053-172">Deve reindirizzare il client a un endpoint diverso, in genere nei casi in cui un'altra app elabora l'errore.</span><span class="sxs-lookup"><span data-stu-id="d3053-172">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="d3053-173">Per le app Web, la barra degli indirizzi del browser del client riflette l'endpoint reindirizzato.</span><span class="sxs-lookup"><span data-stu-id="d3053-173">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="d3053-174">Non deve conservare e restituire il codice di stato originale con la risposta di reindirizzamento iniziale.</span><span class="sxs-lookup"><span data-stu-id="d3053-174">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="d3053-175">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="d3053-175">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="d3053-176">Metodo di estensione <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:</span><span class="sxs-lookup"><span data-stu-id="d3053-176">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="d3053-177">Restituisce il codice di stato originale al client.</span><span class="sxs-lookup"><span data-stu-id="d3053-177">Returns the original status code to the client.</span></span>
* <span data-ttu-id="d3053-178">Genera il corpo della risposta eseguendo nuovamente la pipeline delle richieste con un percorso alternativo.</span><span class="sxs-lookup"><span data-stu-id="d3053-178">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="d3053-179">Se si punta a un endpoint all'interno dell'app, creare una visualizzazione o una Razor pagina MVC per l'endpoint.</span><span class="sxs-lookup"><span data-stu-id="d3053-179">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="d3053-180">Assicurarsi `UseStatusCodePagesWithReExecute` che venga inserito prima `UseRouting` di in modo che sia possibile reindirizzare la richiesta alla pagina stato.</span><span class="sxs-lookup"><span data-stu-id="d3053-180">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="d3053-181">Per un Razor esempio di pagine, vedere *pages/StatusCode. cshtml* nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="d3053-181">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="d3053-182">Questo metodo viene usato comunemente quando l'app deve:</span><span class="sxs-lookup"><span data-stu-id="d3053-182">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="d3053-183">Elaborare la richiesta senza il reindirizzamento a un endpoint diverso.</span><span class="sxs-lookup"><span data-stu-id="d3053-183">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="d3053-184">Per le app Web, la barra degli indirizzi del browser del client riflette l'endpoint richiesto in origine.</span><span class="sxs-lookup"><span data-stu-id="d3053-184">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="d3053-185">Conservare e restituire il codice di stato originale con la risposta.</span><span class="sxs-lookup"><span data-stu-id="d3053-185">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="d3053-186">I modelli di URL e di stringa di query potrebbero includere un segnaposto (`{0}`) per il codice di stato.</span><span class="sxs-lookup"><span data-stu-id="d3053-186">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="d3053-187">Il modello di URL deve iniziare con una barra (`/`).</span><span class="sxs-lookup"><span data-stu-id="d3053-187">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="d3053-188">Quando si usa un segnaposto nel percorso, verificare che l'endpoint (pagina o controller) possa elaborare il segmento del percorso.</span><span class="sxs-lookup"><span data-stu-id="d3053-188">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="d3053-189">Ad esempio, una Razor pagina per gli errori deve accettare il valore del segmento di percorso facoltativo con la `@page` direttiva:</span><span class="sxs-lookup"><span data-stu-id="d3053-189">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="d3053-190">L'endpoint che elabora l'errore può ottenere l'URL originale che ha generato l'errore, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="d3053-190">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="d3053-191">Disabilitare le tabelle codici di stato</span><span class="sxs-lookup"><span data-stu-id="d3053-191">Disable status code pages</span></span>

<span data-ttu-id="d3053-192">Per disabilitare le tabelle codici di stato per un controller MVC o un metodo di azione, usare l' [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="d3053-192">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="d3053-193">Per disabilitare le tabelle codici di stato per richieste specifiche in un Razor metodo del gestore di pagine o in un controller MVC, usare <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="d3053-193">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="d3053-194">Codice di gestione delle eccezioni</span><span class="sxs-lookup"><span data-stu-id="d3053-194">Exception-handling code</span></span>

<span data-ttu-id="d3053-195">Il codice in pagine di gestione delle eccezioni può generare eccezioni.</span><span class="sxs-lookup"><span data-stu-id="d3053-195">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="d3053-196">È spesso consigliabile che le pagine di errore di produzione contengano esclusivamente contenuto statico.</span><span class="sxs-lookup"><span data-stu-id="d3053-196">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="d3053-197">Intestazioni di risposta</span><span class="sxs-lookup"><span data-stu-id="d3053-197">Response headers</span></span>

<span data-ttu-id="d3053-198">Dopo l'invio delle intestazioni per una risposta:</span><span class="sxs-lookup"><span data-stu-id="d3053-198">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="d3053-199">L'app non può modificare il codice di stato della risposta.</span><span class="sxs-lookup"><span data-stu-id="d3053-199">The app can't change the response's status code.</span></span>
* <span data-ttu-id="d3053-200">Non è possibile eseguire pagine o gestori delle eccezioni.</span><span class="sxs-lookup"><span data-stu-id="d3053-200">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="d3053-201">La risposta deve essere completata o la connessione deve essere interrotta.</span><span class="sxs-lookup"><span data-stu-id="d3053-201">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="d3053-202">Gestione delle eccezioni del server</span><span class="sxs-lookup"><span data-stu-id="d3053-202">Server exception handling</span></span>

<span data-ttu-id="d3053-203">Oltre alla logica di gestione delle eccezioni nell'app, l'[implementazione del server HTTP](xref:fundamentals/servers/index) può gestire alcune eccezioni.</span><span class="sxs-lookup"><span data-stu-id="d3053-203">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="d3053-204">Se rileva un'eccezione prima dell'invio delle intestazioni di risposta, il server invia una risposta *500 - Errore interno del server* senza corpo.</span><span class="sxs-lookup"><span data-stu-id="d3053-204">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="d3053-205">Se il server rileva un'eccezione dopo l'invio delle intestazioni di risposta, il server chiude la connessione.</span><span class="sxs-lookup"><span data-stu-id="d3053-205">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="d3053-206">Le richieste che non sono gestite dall'app vengono gestite dal server.</span><span class="sxs-lookup"><span data-stu-id="d3053-206">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="d3053-207">Qualsiasi eccezione che si verifica quando il server sta gestendo la richiesta viene gestita dalla gestione delle eccezioni del server.</span><span class="sxs-lookup"><span data-stu-id="d3053-207">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="d3053-208">Eventuali pagine di errore personalizzate dell'app, middleware di gestione delle eccezioni e filtri non hanno effetto su questo comportamento.</span><span class="sxs-lookup"><span data-stu-id="d3053-208">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="d3053-209">Gestione delle eccezioni durante l'avvio</span><span class="sxs-lookup"><span data-stu-id="d3053-209">Startup exception handling</span></span>

<span data-ttu-id="d3053-210">Solo il livello di hosting può gestire le eccezioni che si verificano durante l'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="d3053-210">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="d3053-211">L'host può essere configurato per [acquisire gli errori di avvio](xref:fundamentals/host/web-host#capture-startup-errors) e [acquisire errori dettagliati](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="d3053-211">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="d3053-212">Il livello di hosting può visualizzare una pagina di errore per un errore di avvio acquisito solo se l'errore si verifica dopo l'associazione indirizzo host/porta.</span><span class="sxs-lookup"><span data-stu-id="d3053-212">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="d3053-213">Se si verifica un errore di associazione:</span><span class="sxs-lookup"><span data-stu-id="d3053-213">If binding fails:</span></span>

* <span data-ttu-id="d3053-214">Il livello di hosting registra un'eccezione critica.</span><span class="sxs-lookup"><span data-stu-id="d3053-214">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="d3053-215">Si verifica un arresto anomalo del processo di dotnet.</span><span class="sxs-lookup"><span data-stu-id="d3053-215">The dotnet process crashes.</span></span>
* <span data-ttu-id="d3053-216">Non viene visualizzata alcuna pagina di errore quando il server HTTP è [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="d3053-216">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="d3053-217">Se durante l'esecuzione in [IIS](/iis) (o servizio app di Azure) o in [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) il processo non può essere avviato, viene restituito l'errore *502.5 Errore del processo* dal [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="d3053-217">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="d3053-218">Per altre informazioni, vedere <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="d3053-218">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="d3053-219">Pagina di errore di database</span><span class="sxs-lookup"><span data-stu-id="d3053-219">Database error page</span></span>

<span data-ttu-id="d3053-220">Il middleware della pagina di errore del database acquisisce le eccezioni correlate al database che possono essere risolte tramite Entity Framework migrazioni.</span><span class="sxs-lookup"><span data-stu-id="d3053-220">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="d3053-221">Quando si verificano queste eccezioni, viene generata una risposta HTML con i dettagli delle azioni possibili per risolvere il problema.</span><span class="sxs-lookup"><span data-stu-id="d3053-221">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="d3053-222">Questa pagina deve essere abilitata solo nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="d3053-222">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="d3053-223">Abilitare la pagina aggiungendo codice `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d3053-223">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="d3053-224"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> richiede il pacchetto NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) .</span><span class="sxs-lookup"><span data-stu-id="d3053-224"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="d3053-225">Filtri eccezioni</span><span class="sxs-lookup"><span data-stu-id="d3053-225">Exception filters</span></span>

<span data-ttu-id="d3053-226">Nelle app MVC i filtri delle eccezioni possono essere configurati a livello globale o per ogni controller o azione singolarmente.</span><span class="sxs-lookup"><span data-stu-id="d3053-226">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="d3053-227">Nelle Razor app Pages possono essere configurate a livello globale o per modello di pagina.</span><span class="sxs-lookup"><span data-stu-id="d3053-227">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="d3053-228">Questi filtri gestiscono tutte le eccezioni non gestite che si verificano durante l'esecuzione di un'azione del controller o di un altro filtro</span><span class="sxs-lookup"><span data-stu-id="d3053-228">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="d3053-229">Per altre informazioni, vedere <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="d3053-229">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="d3053-230">I filtri delle eccezioni sono utili per intercettare le eccezioni che si verificano all'interno di azioni MVC, ma non sono flessibili come il middleware di gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="d3053-230">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="d3053-231">È consigliabile usare il middleware.</span><span class="sxs-lookup"><span data-stu-id="d3053-231">We recommend using the middleware.</span></span> <span data-ttu-id="d3053-232">Usare i filtri solo quando è necessario eseguire la gestione degli errori in modo diverso in base all'azione MVC scelta.</span><span class="sxs-lookup"><span data-stu-id="d3053-232">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="d3053-233">Errori di stato del modello</span><span class="sxs-lookup"><span data-stu-id="d3053-233">Model state errors</span></span>

<span data-ttu-id="d3053-234">Per informazioni su come gestire gli errori dello stato del modello, vedere [Associazione di modelli](xref:mvc/models/model-binding) e [Convalida del modello](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="d3053-234">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d3053-235">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="d3053-235">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
