---
title: Eseguire la migrazione di gestori e moduli HTTP a ASP.NET Core middleware
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/http-modules
ms.openlocfilehash: 362dd16db358f7ceb6730bde908fff9854c73a84
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403651"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="b6ada-102">Eseguire la migrazione di gestori e moduli HTTP a ASP.NET Core middleware</span><span class="sxs-lookup"><span data-stu-id="b6ada-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="b6ada-103">Questo articolo illustra come eseguire la migrazione di [moduli e gestori HTTP ASP.NET esistenti da System. webserver](/iis/configuration/system.webserver/) a [middleware](xref:fundamentals/middleware/index)ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b6ada-103">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="b6ada-104">Moduli e gestori rivisitati</span><span class="sxs-lookup"><span data-stu-id="b6ada-104">Modules and handlers revisited</span></span>

<span data-ttu-id="b6ada-105">Prima di procedere con ASP.NET Core middleware, è necessario riepilogare il funzionamento dei moduli e gestori HTTP:</span><span class="sxs-lookup"><span data-stu-id="b6ada-105">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![Gestore moduli](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="b6ada-107">**I gestori sono:**</span><span class="sxs-lookup"><span data-stu-id="b6ada-107">**Handlers are:**</span></span>

* <span data-ttu-id="b6ada-108">Classi che implementano [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span><span class="sxs-lookup"><span data-stu-id="b6ada-108">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

* <span data-ttu-id="b6ada-109">Utilizzato per gestire le richieste con un nome file o un'estensione specifica, ad esempio *. report*</span><span class="sxs-lookup"><span data-stu-id="b6ada-109">Used to handle requests with a given file name or extension, such as *.report*</span></span>

* <span data-ttu-id="b6ada-110">[Configurato](/iis/configuration/system.webserver/handlers/) in *Web.config*</span><span class="sxs-lookup"><span data-stu-id="b6ada-110">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="b6ada-111">**I moduli sono:**</span><span class="sxs-lookup"><span data-stu-id="b6ada-111">**Modules are:**</span></span>

* <span data-ttu-id="b6ada-112">Classi che implementano [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span><span class="sxs-lookup"><span data-stu-id="b6ada-112">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

* <span data-ttu-id="b6ada-113">Richiamato per ogni richiesta</span><span class="sxs-lookup"><span data-stu-id="b6ada-113">Invoked for every request</span></span>

* <span data-ttu-id="b6ada-114">In grado di eseguire il cortocircuito (arrestare l'ulteriore elaborazione di una richiesta)</span><span class="sxs-lookup"><span data-stu-id="b6ada-114">Able to short-circuit (stop further processing of a request)</span></span>

* <span data-ttu-id="b6ada-115">In grado di aggiungere alla risposta HTTP o di crearne di propri</span><span class="sxs-lookup"><span data-stu-id="b6ada-115">Able to add to the HTTP response, or create their own</span></span>

* <span data-ttu-id="b6ada-116">[Configurato](/iis/configuration/system.webserver/modules/) in *Web.config*</span><span class="sxs-lookup"><span data-stu-id="b6ada-116">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="b6ada-117">**L'ordine in cui i moduli elaborano le richieste in ingresso è determinato da:**</span><span class="sxs-lookup"><span data-stu-id="b6ada-117">**The order in which modules process incoming requests is determined by:**</span></span>

1. <span data-ttu-id="b6ada-118">Il [ciclo di vita dell'applicazione](https://msdn.microsoft.com/library/ms227673.aspx), ovvero eventi di serie generati da ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)e così via. Ogni modulo può creare un gestore per uno o più eventi.</span><span class="sxs-lookup"><span data-stu-id="b6ada-118">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

2. <span data-ttu-id="b6ada-119">Per lo stesso evento, l'ordine in cui sono configurati in *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="b6ada-119">For the same event, the order in which they're configured in *Web.config*.</span></span>

<span data-ttu-id="b6ada-120">Oltre ai moduli, è possibile aggiungere i gestori per gli eventi del ciclo di vita al file *Global.asax.cs* .</span><span class="sxs-lookup"><span data-stu-id="b6ada-120">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="b6ada-121">Questi gestori vengono eseguiti dopo i gestori nei moduli configurati.</span><span class="sxs-lookup"><span data-stu-id="b6ada-121">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="b6ada-122">Da gestori e moduli al middleware</span><span class="sxs-lookup"><span data-stu-id="b6ada-122">From handlers and modules to middleware</span></span>

<span data-ttu-id="b6ada-123">**Il middleware è più semplice dei moduli e gestori HTTP:**</span><span class="sxs-lookup"><span data-stu-id="b6ada-123">**Middleware are simpler than HTTP modules and handlers:**</span></span>

* <span data-ttu-id="b6ada-124">Moduli, gestori, *Global.asax.cs*, *Web.config* (eccetto la configurazione di IIS) e il ciclo di vita dell'applicazione sono finiti</span><span class="sxs-lookup"><span data-stu-id="b6ada-124">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

* <span data-ttu-id="b6ada-125">I ruoli di moduli e gestori sono stati rilevati dal middleware</span><span class="sxs-lookup"><span data-stu-id="b6ada-125">The roles of both modules and handlers have been taken over by middleware</span></span>

* <span data-ttu-id="b6ada-126">Il middleware viene configurato usando codice anziché in *Web.config*</span><span class="sxs-lookup"><span data-stu-id="b6ada-126">Middleware are configured using code rather than in *Web.config*</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="b6ada-127">La creazione di [rami della pipeline](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) consente di inviare richieste a un middleware specifico, in base non solo all'URL, ma anche alle intestazioni delle richieste, alle stringhe di query e così via.</span><span class="sxs-lookup"><span data-stu-id="b6ada-127">[Pipeline branching](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="b6ada-128">La creazione di [rami della pipeline](xref:fundamentals/middleware/index#use-run-and-map) consente di inviare richieste a un middleware specifico, in base non solo all'URL, ma anche alle intestazioni delle richieste, alle stringhe di query e così via.</span><span class="sxs-lookup"><span data-stu-id="b6ada-128">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

::: moniker-end

<span data-ttu-id="b6ada-129">**Il middleware è molto simile ai moduli:**</span><span class="sxs-lookup"><span data-stu-id="b6ada-129">**Middleware are very similar to modules:**</span></span>

* <span data-ttu-id="b6ada-130">Richiamato in linea di base per ogni richiesta</span><span class="sxs-lookup"><span data-stu-id="b6ada-130">Invoked in principle for every request</span></span>

* <span data-ttu-id="b6ada-131">In grado di eseguire il cortocircuito di una richiesta, [non passando la richiesta al middleware successivo](#http-modules-shortcircuiting-middleware)</span><span class="sxs-lookup"><span data-stu-id="b6ada-131">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

* <span data-ttu-id="b6ada-132">In grado di creare la propria risposta HTTP</span><span class="sxs-lookup"><span data-stu-id="b6ada-132">Able to create their own HTTP response</span></span>

<span data-ttu-id="b6ada-133">**Il middleware e i moduli vengono elaborati in un ordine diverso:**</span><span class="sxs-lookup"><span data-stu-id="b6ada-133">**Middleware and modules are processed in a different order:**</span></span>

* <span data-ttu-id="b6ada-134">L'ordine del middleware si basa sull'ordine in cui vengono inseriti nella pipeline delle richieste, mentre l'ordine dei moduli è basato principalmente sugli eventi del [ciclo di vita dell'applicazione](https://msdn.microsoft.com/library/ms227673.aspx)</span><span class="sxs-lookup"><span data-stu-id="b6ada-134">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

* <span data-ttu-id="b6ada-135">L'ordine del middleware per le risposte è il contrario rispetto a quello per le richieste, mentre l'ordine dei moduli è lo stesso per le richieste e le risposte</span><span class="sxs-lookup"><span data-stu-id="b6ada-135">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

* <span data-ttu-id="b6ada-136">Vedere [creare una pipeline middleware con IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="b6ada-136">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![Middleware](http-modules/_static/middleware.png)

<span data-ttu-id="b6ada-138">Si noti come nell'immagine precedente il middleware di autenticazione ha corto circuito della richiesta.</span><span class="sxs-lookup"><span data-stu-id="b6ada-138">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="b6ada-139">Migrazione del codice del modulo al middleware</span><span class="sxs-lookup"><span data-stu-id="b6ada-139">Migrating module code to middleware</span></span>

<span data-ttu-id="b6ada-140">Un modulo HTTP esistente sarà simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="b6ada-140">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="b6ada-141">Come illustrato nella pagina [middleware](xref:fundamentals/middleware/index) , un middleware ASP.NET Core è una classe che espone un `Invoke` metodo che accetta un oggetto `HttpContext` e restituisce un oggetto `Task` .</span><span class="sxs-lookup"><span data-stu-id="b6ada-141">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="b6ada-142">Il nuovo middleware sarà simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="b6ada-142">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="b6ada-143">Il modello middleware precedente è stato ricavato dalla sezione sulla [scrittura del middleware](xref:fundamentals/middleware/write).</span><span class="sxs-lookup"><span data-stu-id="b6ada-143">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="b6ada-144">La classe helper *MyMiddlewareExtensions* rende più semplice la configurazione del middleware nella `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="b6ada-144">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="b6ada-145">Il `UseMyMiddleware` metodo aggiunge la classe middleware alla pipeline della richiesta.</span><span class="sxs-lookup"><span data-stu-id="b6ada-145">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="b6ada-146">I servizi richiesti dal middleware vengono inseriti nel costruttore del middleware.</span><span class="sxs-lookup"><span data-stu-id="b6ada-146">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="b6ada-147">Il modulo potrebbe terminare una richiesta, ad esempio se l'utente non è autorizzato:</span><span class="sxs-lookup"><span data-stu-id="b6ada-147">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="b6ada-148">Un middleware gestisce questa operazione non chiamando il `Invoke` middleware successivo nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="b6ada-148">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="b6ada-149">Tenere presente che questa operazione non termina completamente la richiesta, perché i middleware precedenti verranno comunque richiamati quando la risposta ripercorre la pipeline.</span><span class="sxs-lookup"><span data-stu-id="b6ada-149">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="b6ada-150">Quando si esegue la migrazione della funzionalità del modulo al nuovo middleware, è possibile che il codice non venga compilato perché la `HttpContext` classe è cambiata in modo significativo in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b6ada-150">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="b6ada-151">[Successivamente](#migrating-to-the-new-httpcontext), verrà illustrato come eseguire la migrazione al nuovo ASP.NET Core HttpContext.</span><span class="sxs-lookup"><span data-stu-id="b6ada-151">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="b6ada-152">Migrazione dell'inserimento del modulo nella pipeline delle richieste</span><span class="sxs-lookup"><span data-stu-id="b6ada-152">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="b6ada-153">I moduli HTTP vengono in genere aggiunti alla pipeline di richieste usando *Web.config*:</span><span class="sxs-lookup"><span data-stu-id="b6ada-153">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="b6ada-154">Per convertirlo, [aggiungere il nuovo middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) alla pipeline delle richieste nella `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="b6ada-154">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="b6ada-155">Il punto esatto della pipeline in cui si inserisce il nuovo middleware dipende dall'evento gestito come modulo ( `BeginRequest` , e `EndRequest` così via) e dall'ordine nell'elenco dei moduli in *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="b6ada-155">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="b6ada-156">Come indicato in precedenza, non esiste alcun ciclo di vita dell'applicazione in ASP.NET Core e l'ordine in cui le risposte vengono elaborate dal middleware differisce dall'ordine usato dai moduli.</span><span class="sxs-lookup"><span data-stu-id="b6ada-156">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="b6ada-157">Questo potrebbe rendere più complessa la decisione di ordinamento.</span><span class="sxs-lookup"><span data-stu-id="b6ada-157">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="b6ada-158">Se l'ordinamento diventa un problema, è possibile suddividere il modulo in più componenti middleware che possono essere ordinati in modo indipendente.</span><span class="sxs-lookup"><span data-stu-id="b6ada-158">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="b6ada-159">Migrazione del codice del gestore al middleware</span><span class="sxs-lookup"><span data-stu-id="b6ada-159">Migrating handler code to middleware</span></span>

<span data-ttu-id="b6ada-160">Un gestore HTTP ha un aspetto simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="b6ada-160">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="b6ada-161">Nel progetto di ASP.NET Core, è necessario tradurlo in un middleware simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="b6ada-161">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="b6ada-162">Questo middleware è molto simile al middleware corrispondente ai moduli.</span><span class="sxs-lookup"><span data-stu-id="b6ada-162">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="b6ada-163">L'unica differenza reale è che in questo caso non viene chiamata `_next.Invoke(context)` .</span><span class="sxs-lookup"><span data-stu-id="b6ada-163">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="b6ada-164">Questa operazione è sensata, perché il gestore si trova alla fine della pipeline delle richieste, quindi non ci sarà alcun middleware successivo da richiamare.</span><span class="sxs-lookup"><span data-stu-id="b6ada-164">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="b6ada-165">Migrazione dell'inserimento del gestore nella pipeline delle richieste</span><span class="sxs-lookup"><span data-stu-id="b6ada-165">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="b6ada-166">La configurazione di un gestore HTTP viene eseguita in *Web.config* e ha un aspetto simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="b6ada-166">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="b6ada-167">È possibile eseguire la conversione aggiungendo il middleware del nuovo gestore alla pipeline delle richieste nella `Startup` classe, simile a middleware convertito da moduli.</span><span class="sxs-lookup"><span data-stu-id="b6ada-167">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="b6ada-168">Il problema con questo approccio è che invia tutte le richieste al nuovo middleware del gestore.</span><span class="sxs-lookup"><span data-stu-id="b6ada-168">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="b6ada-169">Tuttavia, si desidera che le richieste con una determinata estensione raggiungano il middleware.</span><span class="sxs-lookup"><span data-stu-id="b6ada-169">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="b6ada-170">Questo consentirebbe di ottenere le stesse funzionalità con il gestore HTTP.</span><span class="sxs-lookup"><span data-stu-id="b6ada-170">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="b6ada-171">Una soluzione consiste nell'eseguire il branching della pipeline per le richieste con una determinata estensione, usando il `MapWhen` metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="b6ada-171">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="b6ada-172">Questa operazione viene eseguita nello stesso `Configure` metodo in cui si aggiunge l'altro middleware:</span><span class="sxs-lookup"><span data-stu-id="b6ada-172">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="b6ada-173">`MapWhen`accetta i parametri seguenti:</span><span class="sxs-lookup"><span data-stu-id="b6ada-173">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="b6ada-174">Espressione lambda che accetta l'oggetto `HttpContext` e restituisce `true` se la richiesta deve andare al di sotto del ramo.</span><span class="sxs-lookup"><span data-stu-id="b6ada-174">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="b6ada-175">Ciò significa che è possibile creare branch richieste non solo in base alla relativa estensione, ma anche su intestazioni di richiesta, parametri della stringa di query e così via.</span><span class="sxs-lookup"><span data-stu-id="b6ada-175">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="b6ada-176">Espressione lambda che accetta un oggetto `IApplicationBuilder` e aggiunge tutto il middleware per il ramo.</span><span class="sxs-lookup"><span data-stu-id="b6ada-176">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="b6ada-177">Ciò significa che è possibile aggiungere un middleware aggiuntivo al ramo davanti al middleware del gestore.</span><span class="sxs-lookup"><span data-stu-id="b6ada-177">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="b6ada-178">Middleware aggiunto alla pipeline prima che il ramo venga richiamato in tutte le richieste; il ramo non avrà alcun effetto su di essi.</span><span class="sxs-lookup"><span data-stu-id="b6ada-178">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="b6ada-179">Caricamento delle opzioni del middleware usando il modello di opzioni</span><span class="sxs-lookup"><span data-stu-id="b6ada-179">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="b6ada-180">Alcuni moduli e gestori hanno opzioni di configurazione archiviate in *Web.config*. Tuttavia, in ASP.NET Core viene usato un nuovo modello di configurazione al posto di *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="b6ada-180">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="b6ada-181">Il nuovo [sistema di configurazione](xref:fundamentals/configuration/index) offre le opzioni seguenti per risolvere il problema:</span><span class="sxs-lookup"><span data-stu-id="b6ada-181">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="b6ada-182">Inserire direttamente le opzioni nel middleware, come illustrato nella [sezione successiva](#loading-middleware-options-through-direct-injection).</span><span class="sxs-lookup"><span data-stu-id="b6ada-182">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="b6ada-183">Usare il [modello di opzioni](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="b6ada-183">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="b6ada-184">Creare una classe che contenga le opzioni del middleware, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b6ada-184">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="b6ada-185">Archiviare i valori delle opzioni</span><span class="sxs-lookup"><span data-stu-id="b6ada-185">Store the option values</span></span>

   <span data-ttu-id="b6ada-186">Il sistema di configurazione consente di archiviare i valori delle opzioni ovunque si desideri.</span><span class="sxs-lookup"><span data-stu-id="b6ada-186">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="b6ada-187">Tuttavia, la maggior parte dei siti USA *appsettings.json*, quindi prendiamo questo approccio:</span><span class="sxs-lookup"><span data-stu-id="b6ada-187">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   <span data-ttu-id="b6ada-188">*MyMiddlewareOptionsSection* è il nome di una sezione.</span><span class="sxs-lookup"><span data-stu-id="b6ada-188">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="b6ada-189">Non deve corrispondere al nome della classe Options.</span><span class="sxs-lookup"><span data-stu-id="b6ada-189">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="b6ada-190">Associare i valori delle opzioni alla classe Options</span><span class="sxs-lookup"><span data-stu-id="b6ada-190">Associate the option values with the options class</span></span>

    <span data-ttu-id="b6ada-191">Il modello di opzioni usa il Framework di inserimento delle dipendenze di ASP.NET Core per associare il tipo di opzioni (ad esempio `MyMiddlewareOptions` ) a un `MyMiddlewareOptions` oggetto con le opzioni effettive.</span><span class="sxs-lookup"><span data-stu-id="b6ada-191">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="b6ada-192">Aggiornare la `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="b6ada-192">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="b6ada-193">Se si usa *appsettings.json*, aggiungerlo al generatore di configurazione nel `Startup` costruttore:</span><span class="sxs-lookup"><span data-stu-id="b6ada-193">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="b6ada-194">Configurare il servizio opzioni:</span><span class="sxs-lookup"><span data-stu-id="b6ada-194">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="b6ada-195">Associare le opzioni alla classe Options:</span><span class="sxs-lookup"><span data-stu-id="b6ada-195">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="b6ada-196">Inserire le opzioni nel costruttore del middleware.</span><span class="sxs-lookup"><span data-stu-id="b6ada-196">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="b6ada-197">Questa operazione è simile all'inserimento di opzioni in un controller.</span><span class="sxs-lookup"><span data-stu-id="b6ada-197">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="b6ada-198">Il metodo di estensione [UseMiddleware](#http-modules-usemiddleware) che aggiunge il middleware al `IApplicationBuilder` si occupa dell'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="b6ada-198">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="b6ada-199">Questo non è limitato agli `IOptions` oggetti.</span><span class="sxs-lookup"><span data-stu-id="b6ada-199">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="b6ada-200">Tutti gli altri oggetti richiesti dal middleware possono essere inseriti in questo modo.</span><span class="sxs-lookup"><span data-stu-id="b6ada-200">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="b6ada-201">Caricamento delle opzioni del middleware tramite l'inserimento diretto</span><span class="sxs-lookup"><span data-stu-id="b6ada-201">Loading middleware options through direct injection</span></span>

<span data-ttu-id="b6ada-202">Il modello Options presenta il vantaggio di creare un accoppiamento libero tra i valori delle opzioni e i relativi consumer.</span><span class="sxs-lookup"><span data-stu-id="b6ada-202">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="b6ada-203">Una volta associata una classe Options con i valori effettivi delle opzioni, qualsiasi altra classe può ottenere l'accesso alle opzioni tramite il Framework di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="b6ada-203">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="b6ada-204">Non è necessario passare i valori delle opzioni.</span><span class="sxs-lookup"><span data-stu-id="b6ada-204">There's no need to pass around options values.</span></span>

<span data-ttu-id="b6ada-205">Questa operazione si interrompe anche se si vuole usare lo stesso middleware due volte, con opzioni diverse.</span><span class="sxs-lookup"><span data-stu-id="b6ada-205">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="b6ada-206">Ad esempio un middleware di autorizzazione usato in rami diversi che consentono ruoli diversi.</span><span class="sxs-lookup"><span data-stu-id="b6ada-206">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="b6ada-207">Non è possibile associare due oggetti Options diversi a una classe di opzioni.</span><span class="sxs-lookup"><span data-stu-id="b6ada-207">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="b6ada-208">La soluzione consiste nell'ottenere gli oggetti Options con i valori effettivi delle opzioni nella `Startup` classe e passarli direttamente a ogni istanza del middleware.</span><span class="sxs-lookup"><span data-stu-id="b6ada-208">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="b6ada-209">Aggiungi una seconda chiave alla *appsettings.js*</span><span class="sxs-lookup"><span data-stu-id="b6ada-209">Add a second key to *appsettings.json*</span></span>

   <span data-ttu-id="b6ada-210">Per aggiungere un secondo set di opzioni all' *appsettings.jssu* file, usare una nuova chiave per identificarla in modo univoco:</span><span class="sxs-lookup"><span data-stu-id="b6ada-210">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="b6ada-211">Recuperare i valori delle opzioni e passarli al middleware.</span><span class="sxs-lookup"><span data-stu-id="b6ada-211">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="b6ada-212">Il `Use...` metodo di estensione, che aggiunge il middleware alla pipeline, è una posizione logica per passare i valori delle opzioni:</span><span class="sxs-lookup"><span data-stu-id="b6ada-212">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="b6ada-213">Consentire al middleware di prendere un parametro options.</span><span class="sxs-lookup"><span data-stu-id="b6ada-213">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="b6ada-214">Fornire un overload del `Use...` metodo di estensione (che accetta il parametro options e lo passa a `UseMiddleware` ).</span><span class="sxs-lookup"><span data-stu-id="b6ada-214">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="b6ada-215">Quando `UseMiddleware` viene chiamato con parametri, passa i parametri al costruttore del middleware quando crea un'istanza dell'oggetto middleware.</span><span class="sxs-lookup"><span data-stu-id="b6ada-215">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="b6ada-216">Si noti che in questo modo viene eseguito il wrapping dell'oggetto options in un `OptionsWrapper` oggetto.</span><span class="sxs-lookup"><span data-stu-id="b6ada-216">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="b6ada-217">Implementa `IOptions` , come previsto dal costruttore del middleware.</span><span class="sxs-lookup"><span data-stu-id="b6ada-217">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="b6ada-218">Migrazione al nuovo HttpContext</span><span class="sxs-lookup"><span data-stu-id="b6ada-218">Migrating to the new HttpContext</span></span>

<span data-ttu-id="b6ada-219">Si è visto in precedenza che il `Invoke` metodo nel middleware accetta un parametro di tipo `HttpContext` :</span><span class="sxs-lookup"><span data-stu-id="b6ada-219">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="b6ada-220">`HttpContext`è stato modificato in modo significativo in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b6ada-220">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="b6ada-221">In questa sezione viene illustrato come tradurre le proprietà più comunemente utilizzate di [System. Web. HttpContext](/dotnet/api/system.web.httpcontext) nel nuovo oggetto `Microsoft.AspNetCore.Http.HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="b6ada-221">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="b6ada-222">HttpContext</span><span class="sxs-lookup"><span data-stu-id="b6ada-222">HttpContext</span></span>

<span data-ttu-id="b6ada-223">**HttpContext. Items** viene convertito in:</span><span class="sxs-lookup"><span data-stu-id="b6ada-223">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="b6ada-224">**ID richiesta univoco (nessuna controparte System. Web. HttpContext)**</span><span class="sxs-lookup"><span data-stu-id="b6ada-224">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="b6ada-225">Fornisce un ID univoco per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="b6ada-225">Gives you a unique id for each request.</span></span> <span data-ttu-id="b6ada-226">Molto utile da includere nei log.</span><span class="sxs-lookup"><span data-stu-id="b6ada-226">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="b6ada-227">HttpContext. Request</span><span class="sxs-lookup"><span data-stu-id="b6ada-227">HttpContext.Request</span></span>

<span data-ttu-id="b6ada-228">**HttpContext. Request. HttpMethod** viene convertito in:</span><span class="sxs-lookup"><span data-stu-id="b6ada-228">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="b6ada-229">**HttpContext. Request. QueryString** viene convertito in:</span><span class="sxs-lookup"><span data-stu-id="b6ada-229">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="b6ada-230">**HttpContext. Request. URL** e **HttpContext. Request. RawUrl** vengono convertiti in:</span><span class="sxs-lookup"><span data-stu-id="b6ada-230">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="b6ada-231">**HttpContext. Request. IsSecureConnection** viene convertito in:</span><span class="sxs-lookup"><span data-stu-id="b6ada-231">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="b6ada-232">**HttpContext. Request. UserHostAddress** viene convertito in:</span><span class="sxs-lookup"><span data-stu-id="b6ada-232">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="b6ada-233">**HttpContext. Request. cookies** viene convertito in:</span><span class="sxs-lookup"><span data-stu-id="b6ada-233">**HttpContext.Request.Cookies** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

<span data-ttu-id="b6ada-234">**HttpContext. Request. RequestContext. RouteData** viene convertito in:</span><span class="sxs-lookup"><span data-stu-id="b6ada-234">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="b6ada-235">**HttpContext. Request. Headers** viene convertito in:</span><span class="sxs-lookup"><span data-stu-id="b6ada-235">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="b6ada-236">**HttpContext. Request. UserAgent** viene convertito in:</span><span class="sxs-lookup"><span data-stu-id="b6ada-236">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="b6ada-237">**HttpContext. Request. UrlReferrer** viene convertito in:</span><span class="sxs-lookup"><span data-stu-id="b6ada-237">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="b6ada-238">**HttpContext. Request. ContentType** viene convertito in:</span><span class="sxs-lookup"><span data-stu-id="b6ada-238">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="b6ada-239">**HttpContext. Request. Form** viene convertito in:</span><span class="sxs-lookup"><span data-stu-id="b6ada-239">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="b6ada-240">Leggere i valori del modulo solo se il sottotipo di contenuto è *x-www-form-urlencoded* o *form-data*.</span><span class="sxs-lookup"><span data-stu-id="b6ada-240">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="b6ada-241">**HttpContext. Request. InputStream** viene convertito in:</span><span class="sxs-lookup"><span data-stu-id="b6ada-241">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="b6ada-242">Usare questo codice solo in un middleware di tipo gestore, alla fine di una pipeline.</span><span class="sxs-lookup"><span data-stu-id="b6ada-242">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="b6ada-243">È possibile leggere il corpo non elaborato come illustrato sopra solo una volta per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="b6ada-243">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="b6ada-244">Il middleware che tenta di leggere il corpo dopo la prima lettura leggerà un corpo vuoto.</span><span class="sxs-lookup"><span data-stu-id="b6ada-244">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="b6ada-245">Questa operazione non si applica alla lettura di un modulo, come illustrato in precedenza, perché viene eseguita da un buffer.</span><span class="sxs-lookup"><span data-stu-id="b6ada-245">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="b6ada-246">HttpContext. Response</span><span class="sxs-lookup"><span data-stu-id="b6ada-246">HttpContext.Response</span></span>

<span data-ttu-id="b6ada-247">**HttpContext. Response. status** e **HttpContext. Response. StatusDescription** vengono convertiti in:</span><span class="sxs-lookup"><span data-stu-id="b6ada-247">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="b6ada-248">**HttpContext. Response. ContentEncoding** e **HttpContext. Response. ContentType** sono convertibili in:</span><span class="sxs-lookup"><span data-stu-id="b6ada-248">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="b6ada-249">Anche **HttpContext. Response. ContentType** viene convertito in:</span><span class="sxs-lookup"><span data-stu-id="b6ada-249">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="b6ada-250">**HttpContext. Response. output** viene convertito in:</span><span class="sxs-lookup"><span data-stu-id="b6ada-250">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="b6ada-251">**HttpContext. Response. TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="b6ada-251">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="b6ada-252">[Qui](../fundamentals/request-features.md#middleware-and-request-features)viene illustrato come servire un file.</span><span class="sxs-lookup"><span data-stu-id="b6ada-252">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="b6ada-253">**HttpContext. Response. Headers**</span><span class="sxs-lookup"><span data-stu-id="b6ada-253">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="b6ada-254">L'invio di intestazioni di risposta è complicato dal fatto che, se si impostano elementi che sono stati scritti nel corpo della risposta, non verranno inviati.</span><span class="sxs-lookup"><span data-stu-id="b6ada-254">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="b6ada-255">La soluzione consiste nell'impostare un metodo di callback che verrà chiamato immediatamente prima di iniziare la scrittura della risposta.</span><span class="sxs-lookup"><span data-stu-id="b6ada-255">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="b6ada-256">Questa operazione viene eseguita in modo ottimale all'inizio del `Invoke` metodo nel middleware.</span><span class="sxs-lookup"><span data-stu-id="b6ada-256">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="b6ada-257">Si tratta di un metodo di callback che imposta le intestazioni della risposta.</span><span class="sxs-lookup"><span data-stu-id="b6ada-257">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="b6ada-258">Il codice seguente imposta un metodo di callback denominato `SetHeaders` :</span><span class="sxs-lookup"><span data-stu-id="b6ada-258">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="b6ada-259">Il `SetHeaders` metodo di callback avrà un aspetto simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="b6ada-259">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="b6ada-260">**HttpContext. Response. cookies**</span><span class="sxs-lookup"><span data-stu-id="b6ada-260">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="b6ada-261">I cookie passano al browser in un'intestazione di risposta *set-cookie* .</span><span class="sxs-lookup"><span data-stu-id="b6ada-261">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="b6ada-262">Di conseguenza, l'invio di cookie richiede lo stesso callback utilizzato per inviare le intestazioni di risposta:</span><span class="sxs-lookup"><span data-stu-id="b6ada-262">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="b6ada-263">Il `SetCookies` metodo di callback avrà un aspetto simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="b6ada-263">The `SetCookies` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a><span data-ttu-id="b6ada-264">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b6ada-264">Additional resources</span></span>

* [<span data-ttu-id="b6ada-265">Cenni preliminari sui gestori HTTP e sui moduli HTTP</span><span class="sxs-lookup"><span data-stu-id="b6ada-265">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="b6ada-266">Configuration</span><span class="sxs-lookup"><span data-stu-id="b6ada-266">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="b6ada-267">Avvio dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="b6ada-267">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="b6ada-268">Middleware</span><span class="sxs-lookup"><span data-stu-id="b6ada-268">Middleware</span></span>](xref:fundamentals/middleware/index)
