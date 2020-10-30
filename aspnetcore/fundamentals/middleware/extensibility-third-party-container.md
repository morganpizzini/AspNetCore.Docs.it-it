---
title: Attivazione del middleware con un contenitore di terze parti in ASP.NET Core
author: rick-anderson
description: Informazioni su come usare un middleware fortemente tipizzato con un'attivazione basata su factory e un contenitore di terze parti in ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: d322b1b3dda161c9948359253c3f7fee64a1f9ce
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057765"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="50a2c-103">Attivazione del middleware con un contenitore di terze parti in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="50a2c-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="50a2c-104">In questo articolo viene illustrato come usare <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> e <xref:Microsoft.AspNetCore.Http.IMiddleware> come punto di estendibilità per l'attivazione del [middleware](xref:fundamentals/middleware/index) con un contenitore di terze parti.</span><span class="sxs-lookup"><span data-stu-id="50a2c-104">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="50a2c-105">Per informazioni introduttive su `IMiddlewareFactory` e `IMiddleware`, vedere <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="50a2c-105">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="50a2c-106">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="50a2c-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="50a2c-107">L'app di esempio illustra l'attivazione del middleware da parte di un'implementazione `IMiddlewareFactory`, `SimpleInjectorMiddlewareFactory`.</span><span class="sxs-lookup"><span data-stu-id="50a2c-107">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="50a2c-108">Nell'esempio viene utilizzato il contenitore di inserimento delle dipendenze [Simple Injector](https://simpleinjector.org).</span><span class="sxs-lookup"><span data-stu-id="50a2c-108">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="50a2c-109">L'implementazione del middleware nell'esempio registra il valore fornito da un parametro stringa di query (`key`).</span><span class="sxs-lookup"><span data-stu-id="50a2c-109">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="50a2c-110">Il middleware usa un contesto di database inserito, ovvero un servizio con ambito, per registrare il valore della stringa di query in un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="50a2c-110">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="50a2c-111">Nell'app di esempio viene utilizzato [Simple Injector](https://github.com/simpleinjector/SimpleInjector) esclusivamente a scopi dimostrativi.</span><span class="sxs-lookup"><span data-stu-id="50a2c-111">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="50a2c-112">L'utilizzo di Simple Injector non ne implica la sponsorizzazione.</span><span class="sxs-lookup"><span data-stu-id="50a2c-112">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="50a2c-113">Le modalità di attivazione del middleware descritte nella documentazione di Simple Injector e i problemi in GitHub sono consigliati dai gestori di Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="50a2c-113">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="50a2c-114">Per ulteriori informazioni, vedere la [documentazione di Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) e il [repository GitHub su Simple Injector](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="50a2c-114">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="50a2c-115">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="50a2c-115">IMiddlewareFactory</span></span>

<span data-ttu-id="50a2c-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> specifica i metodi per creare il middleware.</span><span class="sxs-lookup"><span data-stu-id="50a2c-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="50a2c-117">Nell'app di esempio viene implementato il middleware basato su factory per creare un'istanza di `SimpleInjectorActivatedMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="50a2c-117">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="50a2c-118">Il middleware basato su factory usa il contenitore Simple Injector per risolvere il middleware:</span><span class="sxs-lookup"><span data-stu-id="50a2c-118">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="50a2c-119">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="50a2c-119">IMiddleware</span></span>

<span data-ttu-id="50a2c-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> definisce il middleware per la pipeline di richieste dell'app.</span><span class="sxs-lookup"><span data-stu-id="50a2c-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="50a2c-121">Middleware attivato da un'implementazione `IMiddlewareFactory` ( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):</span><span class="sxs-lookup"><span data-stu-id="50a2c-121">Middleware activated by an `IMiddlewareFactory` implementation ( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="50a2c-122">Per il middleware viene creata un'estensione ( *Middleware/MiddlewareExtensions.cs* ):</span><span class="sxs-lookup"><span data-stu-id="50a2c-122">An extension is created for the middleware ( *Middleware/MiddlewareExtensions.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="50a2c-123">`Startup.ConfigureServices` deve eseguire diverse attività:</span><span class="sxs-lookup"><span data-stu-id="50a2c-123">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="50a2c-124">Impostare il contenitore Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="50a2c-124">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="50a2c-125">Registrare la factory e il middleware.</span><span class="sxs-lookup"><span data-stu-id="50a2c-125">Register the factory and middleware.</span></span>
* <span data-ttu-id="50a2c-126">Rendere disponibile il contesto di database dell'app dal contenitore Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="50a2c-126">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="50a2c-127">Il middleware è registrato nella pipeline di elaborazione della richiesta in `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="50a2c-127">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="50a2c-128">In questo articolo viene illustrato come usare <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> e <xref:Microsoft.AspNetCore.Http.IMiddleware> come punto di estendibilità per l'attivazione del [middleware](xref:fundamentals/middleware/index) con un contenitore di terze parti.</span><span class="sxs-lookup"><span data-stu-id="50a2c-128">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="50a2c-129">Per informazioni introduttive su `IMiddlewareFactory` e `IMiddleware`, vedere <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="50a2c-129">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="50a2c-130">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="50a2c-130">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="50a2c-131">L'app di esempio illustra l'attivazione del middleware da parte di un'implementazione `IMiddlewareFactory`, `SimpleInjectorMiddlewareFactory`.</span><span class="sxs-lookup"><span data-stu-id="50a2c-131">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="50a2c-132">Nell'esempio viene utilizzato il contenitore di inserimento delle dipendenze [Simple Injector](https://simpleinjector.org).</span><span class="sxs-lookup"><span data-stu-id="50a2c-132">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="50a2c-133">L'implementazione del middleware nell'esempio registra il valore fornito da un parametro stringa di query (`key`).</span><span class="sxs-lookup"><span data-stu-id="50a2c-133">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="50a2c-134">Il middleware usa un contesto di database inserito, ovvero un servizio con ambito, per registrare il valore della stringa di query in un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="50a2c-134">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="50a2c-135">Nell'app di esempio viene utilizzato [Simple Injector](https://github.com/simpleinjector/SimpleInjector) esclusivamente a scopi dimostrativi.</span><span class="sxs-lookup"><span data-stu-id="50a2c-135">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="50a2c-136">L'utilizzo di Simple Injector non ne implica la sponsorizzazione.</span><span class="sxs-lookup"><span data-stu-id="50a2c-136">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="50a2c-137">Le modalità di attivazione del middleware descritte nella documentazione di Simple Injector e i problemi in GitHub sono consigliati dai gestori di Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="50a2c-137">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="50a2c-138">Per ulteriori informazioni, vedere la [documentazione di Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) e il [repository GitHub su Simple Injector](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="50a2c-138">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="50a2c-139">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="50a2c-139">IMiddlewareFactory</span></span>

<span data-ttu-id="50a2c-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> specifica i metodi per creare il middleware.</span><span class="sxs-lookup"><span data-stu-id="50a2c-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="50a2c-141">Nell'app di esempio viene implementato il middleware basato su factory per creare un'istanza di `SimpleInjectorActivatedMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="50a2c-141">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="50a2c-142">Il middleware basato su factory usa il contenitore Simple Injector per risolvere il middleware:</span><span class="sxs-lookup"><span data-stu-id="50a2c-142">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="50a2c-143">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="50a2c-143">IMiddleware</span></span>

<span data-ttu-id="50a2c-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> definisce il middleware per la pipeline di richieste dell'app.</span><span class="sxs-lookup"><span data-stu-id="50a2c-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="50a2c-145">Middleware attivato da un'implementazione `IMiddlewareFactory` ( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):</span><span class="sxs-lookup"><span data-stu-id="50a2c-145">Middleware activated by an `IMiddlewareFactory` implementation ( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="50a2c-146">Per il middleware viene creata un'estensione ( *Middleware/MiddlewareExtensions.cs* ):</span><span class="sxs-lookup"><span data-stu-id="50a2c-146">An extension is created for the middleware ( *Middleware/MiddlewareExtensions.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="50a2c-147">`Startup.ConfigureServices` deve eseguire diverse attività:</span><span class="sxs-lookup"><span data-stu-id="50a2c-147">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="50a2c-148">Impostare il contenitore Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="50a2c-148">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="50a2c-149">Registrare la factory e il middleware.</span><span class="sxs-lookup"><span data-stu-id="50a2c-149">Register the factory and middleware.</span></span>
* <span data-ttu-id="50a2c-150">Rendere disponibile il contesto di database dell'app dal contenitore Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="50a2c-150">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="50a2c-151">Il middleware è registrato nella pipeline di elaborazione della richiesta in `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="50a2c-151">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="50a2c-152">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="50a2c-152">Additional resources</span></span>

* [<span data-ttu-id="50a2c-153">Middleware</span><span class="sxs-lookup"><span data-stu-id="50a2c-153">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="50a2c-154">Attivazione del middleware basata sulla factory</span><span class="sxs-lookup"><span data-stu-id="50a2c-154">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="50a2c-155">Repository di GitHub su Simple Injector</span><span class="sxs-lookup"><span data-stu-id="50a2c-155">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="50a2c-156">Documentazione di Simple Injector</span><span class="sxs-lookup"><span data-stu-id="50a2c-156">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
