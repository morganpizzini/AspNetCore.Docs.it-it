---
title: Accedere a HttpContext in ASP.NET Core
author: coderandhiker
description: Informazioni su come accedere a HttpContext in ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/5/2020
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
uid: fundamentals/httpcontext
ms.openlocfilehash: f51814d25d4e87d166c7b587306da6c77dbf047e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059975"
---
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="6b376-103">Accedere a HttpContext in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6b376-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="6b376-104">ASP.NET Core l'accesso alle app `HttpContext` tramite l' <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interfaccia e l'implementazione predefinita <xref:Microsoft.AspNetCore.Http.HttpContextAccessor> .</span><span class="sxs-lookup"><span data-stu-id="6b376-104">ASP.NET Core apps access `HttpContext` through the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface and its default implementation <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span></span> <span data-ttu-id="6b376-105">L'uso di `IHttpContextAccessor` è necessario solo per l'accesso a `HttpContext` all'interno di un servizio.</span><span class="sxs-lookup"><span data-stu-id="6b376-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

## <a name="use-httpcontext-from-no-locrazor-pages"></a><span data-ttu-id="6b376-106">USA HttpContext da :::no-loc(Razor)::: pagine</span><span class="sxs-lookup"><span data-stu-id="6b376-106">Use HttpContext from :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="6b376-107">Le :::no-loc(Razor)::: pagine espongono <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel> la <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel.HttpContext> proprietà:</span><span class="sxs-lookup"><span data-stu-id="6b376-107">The :::no-loc(Razor)::: Pages <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel> exposes the <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel.HttpContext> property:</span></span>

```csharp
public class AboutModel : PageModel
{
    public string Message { get; set; }

    public void OnGet()
    {
        Message = HttpContext.Request.PathBase;
    }
}
```

## <a name="use-httpcontext-from-a-no-locrazor-view"></a><span data-ttu-id="6b376-108">Usare HttpContext da una :::no-loc(Razor)::: vista</span><span class="sxs-lookup"><span data-stu-id="6b376-108">Use HttpContext from a :::no-loc(Razor)::: view</span></span>

<span data-ttu-id="6b376-109">:::no-loc(Razor):::le visualizzazioni espongono `HttpContext` direttamente tramite una proprietà [ :::no-loc(Razor)::: Page. Context](xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.Context) nella vista.</span><span class="sxs-lookup"><span data-stu-id="6b376-109">:::no-loc(Razor)::: views expose the `HttpContext` directly via a [:::no-loc(Razor):::Page.Context](xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.Context) property on the view.</span></span> <span data-ttu-id="6b376-110">Nell'esempio seguente viene recuperato il nome utente corrente in un'app Intranet utilizzando l'autenticazione di Windows:</span><span class="sxs-lookup"><span data-stu-id="6b376-110">The following example retrieves the current username in an intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.:::no-loc(Identity):::.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="6b376-111">Usare HttpContext da un controller</span><span class="sxs-lookup"><span data-stu-id="6b376-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="6b376-112">I controller espongono la proprietà [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext):</span><span class="sxs-lookup"><span data-stu-id="6b376-112">Controllers expose the [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) property:</span></span>

```csharp
public class HomeController : Controller
{
    public IActionResult About()
    {
        var pathBase = HttpContext.Request.PathBase;

        ...

        return View();
    }
}
```

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="6b376-113">Usare HttpContext dal middleware</span><span class="sxs-lookup"><span data-stu-id="6b376-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="6b376-114">Quando si utilizzano componenti middleware personalizzati, `HttpContext` viene passato nel metodo `Invoke` o `InvokeAsync` ed è accessibile quando viene configurato il middleware:</span><span class="sxs-lookup"><span data-stu-id="6b376-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="6b376-115">Usare HttpContext da componenti personalizzati</span><span class="sxs-lookup"><span data-stu-id="6b376-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="6b376-116">Per altri framework e componenti personalizzati che richiedono l'accesso a `HttpContext`, l'approccio consigliato consiste nel registrare una dipendenza tramite il contenitore predefinito di [inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6b376-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="6b376-117">Il contenitore di inserimento delle dipendenze fornisce `IHttpContextAccessor` a tutte le classi che lo dichiarano come dipendenza nei costruttori:</span><span class="sxs-lookup"><span data-stu-id="6b376-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddControllersWithViews();
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc()
         .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

<span data-ttu-id="6b376-118">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="6b376-118">In the following example:</span></span>

