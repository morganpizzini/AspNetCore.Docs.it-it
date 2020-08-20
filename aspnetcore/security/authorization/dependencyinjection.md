---
title: Inserimento delle dipendenze nei gestori di requisiti in ASP.NET Core
author: rick-anderson
description: Informazioni su come inserire i gestori dei requisiti di autorizzazione in un'app ASP.NET Core usando l'inserimento di dipendenze.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 4bc7eb38262c8a94a84aacc978737a778bfd71a1
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632564"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="dcde8-103">Inserimento delle dipendenze nei gestori di requisiti in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dcde8-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="dcde8-104">I [gestori di autorizzazione devono essere registrati](xref:security/authorization/policies#handler-registration) nella raccolta di servizi durante la configurazione (usando l' [inserimento di dipendenze](xref:fundamentals/dependency-injection)).</span><span class="sxs-lookup"><span data-stu-id="dcde8-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration (using [dependency injection](xref:fundamentals/dependency-injection)).</span></span>

<span data-ttu-id="dcde8-105">Si supponga di avere un repository di regole che si desidera valutare all'interno di un gestore autorizzazioni e che il repository sia stato registrato nella raccolta di servizi.</span><span class="sxs-lookup"><span data-stu-id="dcde8-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="dcde8-106">L'autorizzazione lo risolve e inserisce nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="dcde8-106">Authorization will resolve and inject that into your constructor.</span></span>

<span data-ttu-id="dcde8-107">Ad esempio, se si desidera utilizzare ASP. Infrastruttura di registrazione di NET che si vuole inserire `ILoggerFactory` nel gestore.</span><span class="sxs-lookup"><span data-stu-id="dcde8-107">For example, if you wanted to use ASP.NET's logging infrastructure you would want to inject `ILoggerFactory` into your handler.</span></span> <span data-ttu-id="dcde8-108">Questo gestore potrebbe essere simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="dcde8-108">Such a handler might look like:</span></span>

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

<span data-ttu-id="dcde8-109">Registrare il gestore con `services.AddSingleton()` :</span><span class="sxs-lookup"><span data-stu-id="dcde8-109">You would register the handler with `services.AddSingleton()`:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="dcde8-110">Quando l'applicazione viene avviata, verrà creata un'istanza del gestore che inserisce il registrato `ILoggerFactory` nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="dcde8-110">An instance of the handler will be created when your application starts, and DI will inject the registered `ILoggerFactory` into your constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="dcde8-111">I gestori che usano Entity Framework non devono essere registrati come singleton.</span><span class="sxs-lookup"><span data-stu-id="dcde8-111">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
