---
title: Gestire gli errori in ASP.NET Core
author: rick-anderson
description: Informazioni su come gestire gli errori nelle app ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: fundamentals/error-handling
ms.openlocfilehash: 28b463bccfb8aff4d10b95aa9a984455b4f4b976
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658816"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="643e5-103">Gestire gli errori in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="643e5-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="643e5-104">Di [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="643e5-104">By [Tom Dykstra](https://github.com/tdykstra/) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="643e5-105">Questo articolo illustra gli approcci comuni alla gestione degli errori nelle app Web di ASP.NET Core.This article covers common approaches to handling errors in ASP.NET Core web apps.</span><span class="sxs-lookup"><span data-stu-id="643e5-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="643e5-106">Vedere <xref:web-api/handle-errors> per le API Web.</span><span class="sxs-lookup"><span data-stu-id="643e5-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="643e5-107">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="643e5-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="643e5-108">([Come scaricare](xref:index#how-to-download-a-sample).) L'articolo include istruzioni su come impostare `#endif` `#define`direttive per il preprocessore (`#if`, , ) nell'app di esempio per abilitare scenari diversi.</span><span class="sxs-lookup"><span data-stu-id="643e5-108">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="643e5-109">Pagina delle eccezioni per gli sviluppatori</span><span class="sxs-lookup"><span data-stu-id="643e5-109">Developer Exception Page</span></span>

<span data-ttu-id="643e5-110">In *Pagina delle eccezioni per gli sviluppatori* sono disponibili informazioni dettagliate sulle eccezioni delle richieste.</span><span class="sxs-lookup"><span data-stu-id="643e5-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="643e5-111">La pagina è disponibile nel pacchetto [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) che si trova nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="643e5-111">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="643e5-112">Aggiungere codice al metodo `Startup.Configure` per abilitare la pagina quando l'app è in esecuzione nell'[ambiente](xref:fundamentals/environments) di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="643e5-112">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="643e5-113">Posizionare la chiamata a <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> prima di qualsiasi middleware in cui si vogliono rilevare le eccezioni.</span><span class="sxs-lookup"><span data-stu-id="643e5-113">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="643e5-114">Abilitare la pagina eccezione per sviluppatori **solo quando l'app è in esecuzione nell'ambiente**di sviluppo .</span><span class="sxs-lookup"><span data-stu-id="643e5-114">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="643e5-115">per non condividere pubblicamente le informazioni dettagliate sulle eccezioni quando l'app è in esecuzione nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="643e5-115">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="643e5-116">Per altre informazioni sulla configurazione di ambienti, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="643e5-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="643e5-117">La pagina include le informazioni seguenti sull'eccezione e sulla richiesta:</span><span class="sxs-lookup"><span data-stu-id="643e5-117">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="643e5-118">Analisi dello stack</span><span class="sxs-lookup"><span data-stu-id="643e5-118">Stack trace</span></span>
* <span data-ttu-id="643e5-119">Parametri della stringa di query (se presenti)</span><span class="sxs-lookup"><span data-stu-id="643e5-119">Query string parameters (if any)</span></span>
* <span data-ttu-id="643e5-120">Cookie (se presenti)</span><span class="sxs-lookup"><span data-stu-id="643e5-120">Cookies (if any)</span></span>
* <span data-ttu-id="643e5-121">Headers</span><span class="sxs-lookup"><span data-stu-id="643e5-121">Headers</span></span>

<span data-ttu-id="643e5-122">Per visualizzare la pagina delle eccezioni per gli sviluppatori nell'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), usare la direttiva del preprocessore `DevEnvironment` e selezionare **Trigger an exception** (Attiva un'eccezione) nella home page.</span><span class="sxs-lookup"><span data-stu-id="643e5-122">To see the Developer Exception Page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="643e5-123">Pagina del gestore di eccezioni</span><span class="sxs-lookup"><span data-stu-id="643e5-123">Exception handler page</span></span>

<span data-ttu-id="643e5-124">Per configurare una pagina di gestione degli errori personalizzata per l'ambiente di produzione, usare il middleware di gestione delle eccezioni.</span><span class="sxs-lookup"><span data-stu-id="643e5-124">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="643e5-125">Il middleware:</span><span class="sxs-lookup"><span data-stu-id="643e5-125">The middleware:</span></span>

* <span data-ttu-id="643e5-126">Intercetta e registra le eccezioni.</span><span class="sxs-lookup"><span data-stu-id="643e5-126">Catches and logs exceptions.</span></span>
* <span data-ttu-id="643e5-127">Esegue nuovamente la richiesta in una pipeline alternativa per la pagina o il controller indicati.</span><span class="sxs-lookup"><span data-stu-id="643e5-127">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="643e5-128">La richiesta non viene eseguita nuovamente se la risposta è stata avviata.</span><span class="sxs-lookup"><span data-stu-id="643e5-128">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="643e5-129">Nell'esempio seguente <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> aggiunge il middleware di gestione delle eccezioni in ambienti non di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="643e5-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="643e5-130">Il modello di app Razor Pages fornisce una pagina di errore (*.cshtml*) e una classe <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> (`ErrorModel`) nella cartella *Pages*.</span><span class="sxs-lookup"><span data-stu-id="643e5-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="643e5-131">Per un'app MVC, il modello di progetto include un metodo di azione Error e una visualizzazione degli errori.</span><span class="sxs-lookup"><span data-stu-id="643e5-131">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="643e5-132">Ecco il metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="643e5-132">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="643e5-133">Non contrassegnare il metodo di azione del gestore errori con gli attributi del metodo HTTP, ad `HttpGet`esempio .</span><span class="sxs-lookup"><span data-stu-id="643e5-133">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="643e5-134">I verbi espliciti impediscono ad alcune richieste di raggiungere il metodo.</span><span class="sxs-lookup"><span data-stu-id="643e5-134">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="643e5-135">Consentire l'accesso anonimo al metodo in modo che gli utenti non autenticati possano ricevere la visualizzazione degli errori.</span><span class="sxs-lookup"><span data-stu-id="643e5-135">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="643e5-136">Accedere all'eccezione</span><span class="sxs-lookup"><span data-stu-id="643e5-136">Access the exception</span></span>

<span data-ttu-id="643e5-137">Usare <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> per accedere all'eccezione e al percorso della richiesta originale in un controller o in una pagina del gestore degli errori:</span><span class="sxs-lookup"><span data-stu-id="643e5-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="643e5-138">**Non** fornire informazioni sugli errori sensibili ai client.</span><span class="sxs-lookup"><span data-stu-id="643e5-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="643e5-139">Fornire informazioni sugli errori rappresenta un rischio di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="643e5-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="643e5-140">Per visualizzare la pagina di gestione delle eccezioni nell'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), usare le direttive del preprocessore `ProdEnvironment` e `ErrorHandlerPage` e selezionare **Trigger an exception** (Attiva un'eccezione) nella home page.</span><span class="sxs-lookup"><span data-stu-id="643e5-140">To see the exception handling page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="643e5-141">Espressione lambda per il gestore di eccezioni</span><span class="sxs-lookup"><span data-stu-id="643e5-141">Exception handler lambda</span></span>

<span data-ttu-id="643e5-142">Un'alternativa a una [pagina di gestione delle eccezioni personalizzata](#exception-handler-page) consiste nel fornire un'espressione lambda a <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span><span class="sxs-lookup"><span data-stu-id="643e5-142">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span></span> <span data-ttu-id="643e5-143">L'uso di un'espressione lambda consente l'accesso all'errore prima di restituire la risposta.</span><span class="sxs-lookup"><span data-stu-id="643e5-143">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="643e5-144">Di seguito è riportato un esempio dell'uso di un'espressione lambda per la gestione delle eccezioni:</span><span class="sxs-lookup"><span data-stu-id="643e5-144">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="643e5-145">Nel codice precedente, `await context.Response.WriteAsync(new string(' ', 512));` viene aggiunto in modo che il browser Internet Explorer visualizza il messaggio di errore anziché un messaggio di errore di Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="643e5-145">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="643e5-146">Per altre informazioni, vedere [questo problema in GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="643e5-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="643e5-147">**Non** fornire informazioni sugli errori sensibili da <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> o <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> ai client.</span><span class="sxs-lookup"><span data-stu-id="643e5-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="643e5-148">Fornire informazioni sugli errori rappresenta un rischio di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="643e5-148">Serving errors is a security risk.</span></span>

<span data-ttu-id="643e5-149">Per visualizzare il risultato dell'espressione lambda di gestione delle eccezioni nell'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), usare le direttive del preprocessore `ProdEnvironment` e `ErrorHandlerLambda` e selezionare **Trigger an exception** (Attiva un'eccezione) nella home page.</span><span class="sxs-lookup"><span data-stu-id="643e5-149">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="643e5-150">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="643e5-150">UseStatusCodePages</span></span>

<span data-ttu-id="643e5-151">Per impostazione predefinita, un'app ASP.NET Core non offre una tabella codici di stato per i codici di stato HTTP, ad esempio *404 - Non trovato*.</span><span class="sxs-lookup"><span data-stu-id="643e5-151">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="643e5-152">L'app restituisce un codice di stato e un corpo della risposta vuoto.</span><span class="sxs-lookup"><span data-stu-id="643e5-152">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="643e5-153">Per specificare le tabelle codici di stato, usare il middleware delle tabelle codici di stato.</span><span class="sxs-lookup"><span data-stu-id="643e5-153">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="643e5-154">Il middleware è disponibile nel pacchetto [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) che si trova nel [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="643e5-154">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="643e5-155">Per abilitare i gestori di solo testo predefiniti per i codici di stato di errore comuni, chiamare <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> nel metodo `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="643e5-155">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="643e5-156">Chiamare `UseStatusCodePages` prima del middleware di gestione delle richieste (ad esempio, middleware dei file statici e middleware MVC).</span><span class="sxs-lookup"><span data-stu-id="643e5-156">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="643e5-157">Di seguito è riportato un esempio del testo visualizzato dai gestori predefiniti:</span><span class="sxs-lookup"><span data-stu-id="643e5-157">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="643e5-158">Per visualizzare uno dei vari formati di tabella codici di stato nell'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), usare una delle direttive del preprocessore che iniziano con `StatusCodePages` e selezionare **Trigger a 404** (Genera un 404) nella home page.</span><span class="sxs-lookup"><span data-stu-id="643e5-158">To see one of the various status code page formats in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="643e5-159">UseStatusCodePages con stringa di formato</span><span class="sxs-lookup"><span data-stu-id="643e5-159">UseStatusCodePages with format string</span></span>

<span data-ttu-id="643e5-160">Per personalizzare il tipo di contenuto della risposta e il testo, usare l'overload di <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> che accetta un tipo di contenuto e una stringa di formato:</span><span class="sxs-lookup"><span data-stu-id="643e5-160">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="643e5-161">UseStatusCodePages con espressione lambda</span><span class="sxs-lookup"><span data-stu-id="643e5-161">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="643e5-162">Per specificare la gestione degli errori personalizzata e il codice per la scrittura delle risposte, usare l'overload di <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> che accetta un'espressione lambda:</span><span class="sxs-lookup"><span data-stu-id="643e5-162">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="643e5-163">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="643e5-163">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="643e5-164">Metodo di estensione <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:</span><span class="sxs-lookup"><span data-stu-id="643e5-164">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> extension method:</span></span>

* <span data-ttu-id="643e5-165">Invia un codice di stato *302 - Trovato* al client.</span><span class="sxs-lookup"><span data-stu-id="643e5-165">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="643e5-166">Reindirizza il client al percorso specificato nel modello di URL.</span><span class="sxs-lookup"><span data-stu-id="643e5-166">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="643e5-167">Il modello di URL può includere un segnaposto `{0}` per il codice di stato, come illustrato nell'esempio.</span><span class="sxs-lookup"><span data-stu-id="643e5-167">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="643e5-168">Se il modello di URL inizia con una tilde (~), la tilde viene sostituita con il valore `PathBase` dell'app.</span><span class="sxs-lookup"><span data-stu-id="643e5-168">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="643e5-169">Se si punta a un endpoint all'interno dell'app, creare una visualizzazione MVC o una pagina Razor per l'endpoint.</span><span class="sxs-lookup"><span data-stu-id="643e5-169">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="643e5-170">Per un esempio di Razor Pages, vedere *StatusCode.cshtml* nell'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="643e5-170">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="643e5-171">Questo metodo viene usato comunemente quando l'app:</span><span class="sxs-lookup"><span data-stu-id="643e5-171">This method is commonly used when the app:</span></span>

* <span data-ttu-id="643e5-172">Deve reindirizzare il client a un endpoint diverso, in genere nei casi in cui un'altra app elabora l'errore.</span><span class="sxs-lookup"><span data-stu-id="643e5-172">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="643e5-173">Per le app Web, la barra degli indirizzi del browser del client riflette l'endpoint reindirizzato.</span><span class="sxs-lookup"><span data-stu-id="643e5-173">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="643e5-174">Non deve conservare e restituire il codice di stato originale con la risposta di reindirizzamento iniziale.</span><span class="sxs-lookup"><span data-stu-id="643e5-174">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="643e5-175">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="643e5-175">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="643e5-176">Metodo di estensione <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:</span><span class="sxs-lookup"><span data-stu-id="643e5-176">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> extension method:</span></span>

* <span data-ttu-id="643e5-177">Restituisce il codice di stato originale al client.</span><span class="sxs-lookup"><span data-stu-id="643e5-177">Returns the original status code to the client.</span></span>
* <span data-ttu-id="643e5-178">Genera il corpo della risposta eseguendo nuovamente la pipeline delle richieste con un percorso alternativo.</span><span class="sxs-lookup"><span data-stu-id="643e5-178">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="643e5-179">Se si punta a un endpoint all'interno dell'app, creare una visualizzazione MVC o una pagina Razor per l'endpoint.</span><span class="sxs-lookup"><span data-stu-id="643e5-179">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="643e5-180">Per un esempio di Razor Pages, vedere *StatusCode.cshtml* nell'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="643e5-180">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="643e5-181">Questo metodo viene usato comunemente quando l'app deve:</span><span class="sxs-lookup"><span data-stu-id="643e5-181">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="643e5-182">Elaborare la richiesta senza il reindirizzamento a un endpoint diverso.</span><span class="sxs-lookup"><span data-stu-id="643e5-182">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="643e5-183">Per le app Web, la barra degli indirizzi del browser del client riflette l'endpoint richiesto in origine.</span><span class="sxs-lookup"><span data-stu-id="643e5-183">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="643e5-184">Conservare e restituire il codice di stato originale con la risposta.</span><span class="sxs-lookup"><span data-stu-id="643e5-184">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="643e5-185">I modelli di URL e di stringa di query potrebbero includere un segnaposto (`{0}`) per il codice di stato.</span><span class="sxs-lookup"><span data-stu-id="643e5-185">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="643e5-186">Il modello di URL deve iniziare con una barra (`/`).</span><span class="sxs-lookup"><span data-stu-id="643e5-186">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="643e5-187">Quando si usa un segnaposto nel percorso, verificare che l'endpoint (pagina o controller) possa elaborare il segmento del percorso.</span><span class="sxs-lookup"><span data-stu-id="643e5-187">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="643e5-188">Ad esempio, una pagina Razor per gli errori deve accettare il valore del segmento di percorso facoltativo con la direttiva `@page`:</span><span class="sxs-lookup"><span data-stu-id="643e5-188">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="643e5-189">L'endpoint che elabora l'errore può ottenere l'URL originale che ha generato l'errore, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="643e5-189">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="643e5-190">Disabilitare le tabelle codici di stato</span><span class="sxs-lookup"><span data-stu-id="643e5-190">Disable status code pages</span></span>

<span data-ttu-id="643e5-191">Per disabilitare le tabelle codici di stato [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) per un controller MVC o un metodo di azione, usare l'attributo .</span><span class="sxs-lookup"><span data-stu-id="643e5-191">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="643e5-192">Per disabilitare le tabelle codici di stato per richieste specifiche in un metodo del gestore Razor Pages o in un controller MVC, usare <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span><span class="sxs-lookup"><span data-stu-id="643e5-192">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="643e5-193">Codice di gestione delle eccezioni</span><span class="sxs-lookup"><span data-stu-id="643e5-193">Exception-handling code</span></span>

<span data-ttu-id="643e5-194">Il codice in pagine di gestione delle eccezioni può generare eccezioni.</span><span class="sxs-lookup"><span data-stu-id="643e5-194">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="643e5-195">È spesso consigliabile che le pagine di errore di produzione contengano esclusivamente contenuto statico.</span><span class="sxs-lookup"><span data-stu-id="643e5-195">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="643e5-196">Intestazioni della risposta</span><span class="sxs-lookup"><span data-stu-id="643e5-196">Response headers</span></span>

<span data-ttu-id="643e5-197">Dopo l'invio delle intestazioni per una risposta:</span><span class="sxs-lookup"><span data-stu-id="643e5-197">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="643e5-198">L'app non può modificare il codice di stato della risposta.</span><span class="sxs-lookup"><span data-stu-id="643e5-198">The app can't change the response's status code.</span></span>
* <span data-ttu-id="643e5-199">Non è possibile eseguire pagine o gestori delle eccezioni.</span><span class="sxs-lookup"><span data-stu-id="643e5-199">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="643e5-200">La risposta deve essere completata o la connessione deve essere interrotta.</span><span class="sxs-lookup"><span data-stu-id="643e5-200">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="643e5-201">Gestione delle eccezioni del server</span><span class="sxs-lookup"><span data-stu-id="643e5-201">Server exception handling</span></span>

<span data-ttu-id="643e5-202">Oltre alla logica di gestione delle eccezioni nell'app, l'[implementazione del server HTTP](xref:fundamentals/servers/index) può gestire alcune eccezioni.</span><span class="sxs-lookup"><span data-stu-id="643e5-202">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="643e5-203">Se rileva un'eccezione prima dell'invio delle intestazioni di risposta, il server invia una risposta *500 - Errore interno del server* senza corpo.</span><span class="sxs-lookup"><span data-stu-id="643e5-203">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="643e5-204">Se il server rileva un'eccezione dopo l'invio delle intestazioni di risposta, il server chiude la connessione.</span><span class="sxs-lookup"><span data-stu-id="643e5-204">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="643e5-205">Le richieste che non sono gestite dall'app vengono gestite dal server.</span><span class="sxs-lookup"><span data-stu-id="643e5-205">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="643e5-206">Qualsiasi eccezione che si verifica quando il server sta gestendo la richiesta viene gestita dalla gestione delle eccezioni del server.</span><span class="sxs-lookup"><span data-stu-id="643e5-206">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="643e5-207">Eventuali pagine di errore personalizzate dell'app, middleware di gestione delle eccezioni e filtri non hanno effetto su questo comportamento.</span><span class="sxs-lookup"><span data-stu-id="643e5-207">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="643e5-208">Gestione delle eccezioni durante l'avvio</span><span class="sxs-lookup"><span data-stu-id="643e5-208">Startup exception handling</span></span>

<span data-ttu-id="643e5-209">Solo il livello di hosting può gestire le eccezioni che si verificano durante l'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="643e5-209">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="643e5-210">L'host può essere configurato per [acquisire gli errori di avvio](xref:fundamentals/host/web-host#capture-startup-errors) e [acquisire errori dettagliati](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="643e5-210">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="643e5-211">Il livello di hosting può visualizzare una pagina di errore per un errore di avvio acquisito solo se l'errore si verifica dopo l'associazione indirizzo host/porta.</span><span class="sxs-lookup"><span data-stu-id="643e5-211">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="643e5-212">Se si verifica un errore di associazione:</span><span class="sxs-lookup"><span data-stu-id="643e5-212">If binding fails:</span></span>

* <span data-ttu-id="643e5-213">Il livello di hosting registra un'eccezione critica.</span><span class="sxs-lookup"><span data-stu-id="643e5-213">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="643e5-214">Si verifica un arresto anomalo del processo di dotnet.</span><span class="sxs-lookup"><span data-stu-id="643e5-214">The dotnet process crashes.</span></span>
* <span data-ttu-id="643e5-215">Non viene visualizzata alcuna pagina di errore quando il server HTTP è [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="643e5-215">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="643e5-216">Se durante l'esecuzione in [IIS](/iis) (o servizio app di Azure) o in [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) il processo non può essere avviato, viene restituito l'errore *502.5 Errore del processo* dal [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="643e5-216">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="643e5-217">Per altre informazioni, vedere <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="643e5-217">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="643e5-218">Pagina di errore di database</span><span class="sxs-lookup"><span data-stu-id="643e5-218">Database error page</span></span>

<span data-ttu-id="643e5-219">Il middleware della pagina di errore del database acquisisce le eccezioni correlate al database che possono essere risolte utilizzando le migrazioni di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="643e5-219">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="643e5-220">Quando si verificano queste eccezioni, viene generata una risposta HTML con i dettagli delle azioni possibili per risolvere il problema.</span><span class="sxs-lookup"><span data-stu-id="643e5-220">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="643e5-221">Questa pagina deve essere abilitata solo nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="643e5-221">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="643e5-222">Abilitare la pagina aggiungendo codice `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="643e5-222">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="643e5-223">Filtri eccezioni</span><span class="sxs-lookup"><span data-stu-id="643e5-223">Exception filters</span></span>

<span data-ttu-id="643e5-224">Nelle app MVC i filtri delle eccezioni possono essere configurati a livello globale o per ogni controller o azione singolarmente.</span><span class="sxs-lookup"><span data-stu-id="643e5-224">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="643e5-225">Nelle app Razor Pages è possibile configurarli a livello globale o per ogni modello di pagina.</span><span class="sxs-lookup"><span data-stu-id="643e5-225">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="643e5-226">Questi filtri gestiscono tutte le eccezioni non gestite che si verificano durante l'esecuzione di un'azione del controller o di un altro filtro</span><span class="sxs-lookup"><span data-stu-id="643e5-226">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="643e5-227">Per altre informazioni, vedere <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="643e5-227">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="643e5-228">I filtri delle eccezioni sono utili per intercettare le eccezioni che si verificano all'interno di azioni MVC, ma non sono flessibili come il middleware di gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="643e5-228">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="643e5-229">È consigliabile usare il middleware.</span><span class="sxs-lookup"><span data-stu-id="643e5-229">We recommend using the middleware.</span></span> <span data-ttu-id="643e5-230">Usare i filtri solo quando è necessario eseguire la gestione degli errori in modo diverso in base all'azione MVC scelta.</span><span class="sxs-lookup"><span data-stu-id="643e5-230">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="643e5-231">Errori di stato del modello</span><span class="sxs-lookup"><span data-stu-id="643e5-231">Model state errors</span></span>

<span data-ttu-id="643e5-232">Per informazioni su come gestire gli errori dello stato del modello, vedere [Associazione di modelli](xref:mvc/models/model-binding) e [Convalida del modello](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="643e5-232">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="643e5-233">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="643e5-233">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