* <span data-ttu-id="6b376-119">`UserRepository` dichiara la dipendenza da `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="6b376-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="6b376-120">La dipendenza viene fornita quando l'inserimento delle dipendenze risolve la catena di dipendenze e crea un'istanza di `UserRepository`.</span><span class="sxs-lookup"><span data-stu-id="6b376-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

```csharp
public class UserRepository : IUserRepository
{
    private readonly IHttpContextAccessor _httpContextAccessor;

    public UserRepository(IHttpContextAccessor httpContextAccessor)
    {
        _httpContextAccessor = httpContextAccessor;
    }

    public void LogCurrentUser()
    {
        var username = _httpContextAccessor.HttpContext.User.:::no-loc(Identity):::.Name;
        service.LogAccessRequest(username);
    }
}
```

## <a name="httpcontext-access-from-a-background-thread"></a><span data-ttu-id="6b376-121">Accesso a HttpContext da un thread in background</span><span class="sxs-lookup"><span data-stu-id="6b376-121">HttpContext access from a background thread</span></span>

<span data-ttu-id="6b376-122">`HttpContext` non è thread-safe.</span><span class="sxs-lookup"><span data-stu-id="6b376-122">`HttpContext` isn't thread-safe.</span></span> <span data-ttu-id="6b376-123">La lettura o scrittura delle proprietà `HttpContext` di fuori dell'elaborazione di una richiesta può provocare un'eccezione <xref:System.NullReferenceException>.</span><span class="sxs-lookup"><span data-stu-id="6b376-123">Reading or writing properties of the `HttpContext` outside of processing a request can result in a <xref:System.NullReferenceException>.</span></span>

> [!NOTE]
> <span data-ttu-id="6b376-124">Se l'app genera errori sporadici `NullReferenceException` , esaminare parti del codice che avviano l'elaborazione in background o che continuano l'elaborazione dopo il completamento di una richiesta.</span><span class="sxs-lookup"><span data-stu-id="6b376-124">If your app generates sporadic `NullReferenceException` errors, review parts of the code that start background processing or that continue processing after a request completes.</span></span> <span data-ttu-id="6b376-125">Individuare gli errori, ad esempio definendo un metodo del controller come `async void` .</span><span class="sxs-lookup"><span data-stu-id="6b376-125">Look for mistakes, such as defining a controller method as `async void`.</span></span>

<span data-ttu-id="6b376-126">Per eseguire in modo sicuro operazioni in background con dati `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="6b376-126">To safely perform background work with `HttpContext` data:</span></span>

* <span data-ttu-id="6b376-127">Copiare i dati necessari durante l'elaborazione della richiesta.</span><span class="sxs-lookup"><span data-stu-id="6b376-127">Copy the required data during request processing.</span></span>
* <span data-ttu-id="6b376-128">Passare i dati copiati a un'attività in background.</span><span class="sxs-lookup"><span data-stu-id="6b376-128">Pass the copied data to a background task.</span></span>

<span data-ttu-id="6b376-129">Per evitare codice non sicuro, non passare mai a `HttpContext` un metodo che esegua il lavoro in background.</span><span class="sxs-lookup"><span data-stu-id="6b376-129">To avoid unsafe code, never pass the `HttpContext` into a method that performs background work.</span></span> <span data-ttu-id="6b376-130">Passare invece i dati necessari.</span><span class="sxs-lookup"><span data-stu-id="6b376-130">Pass the required data instead.</span></span> <span data-ttu-id="6b376-131">Nell'esempio seguente `SendEmailCore` viene chiamato per iniziare a inviare un messaggio di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="6b376-131">In the following example, `SendEmailCore` is called to start sending an email.</span></span> <span data-ttu-id="6b376-132">`correlationId`Viene passato a `SendEmailCore` , non a `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="6b376-132">The `correlationId` is passed to `SendEmailCore`, not the `HttpContext`.</span></span> <span data-ttu-id="6b376-133">L'esecuzione del codice non attende il `SendEmailCore` completamento:</span><span class="sxs-lookup"><span data-stu-id="6b376-133">Code execution doesn't wait for `SendEmailCore` to complete:</span></span>

```csharp
public class EmailController : Controller
{
    public IActionResult SendEmail(string email)
    {
        var correlationId = HttpContext.Request.Headers["x-correlation-id"].ToString();

        _ = SendEmailCore(correlationId);

        return View();
    }

    private async Task SendEmailCore(string correlationId)
    {
        ...
    }
}
```

## <a name="no-locblazor-and-shared-state"></a><span data-ttu-id="6b376-134">:::no-loc(Blazor)::: e stato condiviso</span><span class="sxs-lookup"><span data-stu-id="6b376-134">:::no-loc(Blazor)::: and shared state</span></span>

[!INCLUDE[](~/includes/blazor-security/blazor-shared-state.md)]
