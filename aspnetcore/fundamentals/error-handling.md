---
title: Gestire gli errori in ASP.NET Core
author: rick-anderson
description: Informazioni su come gestire gli errori nelle app ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: ad9920ccd830b93d083f3c5ede03702164842b6e
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "97753114"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="950c0-103">Gestire gli errori in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="950c0-103">Handle errors in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="950c0-104">Di [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/)e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="950c0-104">By [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="950c0-105">Questo articolo illustra gli approcci comuni per la gestione degli errori nelle app Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="950c0-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="950c0-106">Vedere <xref:web-api/handle-errors> per le API Web.</span><span class="sxs-lookup"><span data-stu-id="950c0-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="950c0-107">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="950c0-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="950c0-108">([Come scaricare](xref:index#how-to-download-a-sample)). La scheda rete di strumenti di sviluppo del browser F12 è utile quando si esegue il test dell'applicazione di esempio.</span><span class="sxs-lookup"><span data-stu-id="950c0-108">([How to download](xref:index#how-to-download-a-sample).) The network tab on the F12 browser developer tools is useful when testing the sample app.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="950c0-109">Pagina delle eccezioni per gli sviluppatori</span><span class="sxs-lookup"><span data-stu-id="950c0-109">Developer Exception Page</span></span>

<span data-ttu-id="950c0-110">In *Pagina delle eccezioni per gli sviluppatori* sono disponibili informazioni dettagliate sulle eccezioni delle richieste.</span><span class="sxs-lookup"><span data-stu-id="950c0-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="950c0-111">I modelli di ASP.NET Core generano il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="950c0-111">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="950c0-112">Il codice evidenziato precedente Abilita la pagina delle eccezioni per gli sviluppatori quando l'app è in esecuzione nell' [ambiente di sviluppo](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="950c0-112">The preceding highlighted code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="950c0-113">I modelli si posizionano all' <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> inizio della pipeline del middleware, in modo da poter intercettare le eccezioni generate nel middleware che segue.</span><span class="sxs-lookup"><span data-stu-id="950c0-113">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> early in the middleware pipeline so that it can catch exceptions thrown in middleware that follows.</span></span>

<span data-ttu-id="950c0-114">Il codice precedente Abilita la pagina di eccezione Developer \***solo** quando l'app è in esecuzione nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="950c0-114">The preceding code enables the Developer Exception Page \***only** _ when the app runs in the Development environment.</span></span> <span data-ttu-id="950c0-115">Le informazioni dettagliate sulle eccezioni non devono essere visualizzate pubblicamente quando l'app viene eseguita nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="950c0-115">Detailed exception information should not be displayed publicly when the app runs in the Production environment.</span></span> <span data-ttu-id="950c0-116">Per altre informazioni sulla configurazione di ambienti, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="950c0-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="950c0-117">La pagina delle eccezioni per sviluppatori include le informazioni seguenti sull'eccezione e la richiesta:</span><span class="sxs-lookup"><span data-stu-id="950c0-117">The Developer Exception Page includes the following information about the exception and the request:</span></span>

<span data-ttu-id="950c0-118">_ Traccia dello stack</span><span class="sxs-lookup"><span data-stu-id="950c0-118">_ Stack trace</span></span>
* <span data-ttu-id="950c0-119">Parametri della stringa di query se presenti</span><span class="sxs-lookup"><span data-stu-id="950c0-119">Query string parameters if any</span></span>
* <span data-ttu-id="950c0-120">Cookies se presente</span><span class="sxs-lookup"><span data-stu-id="950c0-120">Cookies if any</span></span>
* <span data-ttu-id="950c0-121">Intestazioni</span><span class="sxs-lookup"><span data-stu-id="950c0-121">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="950c0-122">Pagina del gestore di eccezioni</span><span class="sxs-lookup"><span data-stu-id="950c0-122">Exception handler page</span></span>

<span data-ttu-id="950c0-123">Per configurare una pagina di gestione degli errori personalizzata per l' [ambiente di produzione](xref:fundamentals/environments), chiamare <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="950c0-123">To configure a custom error handling page for the [Production environment](xref:fundamentals/environments), call <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="950c0-124">Questo middleware di gestione delle eccezioni:</span><span class="sxs-lookup"><span data-stu-id="950c0-124">This exception handling middleware:</span></span>

* <span data-ttu-id="950c0-125">Intercetta e registra le eccezioni.</span><span class="sxs-lookup"><span data-stu-id="950c0-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="950c0-126">Esegue nuovamente la richiesta in una pipeline alternativa usando il percorso indicato.</span><span class="sxs-lookup"><span data-stu-id="950c0-126">Re-executes the request in an alternate pipeline using the path indicated.</span></span> <span data-ttu-id="950c0-127">La richiesta non viene eseguita nuovamente se la risposta è stata avviata.</span><span class="sxs-lookup"><span data-stu-id="950c0-127">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="950c0-128">Il codice generato dal modello esegue nuovamente la richiesta utilizzando il `/Error` percorso.</span><span class="sxs-lookup"><span data-stu-id="950c0-128">The template generated code re-executes the request using the `/Error` path.</span></span>

<span data-ttu-id="950c0-129">Nell'esempio seguente, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> aggiunge il middleware di gestione delle eccezioni in ambienti non di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="950c0-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the exception handling middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="950c0-130">Il Razor modello di app pagine fornisce una pagina di errore (*cshtml*) e una <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> classe ( `ErrorModel` ) nella cartella *pagine* .</span><span class="sxs-lookup"><span data-stu-id="950c0-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="950c0-131">Per un'app MVC, il modello di progetto include un `Error` metodo di azione e una visualizzazione degli errori per il controller Home.</span><span class="sxs-lookup"><span data-stu-id="950c0-131">For an MVC app, the project template includes an `Error` action method and an Error view for the Home controller.</span></span>

<span data-ttu-id="950c0-132">Non contrassegnare il metodo di azione del gestore errori con attributi di metodo HTTP, ad esempio `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="950c0-132">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="950c0-133">I verbi espliciti impediscono che alcune richieste raggiungano il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="950c0-133">Explicit verbs prevent some requests from reaching the action method.</span></span> <span data-ttu-id="950c0-134">Consentire l'accesso anonimo al metodo se gli utenti non autenticati dovrebbero visualizzare la visualizzazione degli errori.</span><span class="sxs-lookup"><span data-stu-id="950c0-134">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="950c0-135">Accedere all'eccezione</span><span class="sxs-lookup"><span data-stu-id="950c0-135">Access the exception</span></span>

<span data-ttu-id="950c0-136">Utilizzare <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> per accedere all'eccezione e al percorso della richiesta originale in un gestore degli errori.</span><span class="sxs-lookup"><span data-stu-id="950c0-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler.</span></span> <span data-ttu-id="950c0-137">Il codice seguente aggiunge `ExceptionMessage` alle pagine predefinite */Error. cshtml. cs* generate dai modelli di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="950c0-137">The following code adds `ExceptionMessage` to the default *Pages/Error.cshtml.cs* generated by the ASP.NET Core templates:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> <span data-ttu-id="950c0-138">**Non** fornire informazioni sugli errori sensibili ai client.</span><span class="sxs-lookup"><span data-stu-id="950c0-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="950c0-139">Fornire informazioni sugli errori rappresenta un rischio di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="950c0-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="950c0-140">Per testare l'eccezione nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="950c0-140">To test the exception in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="950c0-141">Impostare l'ambiente sull'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="950c0-141">Set the environment to production.</span></span>
* <span data-ttu-id="950c0-142">Rimuovere i commenti da `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="950c0-142">Remove the comments from `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span></span>
* <span data-ttu-id="950c0-143">Selezionare **attiva un'eccezione** nel Home page.</span><span class="sxs-lookup"><span data-stu-id="950c0-143">Select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="950c0-144">Espressione lambda per il gestore di eccezioni</span><span class="sxs-lookup"><span data-stu-id="950c0-144">Exception handler lambda</span></span>

<span data-ttu-id="950c0-145">Un'alternativa a una [pagina di gestione delle eccezioni personalizzata](#exception-handler-page) consiste nel fornire un'espressione lambda a <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="950c0-145">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="950c0-146">L'uso di un'espressione lambda consente l'accesso all'errore prima di restituire la risposta.</span><span class="sxs-lookup"><span data-stu-id="950c0-146">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="950c0-147">Il codice seguente usa un'espressione lambda per la gestione delle eccezioni:</span><span class="sxs-lookup"><span data-stu-id="950c0-147">The following code uses a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <span data-ttu-id="950c0-148">**Non** fornire informazioni sugli errori sensibili da <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> o <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> ai client.</span><span class="sxs-lookup"><span data-stu-id="950c0-148">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="950c0-149">Fornire informazioni sugli errori rappresenta un rischio di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="950c0-149">Serving errors is a security risk.</span></span>

<span data-ttu-id="950c0-150">Per testare l'espressione lambda di gestione delle eccezioni nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="950c0-150">To test the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="950c0-151">Impostare l'ambiente sull'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="950c0-151">Set the environment to production.</span></span>
* <span data-ttu-id="950c0-152">Rimuovere i commenti da `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="950c0-152">Remove the comments from `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span></span>
* <span data-ttu-id="950c0-153">Selezionare **attiva un'eccezione** nel Home page.</span><span class="sxs-lookup"><span data-stu-id="950c0-153">Select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="950c0-154">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="950c0-154">UseStatusCodePages</span></span>

<span data-ttu-id="950c0-155">Per impostazione predefinita, un'app ASP.NET Core non fornisce una tabella codici di stato per i codici di stato di errore HTTP, ad esempio *404-non trovato*.</span><span class="sxs-lookup"><span data-stu-id="950c0-155">By default, an ASP.NET Core app doesn't provide a status code page for HTTP error status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="950c0-156">Quando l'app rileva un codice di stato di errore HTTP 400-599 che non ha un corpo, restituisce il codice di stato e un corpo della risposta vuoto.</span><span class="sxs-lookup"><span data-stu-id="950c0-156">When the app encounters an HTTP 400-599 error status code that doesn't have a body, it returns the status code and an empty response body.</span></span> <span data-ttu-id="950c0-157">Per specificare le tabelle codici di stato, usare il middleware tabelle codici di stato.</span><span class="sxs-lookup"><span data-stu-id="950c0-157">To provide status code pages, use the status code pages middleware.</span></span> <span data-ttu-id="950c0-158">Per abilitare i gestori di solo testo predefiniti per i codici di stato di errore comuni, chiamare <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> nel metodo `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="950c0-158">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<span data-ttu-id="950c0-159">Chiamare `UseStatusCodePages` prima del middleware di gestione delle richieste.</span><span class="sxs-lookup"><span data-stu-id="950c0-159">Call `UseStatusCodePages` before request handling middleware.</span></span> <span data-ttu-id="950c0-160">Ad esempio, chiamare `UseStatusCodePages` prima del middleware del file statico e del middleware degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="950c0-160">For example, call `UseStatusCodePages` before the Static File Middleware and the Endpoints Middleware.</span></span>

<span data-ttu-id="950c0-161">Quando `UseStatusCodePages` non viene utilizzato, la navigazione a un URL senza un endpoint restituisce un messaggio di errore dipendente dal browser che indica che l'endpoint non è stato trovato.</span><span class="sxs-lookup"><span data-stu-id="950c0-161">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="950c0-162">Ad esempio, passando a `Home/Privacy2` .</span><span class="sxs-lookup"><span data-stu-id="950c0-162">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="950c0-163">Quando `UseStatusCodePages` viene chiamato il metodo, il browser restituisce:</span><span class="sxs-lookup"><span data-stu-id="950c0-163">When `UseStatusCodePages` is called, the browser returns:</span></span>

```html
Status Code: 404; Not Found
```

<span data-ttu-id="950c0-164">`UseStatusCodePages` non viene in genere utilizzato nell'ambiente di produzione perché restituisce un messaggio che non è utile per gli utenti.</span><span class="sxs-lookup"><span data-stu-id="950c0-164">`UseStatusCodePages` isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="950c0-165">Per eseguire `UseStatusCodePages` il test nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="950c0-165">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="950c0-166">Impostare l'ambiente sull'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="950c0-166">Set the environment to production.</span></span>
* <span data-ttu-id="950c0-167">Rimuovere i commenti da `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="950c0-167">Remove the comments from `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span></span>
* <span data-ttu-id="950c0-168">Selezionare i collegamenti nel home page home page.</span><span class="sxs-lookup"><span data-stu-id="950c0-168">Select the links on the home page on the home page.</span></span>

> [!NOTE]
> <span data-ttu-id="950c0-169">Il middleware delle tabelle codici di stato **non** rileva le eccezioni.</span><span class="sxs-lookup"><span data-stu-id="950c0-169">The status code pages middleware does **not** catch exceptions.</span></span> <span data-ttu-id="950c0-170">Per fornire una pagina di gestione degli errori personalizzata, utilizzare la [pagina gestore eccezioni](#exception-handler-page).</span><span class="sxs-lookup"><span data-stu-id="950c0-170">To provide a custom error handling page, use the [exception handler page](#exception-handler-page).</span></span>

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="950c0-171">UseStatusCodePages con stringa di formato</span><span class="sxs-lookup"><span data-stu-id="950c0-171">UseStatusCodePages with format string</span></span>

<span data-ttu-id="950c0-172">Per personalizzare il tipo di contenuto della risposta e il testo, usare l'overload di <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> che accetta un tipo di contenuto e una stringa di formato:</span><span class="sxs-lookup"><span data-stu-id="950c0-172">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

<span data-ttu-id="950c0-173">Nel codice precedente, `{0}` è un segnaposto per il codice di errore.</span><span class="sxs-lookup"><span data-stu-id="950c0-173">In the preceding code, `{0}` is a placeholder for the error code.</span></span>

<span data-ttu-id="950c0-174">`UseStatusCodePages` con una stringa di formato non viene in genere usata nell'ambiente di produzione perché restituisce un messaggio che non è utile per gli utenti.</span><span class="sxs-lookup"><span data-stu-id="950c0-174">`UseStatusCodePages` with a format string isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="950c0-175">Per eseguire `UseStatusCodePages` il test nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), rimuovere i commenti da `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="950c0-175">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="950c0-176">UseStatusCodePages con espressione lambda</span><span class="sxs-lookup"><span data-stu-id="950c0-176">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="950c0-177">Per specificare la gestione degli errori personalizzata e il codice per la scrittura delle risposte, usare l'overload di <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> che accetta un'espressione lambda:</span><span class="sxs-lookup"><span data-stu-id="950c0-177">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="950c0-178">`UseStatusCodePages` con un'espressione lambda non viene in genere usata nell'ambiente di produzione perché restituisce un messaggio che non è utile per gli utenti.</span><span class="sxs-lookup"><span data-stu-id="950c0-178">`UseStatusCodePages` with a lambda isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="950c0-179">Per eseguire `UseStatusCodePages` il test nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), rimuovere i commenti da `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="950c0-179">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="950c0-180">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="950c0-180">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="950c0-181">Metodo di estensione <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:</span><span class="sxs-lookup"><span data-stu-id="950c0-181">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="950c0-182">Invia un codice di stato [302 - Trovato](https://developer.mozilla.org/docs/Web/HTTP/Status/302) al client.</span><span class="sxs-lookup"><span data-stu-id="950c0-182">Sends a [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) status code to the client.</span></span>
* <span data-ttu-id="950c0-183">Reindirizza il client all'endpoint di gestione degli errori fornito nel modello di URL.</span><span class="sxs-lookup"><span data-stu-id="950c0-183">Redirects the client to the error handling endpoint provided in the URL template.</span></span> <span data-ttu-id="950c0-184">Nell'endpoint di gestione degli errori vengono in genere visualizzate informazioni sull'errore e viene restituito il protocollo HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="950c0-184">The error handling endpoint typically displays error information and returns HTTP 200.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

<span data-ttu-id="950c0-185">Il modello di URL può includere un `{0}` segnaposto per il codice di stato, come illustrato nel codice precedente.</span><span class="sxs-lookup"><span data-stu-id="950c0-185">The URL template can include a `{0}` placeholder for the status code, as shown in the preceding code.</span></span> <span data-ttu-id="950c0-186">Se il modello di URL inizia con `~` (tilde), `~` viene sostituito dall'app `PathBase` .</span><span class="sxs-lookup"><span data-stu-id="950c0-186">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="950c0-187">Quando si specifica un endpoint nell'app, creare una visualizzazione o una Razor pagina MVC per l'endpoint.</span><span class="sxs-lookup"><span data-stu-id="950c0-187">When specifying an endpoint in the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="950c0-188">Per un Razor esempio di pagine, vedere [pages/MyStatusCode. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="950c0-188">For a Razor Pages example, see [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="950c0-189">Questo metodo viene usato comunemente quando l'app:</span><span class="sxs-lookup"><span data-stu-id="950c0-189">This method is commonly used when the app:</span></span>

* <span data-ttu-id="950c0-190">Deve reindirizzare il client a un endpoint diverso, in genere nei casi in cui un'altra app elabora l'errore.</span><span class="sxs-lookup"><span data-stu-id="950c0-190">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="950c0-191">Per le app Web, la barra degli indirizzi del browser del client riflette l'endpoint reindirizzato.</span><span class="sxs-lookup"><span data-stu-id="950c0-191">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="950c0-192">Non deve conservare e restituire il codice di stato originale con la risposta di reindirizzamento iniziale.</span><span class="sxs-lookup"><span data-stu-id="950c0-192">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="950c0-193">Per eseguire `UseStatusCodePages` il test nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), rimuovere i commenti da `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="950c0-193">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="950c0-194">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="950c0-194">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="950c0-195">Metodo di estensione <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:</span><span class="sxs-lookup"><span data-stu-id="950c0-195">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="950c0-196">Restituisce il codice di stato originale al client.</span><span class="sxs-lookup"><span data-stu-id="950c0-196">Returns the original status code to the client.</span></span>
* <span data-ttu-id="950c0-197">Genera il corpo della risposta eseguendo nuovamente la pipeline delle richieste con un percorso alternativo.</span><span class="sxs-lookup"><span data-stu-id="950c0-197">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

<span data-ttu-id="950c0-198">Se viene specificato un endpoint all'interno dell'app, creare una visualizzazione o una Razor pagina MVC per l'endpoint.</span><span class="sxs-lookup"><span data-stu-id="950c0-198">If an endpoint within the app is specified, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="950c0-199">Assicurarsi `UseStatusCodePagesWithReExecute` che venga inserito prima `UseRouting` di in modo che sia possibile reindirizzare la richiesta alla pagina stato.</span><span class="sxs-lookup"><span data-stu-id="950c0-199">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="950c0-200">Per un Razor esempio di pagine, vedere [pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="950c0-200">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="950c0-201">Questo metodo viene usato comunemente quando l'app deve:</span><span class="sxs-lookup"><span data-stu-id="950c0-201">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="950c0-202">Elaborare la richiesta senza il reindirizzamento a un endpoint diverso.</span><span class="sxs-lookup"><span data-stu-id="950c0-202">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="950c0-203">Per le app Web, la barra degli indirizzi del browser del client riflette l'endpoint richiesto in origine.</span><span class="sxs-lookup"><span data-stu-id="950c0-203">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="950c0-204">Conservare e restituire il codice di stato originale con la risposta.</span><span class="sxs-lookup"><span data-stu-id="950c0-204">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="950c0-205">I modelli URL e stringa di query possono includere un segnaposto `{0}` per il codice di stato.</span><span class="sxs-lookup"><span data-stu-id="950c0-205">The URL and query string templates may include a placeholder `{0}` for the status code.</span></span> <span data-ttu-id="950c0-206">Il modello di URL deve iniziare con `/` .</span><span class="sxs-lookup"><span data-stu-id="950c0-206">The URL template must start with `/`.</span></span>

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

<span data-ttu-id="950c0-207">L'endpoint che elabora l'errore può ottenere l'URL originale che ha generato l'errore, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="950c0-207">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

<span data-ttu-id="950c0-208">Per un Razor esempio di pagine, vedere [pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="950c0-208">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="950c0-209">Per eseguire `UseStatusCodePages` il test nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), rimuovere i commenti da `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="950c0-209">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="950c0-210">Disabilitare le tabelle codici di stato</span><span class="sxs-lookup"><span data-stu-id="950c0-210">Disable status code pages</span></span>

<span data-ttu-id="950c0-211">Per disabilitare le tabelle codici di stato per un controller MVC o un metodo di azione, usare l'attributo [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute).</span><span class="sxs-lookup"><span data-stu-id="950c0-211">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="950c0-212">Per disabilitare le tabelle codici di stato per richieste specifiche in un Razor metodo del gestore di pagine o in un controller MVC, usare <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="950c0-212">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a><span data-ttu-id="950c0-213">Codice di gestione delle eccezioni</span><span class="sxs-lookup"><span data-stu-id="950c0-213">Exception-handling code</span></span>

<span data-ttu-id="950c0-214">Anche il codice nelle pagine di gestione delle eccezioni può generare eccezioni.</span><span class="sxs-lookup"><span data-stu-id="950c0-214">Code in exception handling pages can also throw exceptions.</span></span> <span data-ttu-id="950c0-215">Le pagine di errore di produzione devono essere testate accuratamente e prestare particolare attenzione per evitare di generare eccezioni proprie.</span><span class="sxs-lookup"><span data-stu-id="950c0-215">Production error pages should be tested thoroughly and take extra care to avoid throwing exceptions of their own.</span></span>
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a><span data-ttu-id="950c0-216">Intestazioni di risposta</span><span class="sxs-lookup"><span data-stu-id="950c0-216">Response headers</span></span>

<span data-ttu-id="950c0-217">Dopo l'invio delle intestazioni per una risposta:</span><span class="sxs-lookup"><span data-stu-id="950c0-217">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="950c0-218">L'app non può modificare il codice di stato della risposta.</span><span class="sxs-lookup"><span data-stu-id="950c0-218">The app can't change the response's status code.</span></span>
* <span data-ttu-id="950c0-219">Non è possibile eseguire pagine o gestori delle eccezioni.</span><span class="sxs-lookup"><span data-stu-id="950c0-219">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="950c0-220">La risposta deve essere completata o la connessione deve essere interrotta.</span><span class="sxs-lookup"><span data-stu-id="950c0-220">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="950c0-221">Gestione delle eccezioni del server</span><span class="sxs-lookup"><span data-stu-id="950c0-221">Server exception handling</span></span>

<span data-ttu-id="950c0-222">Oltre alla logica di gestione delle eccezioni in un'app, l' [implementazione del server http](xref:fundamentals/servers/index) può gestire alcune eccezioni.</span><span class="sxs-lookup"><span data-stu-id="950c0-222">In addition to the exception handling logic in an app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="950c0-223">Se il server rileva un'eccezione prima dell'invio delle intestazioni di risposta, il server invia una `500 - Internal Server Error` risposta senza il corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="950c0-223">If the server catches an exception before response headers are sent, the server sends a `500 - Internal Server Error` response without a response body.</span></span> <span data-ttu-id="950c0-224">Se il server rileva un'eccezione dopo l'invio delle intestazioni di risposta, il server chiude la connessione.</span><span class="sxs-lookup"><span data-stu-id="950c0-224">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="950c0-225">Le richieste che non vengono gestite dall'app vengono gestite dal server.</span><span class="sxs-lookup"><span data-stu-id="950c0-225">Requests that aren't handled by the app are handled by the server.</span></span> <span data-ttu-id="950c0-226">Qualsiasi eccezione che si verifica quando il server sta gestendo la richiesta viene gestita dalla gestione delle eccezioni del server.</span><span class="sxs-lookup"><span data-stu-id="950c0-226">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="950c0-227">Eventuali pagine di errore personalizzate dell'app, middleware di gestione delle eccezioni e filtri non hanno effetto su questo comportamento.</span><span class="sxs-lookup"><span data-stu-id="950c0-227">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="950c0-228">Gestione delle eccezioni durante l'avvio</span><span class="sxs-lookup"><span data-stu-id="950c0-228">Startup exception handling</span></span>

<span data-ttu-id="950c0-229">Solo il livello di hosting può gestire le eccezioni che si verificano durante l'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="950c0-229">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="950c0-230">L'host può essere configurato per [acquisire gli errori di avvio](xref:fundamentals/host/web-host#capture-startup-errors) e [acquisire errori dettagliati](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="950c0-230">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="950c0-231">Il livello di hosting può visualizzare una pagina di errore per un errore di avvio acquisito solo se l'errore si verifica dopo l'associazione indirizzo host/porta.</span><span class="sxs-lookup"><span data-stu-id="950c0-231">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="950c0-232">Se si verifica un errore di associazione:</span><span class="sxs-lookup"><span data-stu-id="950c0-232">If binding fails:</span></span>

* <span data-ttu-id="950c0-233">Il livello di hosting registra un'eccezione critica.</span><span class="sxs-lookup"><span data-stu-id="950c0-233">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="950c0-234">Si verifica un arresto anomalo del processo di dotnet.</span><span class="sxs-lookup"><span data-stu-id="950c0-234">The dotnet process crashes.</span></span>
* <span data-ttu-id="950c0-235">Non viene visualizzata alcuna pagina di errore quando il server HTTP è [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="950c0-235">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="950c0-236">Se durante l'esecuzione in [IIS](/iis) (o servizio app di Azure) o in [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) il processo non può essere avviato, viene restituito l'errore *502.5 Errore del processo* dal [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="950c0-236">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="950c0-237">Per altre informazioni, vedere <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="950c0-237">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="950c0-238">Pagina di errore di database</span><span class="sxs-lookup"><span data-stu-id="950c0-238">Database error page</span></span>

<span data-ttu-id="950c0-239">Il filtro eccezioni pagina sviluppatore database `AddDatabaseDeveloperPageExceptionFilter` acquisisce le eccezioni correlate al database che possono essere risolte utilizzando Entity Framework Core migrazioni.</span><span class="sxs-lookup"><span data-stu-id="950c0-239">The Database developer page exception filter `AddDatabaseDeveloperPageExceptionFilter` captures database-related exceptions that can be resolved by using Entity Framework Core migrations.</span></span> <span data-ttu-id="950c0-240">Quando si verificano queste eccezioni, viene generata una risposta HTML con i dettagli delle azioni possibili per risolvere il problema.</span><span class="sxs-lookup"><span data-stu-id="950c0-240">When these exceptions occur, an HTML response is generated with details of possible actions to resolve the issue.</span></span> <span data-ttu-id="950c0-241">Questa pagina è abilitata solo nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="950c0-241">This page is enabled only in the Development environment.</span></span> <span data-ttu-id="950c0-242">Il codice seguente è stato generato dal ASP.NET Core Razor modelli di pagine quando sono stati specificati singoli account utente:</span><span class="sxs-lookup"><span data-stu-id="950c0-242">The following code was generated by the ASP.NET Core Razor Pages templates when individual user accounts were specified:</span></span>

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a><span data-ttu-id="950c0-243">Filtri eccezioni</span><span class="sxs-lookup"><span data-stu-id="950c0-243">Exception filters</span></span>

<span data-ttu-id="950c0-244">Nelle app MVC i filtri delle eccezioni possono essere configurati a livello globale o per ogni controller o azione singolarmente.</span><span class="sxs-lookup"><span data-stu-id="950c0-244">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="950c0-245">Nelle Razor app Pages possono essere configurate a livello globale o per modello di pagina.</span><span class="sxs-lookup"><span data-stu-id="950c0-245">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="950c0-246">Questi filtri gestiscono le eventuali eccezioni non gestite che si verificano durante l'esecuzione di un'azione del controller o di un altro filtro.</span><span class="sxs-lookup"><span data-stu-id="950c0-246">These filters handle any unhandled exceptions that occur during the execution of a controller action or another filter.</span></span> <span data-ttu-id="950c0-247">Per altre informazioni, vedere <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="950c0-247">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

<span data-ttu-id="950c0-248">I filtri eccezioni sono utili per intercettare le eccezioni che si verificano all'interno di azioni MVC, ma non sono flessibili come il [middleware di gestione delle eccezioni](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs)incorporato, `UseExceptionHandler` .</span><span class="sxs-lookup"><span data-stu-id="950c0-248">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the built-in [exception handling middleware](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span></span> <span data-ttu-id="950c0-249">È consigliabile usare `UseExceptionHandler` , a meno che non sia necessario eseguire la gestione degli errori in modo diverso in base all'azione MVC scelta.</span><span class="sxs-lookup"><span data-stu-id="950c0-249">We recommend using `UseExceptionHandler`, unless you need to perform error handling differently based on which MVC action is chosen.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a><span data-ttu-id="950c0-250">Errori di stato del modello</span><span class="sxs-lookup"><span data-stu-id="950c0-250">Model state errors</span></span>

<span data-ttu-id="950c0-251">Per informazioni su come gestire gli errori dello stato del modello, vedere [Associazione di modelli](xref:mvc/models/model-binding) e [Convalida del modello](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="950c0-251">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="950c0-252">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="950c0-252">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="950c0-253">Di  [Tom Dykstra](https://github.com/tdykstra/)e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="950c0-253">By  [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="950c0-254">Questo articolo illustra gli approcci comuni per la gestione degli errori nelle app Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="950c0-254">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="950c0-255">Vedere <xref:web-api/handle-errors> per le API Web.</span><span class="sxs-lookup"><span data-stu-id="950c0-255">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="950c0-256">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="950c0-256">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="950c0-257">([Come scaricare un esempio](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="950c0-257">([How to download](xref:index#how-to-download-a-sample).)</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="950c0-258">Pagina delle eccezioni per gli sviluppatori</span><span class="sxs-lookup"><span data-stu-id="950c0-258">Developer Exception Page</span></span>

<span data-ttu-id="950c0-259">In *Pagina delle eccezioni per gli sviluppatori* sono disponibili informazioni dettagliate sulle eccezioni delle richieste.</span><span class="sxs-lookup"><span data-stu-id="950c0-259">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="950c0-260">I modelli di ASP.NET Core generano il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="950c0-260">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="950c0-261">Il codice precedente Abilita la pagina delle eccezioni per gli sviluppatori quando l'app è in esecuzione nell' [ambiente di sviluppo](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="950c0-261">The preceding code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="950c0-262">I modelli si posizionano <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> prima di qualsiasi middleware, in modo che le eccezioni vengano rilevate nel middleware che segue.</span><span class="sxs-lookup"><span data-stu-id="950c0-262">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware so exceptions are caught in the middleware that follows.</span></span>

<span data-ttu-id="950c0-263">Il codice precedente Abilita la pagina delle eccezioni **per gli sviluppatori solo quando l'app è in esecuzione nell'ambiente di sviluppo**.</span><span class="sxs-lookup"><span data-stu-id="950c0-263">The preceding code enables the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="950c0-264">Le informazioni dettagliate sulle eccezioni non devono essere visualizzate pubblicamente quando l'app viene eseguita nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="950c0-264">Detailed exception information should not be displayed publicly when the app runs in production.</span></span> <span data-ttu-id="950c0-265">Per altre informazioni sulla configurazione di ambienti, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="950c0-265">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="950c0-266">La pagina delle eccezioni per sviluppatori include le informazioni seguenti sull'eccezione e la richiesta:</span><span class="sxs-lookup"><span data-stu-id="950c0-266">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="950c0-267">Analisi dello stack</span><span class="sxs-lookup"><span data-stu-id="950c0-267">Stack trace</span></span>
* <span data-ttu-id="950c0-268">Parametri della stringa di query se presenti</span><span class="sxs-lookup"><span data-stu-id="950c0-268">Query string parameters if any</span></span>
* <span data-ttu-id="950c0-269">Cookies se presente</span><span class="sxs-lookup"><span data-stu-id="950c0-269">Cookies if any</span></span>
* <span data-ttu-id="950c0-270">Intestazioni</span><span class="sxs-lookup"><span data-stu-id="950c0-270">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="950c0-271">Pagina del gestore di eccezioni</span><span class="sxs-lookup"><span data-stu-id="950c0-271">Exception handler page</span></span>

<span data-ttu-id="950c0-272">Per configurare una pagina di gestione degli errori personalizzata per l'ambiente di produzione, usare il middleware di gestione delle eccezioni.</span><span class="sxs-lookup"><span data-stu-id="950c0-272">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="950c0-273">Il middleware:</span><span class="sxs-lookup"><span data-stu-id="950c0-273">The middleware:</span></span>

* <span data-ttu-id="950c0-274">Intercetta e registra le eccezioni.</span><span class="sxs-lookup"><span data-stu-id="950c0-274">Catches and logs exceptions.</span></span>
* <span data-ttu-id="950c0-275">Esegue nuovamente la richiesta in una pipeline alternativa per la pagina o il controller indicati.</span><span class="sxs-lookup"><span data-stu-id="950c0-275">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="950c0-276">La richiesta non viene eseguita nuovamente se la risposta è stata avviata.</span><span class="sxs-lookup"><span data-stu-id="950c0-276">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="950c0-277">Il codice generato dal modello esegue nuovamente la richiesta a `/Error` .</span><span class="sxs-lookup"><span data-stu-id="950c0-277">The template generated code re-executes the request to `/Error`.</span></span>

<span data-ttu-id="950c0-278">Nell'esempio seguente <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> aggiunge il middleware di gestione delle eccezioni in ambienti non di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="950c0-278">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="950c0-279">Il Razor modello di app pagine fornisce una pagina di errore (*cshtml*) e una <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> classe ( `ErrorModel` ) nella cartella *pagine* .</span><span class="sxs-lookup"><span data-stu-id="950c0-279">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="950c0-280">Per un'app MVC, il modello di progetto include un metodo di azione di errore e una visualizzazione degli errori nel controller Home.</span><span class="sxs-lookup"><span data-stu-id="950c0-280">For an MVC app, the project template includes an Error action method and an Error view in the Home controller.</span></span>

<span data-ttu-id="950c0-281">Non contrassegnare il metodo di azione del gestore errori con attributi di metodo HTTP, ad esempio `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="950c0-281">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="950c0-282">I verbi espliciti impediscono ad alcune richieste di raggiungere il metodo.</span><span class="sxs-lookup"><span data-stu-id="950c0-282">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="950c0-283">Consentire l'accesso anonimo al metodo se gli utenti non autenticati dovrebbero visualizzare la visualizzazione degli errori.</span><span class="sxs-lookup"><span data-stu-id="950c0-283">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="950c0-284">Accedere all'eccezione</span><span class="sxs-lookup"><span data-stu-id="950c0-284">Access the exception</span></span>

<span data-ttu-id="950c0-285">Usare <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> per accedere all'eccezione e al percorso della richiesta originale in un controller o in una pagina del gestore degli errori:</span><span class="sxs-lookup"><span data-stu-id="950c0-285">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="950c0-286">**Non** fornire informazioni sugli errori sensibili ai client.</span><span class="sxs-lookup"><span data-stu-id="950c0-286">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="950c0-287">Fornire informazioni sugli errori rappresenta un rischio di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="950c0-287">Serving errors is a security risk.</span></span>

<span data-ttu-id="950c0-288">Per attivare la pagina di gestione delle eccezioni precedente, impostare l'ambiente sulle produzioni e forzare un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="950c0-288">To trigger the preceding exception handling page, set the environment to productions and force an exception.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="950c0-289">Espressione lambda per il gestore di eccezioni</span><span class="sxs-lookup"><span data-stu-id="950c0-289">Exception handler lambda</span></span>

<span data-ttu-id="950c0-290">Un'alternativa a una [pagina di gestione delle eccezioni personalizzata](#exception-handler-page) consiste nel fornire un'espressione lambda a <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="950c0-290">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="950c0-291">L'uso di un'espressione lambda consente l'accesso all'errore prima di restituire la risposta.</span><span class="sxs-lookup"><span data-stu-id="950c0-291">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="950c0-292">Di seguito è riportato un esempio dell'uso di un'espressione lambda per la gestione delle eccezioni:</span><span class="sxs-lookup"><span data-stu-id="950c0-292">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="950c0-293">Nel codice precedente `await context.Response.WriteAsync(new string(' ', 512));` viene aggiunto, in modo che il browser Internet Explorer visualizzi il messaggio di errore anziché un messaggio di errore di IE.</span><span class="sxs-lookup"><span data-stu-id="950c0-293">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="950c0-294">Per altre informazioni, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="950c0-294">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="950c0-295">**Non** fornire informazioni sugli errori sensibili da <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> o <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> ai client.</span><span class="sxs-lookup"><span data-stu-id="950c0-295">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="950c0-296">Fornire informazioni sugli errori rappresenta un rischio di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="950c0-296">Serving errors is a security risk.</span></span>

<span data-ttu-id="950c0-297">Per visualizzare il risultato dell'espressione lambda di gestione delle eccezioni nell'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), usare le direttive del preprocessore `ProdEnvironment` e `ErrorHandlerLambda` e selezionare **Trigger an exception** (Attiva un'eccezione) nella home page.</span><span class="sxs-lookup"><span data-stu-id="950c0-297">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="950c0-298">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="950c0-298">UseStatusCodePages</span></span>

<span data-ttu-id="950c0-299">Per impostazione predefinita, un'app ASP.NET Core non offre una tabella codici di stato per i codici di stato HTTP, ad esempio *404 - Non trovato*.</span><span class="sxs-lookup"><span data-stu-id="950c0-299">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="950c0-300">L'app restituisce un codice di stato e un corpo della risposta vuoto.</span><span class="sxs-lookup"><span data-stu-id="950c0-300">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="950c0-301">Per specificare le tabelle codici di stato, usare il middleware delle tabelle codici di stato.</span><span class="sxs-lookup"><span data-stu-id="950c0-301">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="950c0-302">Il middleware viene reso disponibile dal pacchetto [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) .</span><span class="sxs-lookup"><span data-stu-id="950c0-302">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package.</span></span>

<span data-ttu-id="950c0-303">Per abilitare i gestori di solo testo predefiniti per i codici di stato di errore comuni, chiamare <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> nel metodo `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="950c0-303">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="950c0-304">Chiamare `UseStatusCodePages` prima del middleware di gestione delle richieste (ad esempio, middleware dei file statici e middleware MVC).</span><span class="sxs-lookup"><span data-stu-id="950c0-304">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="950c0-305">Quando `UseStatusCodePages` non viene utilizzato, la navigazione a un URL senza un endpoint restituisce un messaggio di errore dipendente dal browser che indica che l'endpoint non è stato trovato.</span><span class="sxs-lookup"><span data-stu-id="950c0-305">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="950c0-306">Ad esempio, passando a `Home/Privacy2` .</span><span class="sxs-lookup"><span data-stu-id="950c0-306">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="950c0-307">Quando `UseStatusCodePages` viene chiamato il metodo, il browser restituisce:</span><span class="sxs-lookup"><span data-stu-id="950c0-307">When `UseStatusCodePages` is called, the browser returns:</span></span>

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="950c0-308">UseStatusCodePages con stringa di formato</span><span class="sxs-lookup"><span data-stu-id="950c0-308">UseStatusCodePages with format string</span></span>

<span data-ttu-id="950c0-309">Per personalizzare il tipo di contenuto della risposta e il testo, usare l'overload di <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> che accetta un tipo di contenuto e una stringa di formato:</span><span class="sxs-lookup"><span data-stu-id="950c0-309">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="950c0-310">UseStatusCodePages con espressione lambda</span><span class="sxs-lookup"><span data-stu-id="950c0-310">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="950c0-311">Per specificare la gestione degli errori personalizzata e il codice per la scrittura delle risposte, usare l'overload di <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> che accetta un'espressione lambda:</span><span class="sxs-lookup"><span data-stu-id="950c0-311">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="950c0-312">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="950c0-312">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="950c0-313">Metodo di estensione <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:</span><span class="sxs-lookup"><span data-stu-id="950c0-313">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="950c0-314">Invia un codice di stato *302 - Trovato* al client.</span><span class="sxs-lookup"><span data-stu-id="950c0-314">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="950c0-315">Reindirizza il client al percorso specificato nel modello di URL.</span><span class="sxs-lookup"><span data-stu-id="950c0-315">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="950c0-316">Il modello di URL può includere un segnaposto `{0}` per il codice di stato, come illustrato nell'esempio.</span><span class="sxs-lookup"><span data-stu-id="950c0-316">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="950c0-317">Se il modello di URL inizia con `~` (tilde), `~` viene sostituito dall'app `PathBase` .</span><span class="sxs-lookup"><span data-stu-id="950c0-317">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="950c0-318">Se si punta a un endpoint all'interno dell'app, creare una visualizzazione o una Razor pagina MVC per l'endpoint.</span><span class="sxs-lookup"><span data-stu-id="950c0-318">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="950c0-319">Per un Razor esempio di pagine, vedere *pages/StatusCode. cshtml* nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="950c0-319">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="950c0-320">Questo metodo viene usato comunemente quando l'app:</span><span class="sxs-lookup"><span data-stu-id="950c0-320">This method is commonly used when the app:</span></span>

* <span data-ttu-id="950c0-321">Deve reindirizzare il client a un endpoint diverso, in genere nei casi in cui un'altra app elabora l'errore.</span><span class="sxs-lookup"><span data-stu-id="950c0-321">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="950c0-322">Per le app Web, la barra degli indirizzi del browser del client riflette l'endpoint reindirizzato.</span><span class="sxs-lookup"><span data-stu-id="950c0-322">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="950c0-323">Non deve conservare e restituire il codice di stato originale con la risposta di reindirizzamento iniziale.</span><span class="sxs-lookup"><span data-stu-id="950c0-323">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="950c0-324">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="950c0-324">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="950c0-325">Metodo di estensione <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:</span><span class="sxs-lookup"><span data-stu-id="950c0-325">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="950c0-326">Restituisce il codice di stato originale al client.</span><span class="sxs-lookup"><span data-stu-id="950c0-326">Returns the original status code to the client.</span></span>
* <span data-ttu-id="950c0-327">Genera il corpo della risposta eseguendo nuovamente la pipeline delle richieste con un percorso alternativo.</span><span class="sxs-lookup"><span data-stu-id="950c0-327">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="950c0-328">Se si punta a un endpoint all'interno dell'app, creare una visualizzazione o una Razor pagina MVC per l'endpoint.</span><span class="sxs-lookup"><span data-stu-id="950c0-328">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="950c0-329">Assicurarsi `UseStatusCodePagesWithReExecute` che venga inserito prima `UseRouting` di in modo che sia possibile reindirizzare la richiesta alla pagina stato.</span><span class="sxs-lookup"><span data-stu-id="950c0-329">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="950c0-330">Per un Razor esempio di pagine, vedere *pages/StatusCode. cshtml* nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="950c0-330">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="950c0-331">Questo metodo viene usato comunemente quando l'app deve:</span><span class="sxs-lookup"><span data-stu-id="950c0-331">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="950c0-332">Elaborare la richiesta senza il reindirizzamento a un endpoint diverso.</span><span class="sxs-lookup"><span data-stu-id="950c0-332">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="950c0-333">Per le app Web, la barra degli indirizzi del browser del client riflette l'endpoint richiesto in origine.</span><span class="sxs-lookup"><span data-stu-id="950c0-333">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="950c0-334">Conservare e restituire il codice di stato originale con la risposta.</span><span class="sxs-lookup"><span data-stu-id="950c0-334">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="950c0-335">I modelli di URL e di stringa di query potrebbero includere un segnaposto (`{0}`) per il codice di stato.</span><span class="sxs-lookup"><span data-stu-id="950c0-335">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="950c0-336">Il modello di URL deve iniziare con una barra (`/`).</span><span class="sxs-lookup"><span data-stu-id="950c0-336">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="950c0-337">Quando si usa un segnaposto nel percorso, verificare che l'endpoint (pagina o controller) possa elaborare il segmento del percorso.</span><span class="sxs-lookup"><span data-stu-id="950c0-337">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="950c0-338">Ad esempio, una Razor pagina per gli errori deve accettare il valore del segmento di percorso facoltativo con la `@page` direttiva:</span><span class="sxs-lookup"><span data-stu-id="950c0-338">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="950c0-339">L'endpoint che elabora l'errore può ottenere l'URL originale che ha generato l'errore, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="950c0-339">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="950c0-340">Disabilitare le tabelle codici di stato</span><span class="sxs-lookup"><span data-stu-id="950c0-340">Disable status code pages</span></span>

<span data-ttu-id="950c0-341">Per disabilitare le tabelle codici di stato per un controller MVC o un metodo di azione, usare l' [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="950c0-341">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="950c0-342">Per disabilitare le tabelle codici di stato per richieste specifiche in un Razor metodo del gestore di pagine o in un controller MVC, usare <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="950c0-342">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="950c0-343">Codice di gestione delle eccezioni</span><span class="sxs-lookup"><span data-stu-id="950c0-343">Exception-handling code</span></span>

<span data-ttu-id="950c0-344">Il codice in pagine di gestione delle eccezioni può generare eccezioni.</span><span class="sxs-lookup"><span data-stu-id="950c0-344">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="950c0-345">È spesso consigliabile che le pagine di errore di produzione contengano esclusivamente contenuto statico.</span><span class="sxs-lookup"><span data-stu-id="950c0-345">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="950c0-346">Intestazioni di risposta</span><span class="sxs-lookup"><span data-stu-id="950c0-346">Response headers</span></span>

<span data-ttu-id="950c0-347">Dopo l'invio delle intestazioni per una risposta:</span><span class="sxs-lookup"><span data-stu-id="950c0-347">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="950c0-348">L'app non può modificare il codice di stato della risposta.</span><span class="sxs-lookup"><span data-stu-id="950c0-348">The app can't change the response's status code.</span></span>
* <span data-ttu-id="950c0-349">Non è possibile eseguire pagine o gestori delle eccezioni.</span><span class="sxs-lookup"><span data-stu-id="950c0-349">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="950c0-350">La risposta deve essere completata o la connessione deve essere interrotta.</span><span class="sxs-lookup"><span data-stu-id="950c0-350">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="950c0-351">Gestione delle eccezioni del server</span><span class="sxs-lookup"><span data-stu-id="950c0-351">Server exception handling</span></span>

<span data-ttu-id="950c0-352">Oltre alla logica di gestione delle eccezioni nell'app, l'[implementazione del server HTTP](xref:fundamentals/servers/index) può gestire alcune eccezioni.</span><span class="sxs-lookup"><span data-stu-id="950c0-352">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="950c0-353">Se rileva un'eccezione prima dell'invio delle intestazioni di risposta, il server invia una risposta *500 - Errore interno del server* senza corpo.</span><span class="sxs-lookup"><span data-stu-id="950c0-353">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="950c0-354">Se il server rileva un'eccezione dopo l'invio delle intestazioni di risposta, il server chiude la connessione.</span><span class="sxs-lookup"><span data-stu-id="950c0-354">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="950c0-355">Le richieste che non sono gestite dall'app vengono gestite dal server.</span><span class="sxs-lookup"><span data-stu-id="950c0-355">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="950c0-356">Qualsiasi eccezione che si verifica quando il server sta gestendo la richiesta viene gestita dalla gestione delle eccezioni del server.</span><span class="sxs-lookup"><span data-stu-id="950c0-356">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="950c0-357">Eventuali pagine di errore personalizzate dell'app, middleware di gestione delle eccezioni e filtri non hanno effetto su questo comportamento.</span><span class="sxs-lookup"><span data-stu-id="950c0-357">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="950c0-358">Gestione delle eccezioni durante l'avvio</span><span class="sxs-lookup"><span data-stu-id="950c0-358">Startup exception handling</span></span>

<span data-ttu-id="950c0-359">Solo il livello di hosting può gestire le eccezioni che si verificano durante l'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="950c0-359">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="950c0-360">L'host può essere configurato per [acquisire gli errori di avvio](xref:fundamentals/host/web-host#capture-startup-errors) e [acquisire errori dettagliati](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="950c0-360">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="950c0-361">Il livello di hosting può visualizzare una pagina di errore per un errore di avvio acquisito solo se l'errore si verifica dopo l'associazione indirizzo host/porta.</span><span class="sxs-lookup"><span data-stu-id="950c0-361">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="950c0-362">Se si verifica un errore di associazione:</span><span class="sxs-lookup"><span data-stu-id="950c0-362">If binding fails:</span></span>

* <span data-ttu-id="950c0-363">Il livello di hosting registra un'eccezione critica.</span><span class="sxs-lookup"><span data-stu-id="950c0-363">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="950c0-364">Si verifica un arresto anomalo del processo di dotnet.</span><span class="sxs-lookup"><span data-stu-id="950c0-364">The dotnet process crashes.</span></span>
* <span data-ttu-id="950c0-365">Non viene visualizzata alcuna pagina di errore quando il server HTTP è [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="950c0-365">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="950c0-366">Se durante l'esecuzione in [IIS](/iis) (o servizio app di Azure) o in [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) il processo non può essere avviato, viene restituito l'errore *502.5 Errore del processo* dal [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="950c0-366">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="950c0-367">Per altre informazioni, vedere <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="950c0-367">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="950c0-368">Pagina di errore di database</span><span class="sxs-lookup"><span data-stu-id="950c0-368">Database error page</span></span>

<span data-ttu-id="950c0-369">Il middleware della pagina di errore del database acquisisce le eccezioni correlate al database che possono essere risolte tramite Entity Framework migrazioni.</span><span class="sxs-lookup"><span data-stu-id="950c0-369">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="950c0-370">Quando si verificano queste eccezioni, viene generata una risposta HTML con i dettagli delle azioni possibili per risolvere il problema.</span><span class="sxs-lookup"><span data-stu-id="950c0-370">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="950c0-371">Questa pagina deve essere abilitata solo nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="950c0-371">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="950c0-372">Abilitare la pagina aggiungendo codice `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="950c0-372">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="950c0-373"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> richiede il pacchetto NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) .</span><span class="sxs-lookup"><span data-stu-id="950c0-373"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="950c0-374">Filtri eccezioni</span><span class="sxs-lookup"><span data-stu-id="950c0-374">Exception filters</span></span>

<span data-ttu-id="950c0-375">Nelle app MVC i filtri delle eccezioni possono essere configurati a livello globale o per ogni controller o azione singolarmente.</span><span class="sxs-lookup"><span data-stu-id="950c0-375">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="950c0-376">Nelle Razor app Pages possono essere configurate a livello globale o per modello di pagina.</span><span class="sxs-lookup"><span data-stu-id="950c0-376">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="950c0-377">Questi filtri gestiscono tutte le eccezioni non gestite che si verificano durante l'esecuzione di un'azione del controller o di un altro filtro</span><span class="sxs-lookup"><span data-stu-id="950c0-377">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="950c0-378">Per altre informazioni, vedere <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="950c0-378">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="950c0-379">I filtri delle eccezioni sono utili per intercettare le eccezioni che si verificano all'interno di azioni MVC, ma non sono flessibili come il middleware di gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="950c0-379">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="950c0-380">È consigliabile usare il middleware.</span><span class="sxs-lookup"><span data-stu-id="950c0-380">We recommend using the middleware.</span></span> <span data-ttu-id="950c0-381">Usare i filtri solo quando è necessario eseguire la gestione degli errori in modo diverso in base all'azione MVC scelta.</span><span class="sxs-lookup"><span data-stu-id="950c0-381">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="950c0-382">Errori di stato del modello</span><span class="sxs-lookup"><span data-stu-id="950c0-382">Model state errors</span></span>

<span data-ttu-id="950c0-383">Per informazioni su come gestire gli errori dello stato del modello, vedere [Associazione di modelli](xref:mvc/models/model-binding) e [Convalida del modello](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="950c0-383">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="950c0-384">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="950c0-384">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
