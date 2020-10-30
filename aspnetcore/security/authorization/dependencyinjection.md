---
title: Inserimento delle dipendenze nei gestori di requisiti in ASP.NET Core
author: rick-anderson
description: Informazioni su come inserire i gestori dei requisiti di autorizzazione in un'app ASP.NET Core usando l'inserimento di dipendenze.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 6598a9c9cfd1e6597fffcc1aa0c53fa493532458
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060261"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>Inserimento delle dipendenze nei gestori di requisiti in ASP.NET Core

<a name="security-authorization-di"></a>

I [gestori di autorizzazione devono essere registrati](xref:security/authorization/policies#handler-registration) nella raccolta di servizi durante la configurazione tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).

Si supponga di avere un repository di regole che si desidera valutare all'interno di un gestore autorizzazioni e che il repository sia stato registrato nella raccolta di servizi. L'autorizzazione lo risolve e inserisce nel costruttore.

Ad esempio, per utilizzare ASP. Infrastruttura di registrazione di NET, inserire `ILoggerFactory` nel gestore. Questo gestore potrebbe avere un aspetto simile al codice seguente:

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

Il gestore precedente può essere registrato con qualsiasi [durata del servizio](/dotnet/core/extensions/dependency-injection#service-lifetimes). Il codice seguente usa `AddSingleton` per registrare il gestore precedente:

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

Quando l'app viene avviata, viene creata un'istanza del gestore che inserisce l'oggetto registrato `ILoggerFactory` nel costruttore.

> [!NOTE]
> I gestori che usano Entity Framework non devono essere registrati come singleton.
