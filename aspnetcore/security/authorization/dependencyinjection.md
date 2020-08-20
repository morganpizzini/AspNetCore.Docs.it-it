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
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>Inserimento delle dipendenze nei gestori di requisiti in ASP.NET Core

<a name="security-authorization-di"></a>

I [gestori di autorizzazione devono essere registrati](xref:security/authorization/policies#handler-registration) nella raccolta di servizi durante la configurazione (usando l' [inserimento di dipendenze](xref:fundamentals/dependency-injection)).

Si supponga di avere un repository di regole che si desidera valutare all'interno di un gestore autorizzazioni e che il repository sia stato registrato nella raccolta di servizi. L'autorizzazione lo risolve e inserisce nel costruttore.

Ad esempio, se si desidera utilizzare ASP. Infrastruttura di registrazione di NET che si vuole inserire `ILoggerFactory` nel gestore. Questo gestore potrebbe essere simile al seguente:

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

Registrare il gestore con `services.AddSingleton()` :

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

Quando l'applicazione viene avviata, verrà creata un'istanza del gestore che inserisce il registrato `ILoggerFactory` nel costruttore.

> [!NOTE]
> I gestori che usano Entity Framework non devono essere registrati come singleton.
