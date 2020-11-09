---
title: Inserimento delle dipendenze nei gestori di requisiti in ASP.NET Core
author: rick-anderson
description: Informazioni su come inserire i gestori dei requisiti di autorizzazione in un'app ASP.NET Core usando l'inserimento di dipendenze.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 6598a9c9cfd1e6597fffcc1aa0c53fa493532458
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060261"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="1e4b1-103">Inserimento delle dipendenze nei gestori di requisiti in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1e4b1-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="1e4b1-104">I [gestori di autorizzazione devono essere registrati](xref:security/authorization/policies#handler-registration) nella raccolta di servizi durante la configurazione tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1e4b1-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration using [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="1e4b1-105">Si supponga di avere un repository di regole che si desidera valutare all'interno di un gestore autorizzazioni e che il repository sia stato registrato nella raccolta di servizi.</span><span class="sxs-lookup"><span data-stu-id="1e4b1-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="1e4b1-106">L'autorizzazione lo risolve e inserisce nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="1e4b1-106">Authorization resolves and injects that into the constructor.</span></span>

<span data-ttu-id="1e4b1-107">Ad esempio, per utilizzare ASP. Infrastruttura di registrazione di NET, inserire `ILoggerFactory` nel gestore.</span><span class="sxs-lookup"><span data-stu-id="1e4b1-107">For example, to use ASP.NET's logging infrastructure, inject `ILoggerFactory` into the handler.</span></span> <span data-ttu-id="1e4b1-108">Questo gestore potrebbe avere un aspetto simile al codice seguente:</span><span class="sxs-lookup"><span data-stu-id="1e4b1-108">Such a handler might look like the following code:</span></span>

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

<span data-ttu-id="1e4b1-109">Il gestore precedente può essere registrato con qualsiasi [durata del servizio](/dotnet/core/extensions/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="1e4b1-109">The preceding handler can be registered with any [service lifetime](/dotnet/core/extensions/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="1e4b1-110">Il codice seguente usa `AddSingleton` per registrare il gestore precedente:</span><span class="sxs-lookup"><span data-stu-id="1e4b1-110">The following code uses `AddSingleton` to register the preceding handler:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="1e4b1-111">Quando l'app viene avviata, viene creata un'istanza del gestore che inserisce l'oggetto registrato `ILoggerFactory` nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="1e4b1-111">An instance of the handler is created when the app starts, and DI injects the registered `ILoggerFactory` into the constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="1e4b1-112">I gestori che usano Entity Framework non devono essere registrati come singleton.</span><span class="sxs-lookup"><span data-stu-id="1e4b1-112">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
