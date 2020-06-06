---
title: Autorizzazione semplice in ASP.NET Core
author: rick-anderson
description: Informazioni su come usare l'attributo di autorizzazione per limitare l'accesso a controller e azioni ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/simple
ms.openlocfilehash: 4ec31354d7fe11af75fd3a0045b4045f83721cb5
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84272125"
---
# <a name="simple-authorization-in-aspnet-core"></a>Autorizzazione semplice in ASP.NET Core

<a name="security-authorization-simple"></a>

L'autorizzazione in ASP.NET Core viene controllata con <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> e i vari parametri. Nella sua forma più semplice, applicando l' `[Authorize]` attributo a un controller, a un'azione o a una Razor pagina, è possibile limitare l'accesso a tale componente a qualsiasi utente autenticato.

Il codice seguente, ad esempio, limita l'accesso a `AccountController` a qualsiasi utente autenticato.

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

Se si desidera applicare l'autorizzazione a un'azione anziché al controller, applicare l' `AuthorizeAttribute` attributo all'azione stessa:

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

A questo punto, solo gli utenti autenticati possono accedere alla `Logout` funzione.

È anche possibile usare l' `AllowAnonymous` attributo per consentire l'accesso da parte di utenti non autenticati a singole azioni. Ad esempio:

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

Ciò consente solo agli utenti autenticati di `AccountController` , ad eccezione dell' `Login` azione, accessibile da parte di tutti, indipendentemente dallo stato autenticato o non autenticato/anonimo.

> [!WARNING]
> `[AllowAnonymous]`Ignora tutte le istruzioni di autorizzazione. Se si combinano `[AllowAnonymous]` e qualsiasi `[Authorize]` attributo, gli `[Authorize]` attributi vengono ignorati. Se ad esempio si applica `[AllowAnonymous]` a livello di controller, tutti `[Authorize]` gli attributi sullo stesso controller (o su qualsiasi azione al suo interno) vengono ignorati.
