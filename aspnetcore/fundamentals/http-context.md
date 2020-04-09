---
title: Accedere a HttpContext in ASP.NET Core
author: coderandhiker
description: Informazioni su come accedere a HttpContext in ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
uid: fundamentals/httpcontext
ms.openlocfilehash: 8a7ee180380c42ea745c91b8e6a18c1baa820220
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658746"
---
# <a name="access-httpcontext-in-aspnet-core"></a>Accedere a HttpContext in ASP.NET Core

ASP.NET Core `HttpContext` accedere <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> tramite l'interfaccia e la relativa implementazione <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>predefinita. L'uso di `IHttpContextAccessor` è necessario solo per l'accesso a `HttpContext` all'interno di un servizio.

## <a name="use-httpcontext-from-razor-pages"></a>Usare HttpContext da Razor Pages

Le pagine <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> Razor <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> espone la proprietà:The Razor Pages exposes the property:

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

## <a name="use-httpcontext-from-a-razor-view"></a>Usare HttpContext da una visualizzazione Razor

Le visualizzazioni Razor espongono `HttpContext` direttamente tramite una proprietà [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) nella visualizzazione. The following example retrieves the current username in an intranet app using Windows Authentication:

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a>Usare HttpContext da un controller

I controller espongono la proprietà [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext):

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

## <a name="use-httpcontext-from-middleware"></a>Usare HttpContext dal middleware

Quando si utilizzano componenti middleware personalizzati, `HttpContext` viene passato nel metodo `Invoke` o `InvokeAsync` ed è accessibile quando viene configurato il middleware:

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a>Usare HttpContext da componenti personalizzati

Per altri framework e componenti personalizzati che richiedono l'accesso a `HttpContext`, l'approccio consigliato consiste nel registrare una dipendenza tramite il contenitore predefinito di [inserimento delle dipendenze](xref:fundamentals/dependency-injection). Il contenitore di inserimento delle dipendenze fornisce l'oggetto `IHttpContextAccessor` a tutte le classi che lo dichiarano come dipendenza nei relativi costruttori:The dependency injection container supplies the to any classes that declare it as a dependency in their constructors:

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

Nell'esempio seguente:

* `UserRepository` dichiara la dipendenza da `IHttpContextAccessor`.
* La dipendenza viene fornita quando l'inserimento delle dipendenze risolve la catena di dipendenze e crea un'istanza di `UserRepository`.

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
        var username = _httpContextAccessor.HttpContext.User.Identity.Name;
        service.LogAccessRequest(username);
    }
}
```

## <a name="httpcontext-access-from-a-background-thread"></a>Accesso a HttpContext da un thread in background

`HttpContext`non è thread-safe. La lettura o scrittura delle proprietà `HttpContext` di fuori dell'elaborazione di una richiesta può provocare un'eccezione <xref:System.NullReferenceException>.

> [!NOTE]
> Se l'app genera `NullReferenceException` errori sporadici, esamina parti del codice che avviano l'elaborazione in background o che continuano l'elaborazione al termine di una richiesta. Cercare gli errori, ad esempio definire `async void`un metodo del controller come .

Per eseguire in modo sicuro operazioni in background con dati `HttpContext`:

* Copiare i dati necessari durante l'elaborazione della richiesta.
* Passare i dati copiati a un'attività in background.

Per evitare codice unsafe, non passare mai l'oggetto `HttpContext` in un metodo che esegue il lavoro in background. Passare invece i dati richiesti. Nell'esempio seguente `SendEmailCore` viene chiamato per avviare l'invio di un messaggio di posta elettronica. L'oggetto `correlationId` `SendEmailCore`viene passato `HttpContext`a , non al file . L'esecuzione del codice `SendEmailCore` non attende il completamento:Code execution doesn't wait for complete:

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
