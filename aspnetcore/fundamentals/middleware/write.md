---
title: Scrivere middleware di ASP.NET Core personalizzato
author: rick-anderson
description: Informazioni su come scrivere middleware di ASP.NET Core personalizzato.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/18/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/write
ms.openlocfilehash: 480120718959a364ce8008949c3b76c558c0e995
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130587"
---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="8be2a-103">Scrivere middleware di ASP.NET Core personalizzato</span><span class="sxs-lookup"><span data-stu-id="8be2a-103">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="8be2a-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8be2a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8be2a-105">Il middleware è un software che viene assemblato in una pipeline dell'app per gestire richieste e risposte.</span><span class="sxs-lookup"><span data-stu-id="8be2a-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="8be2a-106">ASP.NET Core offre un ampio set di componenti middleware integrati, ma in alcuni scenari si potrebbe voler scrivere un middleware personalizzato.</span><span class="sxs-lookup"><span data-stu-id="8be2a-106">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="8be2a-107">In questo argomento viene descritto come scrivere un middleware *basato sulle convenzioni* .</span><span class="sxs-lookup"><span data-stu-id="8be2a-107">This topic describes how to write *convention-based* middleware.</span></span> <span data-ttu-id="8be2a-108">Per un approccio che usa la tipizzazione forte e l'attivazione per richiesta, vedere <xref:fundamentals/middleware/extensibility> .</span><span class="sxs-lookup"><span data-stu-id="8be2a-108">For an approach that uses strong typing and per-request activation, see <xref:fundamentals/middleware/extensibility>.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="8be2a-109">Classe middleware</span><span class="sxs-lookup"><span data-stu-id="8be2a-109">Middleware class</span></span>

<span data-ttu-id="8be2a-110">Il middleware è in genere incapsulato in una classe ed esposto con un metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="8be2a-110">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="8be2a-111">Si consideri il middleware seguente, che specifica le impostazioni cultura per la richiesta corrente da una stringa di query:</span><span class="sxs-lookup"><span data-stu-id="8be2a-111">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](write/snapshot/StartupCulture.cs)]

<span data-ttu-id="8be2a-112">Il codice di esempio precedente viene usato per illustrare la creazione di un componente middleware.</span><span class="sxs-lookup"><span data-stu-id="8be2a-112">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="8be2a-113">Per il supporto di localizzazione incorporato di ASP.NET Core, vedere <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="8be2a-113">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="8be2a-114">Testare il middleware passando le impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="8be2a-114">Test the middleware by passing in the culture.</span></span> <span data-ttu-id="8be2a-115">Ad esempio, richiedere `https://localhost:5001/?culture=no`.</span><span class="sxs-lookup"><span data-stu-id="8be2a-115">For example, request `https://localhost:5001/?culture=no`.</span></span>

<span data-ttu-id="8be2a-116">Il codice seguente sposta il delegato middleware in una classe:</span><span class="sxs-lookup"><span data-stu-id="8be2a-116">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

<span data-ttu-id="8be2a-117">La classe middleware deve includere:</span><span class="sxs-lookup"><span data-stu-id="8be2a-117">The middleware class must include:</span></span>

* <span data-ttu-id="8be2a-118">Un costruttore pubblico con un parametro di tipo <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span><span class="sxs-lookup"><span data-stu-id="8be2a-118">A public constructor with a parameter of type <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="8be2a-119">Un metodo pubblico denominato `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="8be2a-119">A public method named `Invoke` or `InvokeAsync`.</span></span> <span data-ttu-id="8be2a-120">Questo metodo deve:</span><span class="sxs-lookup"><span data-stu-id="8be2a-120">This method must:</span></span>
  * <span data-ttu-id="8be2a-121">Restituire `Task`.</span><span class="sxs-lookup"><span data-stu-id="8be2a-121">Return a `Task`.</span></span>
  * <span data-ttu-id="8be2a-122">Accettare un primo parametro di tipo <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="8be2a-122">Accept a first parameter of type <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>
  
<span data-ttu-id="8be2a-123">I parametri aggiuntivi per il costruttore e `Invoke`/`InvokeAsync` vengono popolati dall'[inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8be2a-123">Additional parameters for the constructor and `Invoke`/`InvokeAsync` are populated by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware-dependencies"></a><span data-ttu-id="8be2a-124">Dipendenze del middleware</span><span class="sxs-lookup"><span data-stu-id="8be2a-124">Middleware dependencies</span></span>

<span data-ttu-id="8be2a-125">Il middleware deve seguire il [principio delle dipendenze esplicite](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) esponendo le dipendenze nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="8be2a-125">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="8be2a-126">Il middleware viene costruito una volta per ogni *durata applicazione*.</span><span class="sxs-lookup"><span data-stu-id="8be2a-126">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="8be2a-127">Se è necessario condividere servizi con il middleware all'interno di una richiesta, vedere la sezione [Dipendenze del middleware per richiesta](#per-request-middleware-dependencies).</span><span class="sxs-lookup"><span data-stu-id="8be2a-127">See the [Per-request middleware dependencies](#per-request-middleware-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="8be2a-128">I componenti middleware possono risolvere le dipendenze dall'[inserimento di dipendenze](xref:fundamentals/dependency-injection) mediante i parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="8be2a-128">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="8be2a-129">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) può anche accettare direttamente parametri aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="8be2a-129">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-middleware-dependencies"></a><span data-ttu-id="8be2a-130">Dipendenze del middleware per richiesta</span><span class="sxs-lookup"><span data-stu-id="8be2a-130">Per-request middleware dependencies</span></span>

<span data-ttu-id="8be2a-131">Poiché il middleware viene creato all'avvio dell'app e non per richiesta, i servizi di durata *con ambito* usati dai costruttori del middleware non vengono condivisi con altri tipi di inserimento di dipendenze durante ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="8be2a-131">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="8be2a-132">Se è necessario condividere un servizio *con ambito* tra il proprio middleware e altri tipi, aggiungere i servizi alla firma del metodo `Invoke`.</span><span class="sxs-lookup"><span data-stu-id="8be2a-132">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="8be2a-133">Il metodo `Invoke` può accettare parametri aggiuntivi popolati dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="8be2a-133">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

<span data-ttu-id="8be2a-134">Le [Opzioni durata e registrazione](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contengono un esempio completo di middleware con servizi di durata con *ambito* .</span><span class="sxs-lookup"><span data-stu-id="8be2a-134">[Lifetime and registration options](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contains a complete sample of middleware with *scoped* lifetime services.</span></span>

## <a name="middleware-extension-method"></a><span data-ttu-id="8be2a-135">Metodo di estensione del middleware</span><span class="sxs-lookup"><span data-stu-id="8be2a-135">Middleware extension method</span></span>

<span data-ttu-id="8be2a-136">Il metodo di estensione seguente espone il middleware tramite <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span><span class="sxs-lookup"><span data-stu-id="8be2a-136">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="8be2a-137">Il codice seguente chiama il middleware da `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="8be2a-137">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a><span data-ttu-id="8be2a-138">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8be2a-138">Additional resources</span></span>

* <span data-ttu-id="8be2a-139">Il [ciclo di vita e le opzioni di registrazione](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contengono un esempio completo di middleware con servizi di durata con *ambito*, *temporaneo*e *singleton* .</span><span class="sxs-lookup"><span data-stu-id="8be2a-139">[Lifetime and registration options](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contains a complete sample of middleware with *scoped*, *transient*, and *singleton* lifetime services.</span></span>
* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
