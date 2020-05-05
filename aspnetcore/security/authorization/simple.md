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
ms.openlocfilehash: f273c3e9db74fa63de85c65d94223d0ef7326036
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775635"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="a9fb7-103">Autorizzazione semplice in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a9fb7-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="a9fb7-104">L'autorizzazione in MVC viene controllata tramite `AuthorizeAttribute` l'attributo e i vari parametri.</span><span class="sxs-lookup"><span data-stu-id="a9fb7-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="a9fb7-105">Al livello più semplice, l'applicazione `AuthorizeAttribute` dell'attributo a un controller o a un'azione limita l'accesso al controller o all'azione a qualsiasi utente autenticato.</span><span class="sxs-lookup"><span data-stu-id="a9fb7-105">At its simplest, applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="a9fb7-106">Il codice seguente, ad esempio, limita l'accesso `AccountController` a a qualsiasi utente autenticato.</span><span class="sxs-lookup"><span data-stu-id="a9fb7-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="a9fb7-107">Se si desidera applicare l'autorizzazione a un'azione anziché al controller, applicare l' `AuthorizeAttribute` attributo all'azione stessa:</span><span class="sxs-lookup"><span data-stu-id="a9fb7-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="a9fb7-108">A questo punto, solo gli utenti autenticati possono accedere alla `Logout` funzione.</span><span class="sxs-lookup"><span data-stu-id="a9fb7-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="a9fb7-109">È anche possibile usare l' `AllowAnonymous` attributo per consentire l'accesso da parte di utenti non autenticati a singole azioni.</span><span class="sxs-lookup"><span data-stu-id="a9fb7-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="a9fb7-110">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a9fb7-110">For example:</span></span>

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

<span data-ttu-id="a9fb7-111">Ciò consente solo agli utenti autenticati di `AccountController`, ad eccezione dell' `Login` azione, accessibile da parte di tutti, indipendentemente dallo stato autenticato o non autenticato/anonimo.</span><span class="sxs-lookup"><span data-stu-id="a9fb7-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="a9fb7-112">`[AllowAnonymous]`Ignora tutte le istruzioni di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="a9fb7-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="a9fb7-113">Se si combinano `[AllowAnonymous]` e `[Authorize]` qualsiasi attributo, `[Authorize]` gli attributi vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="a9fb7-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="a9fb7-114">Se ad esempio si applica `[AllowAnonymous]` a livello di controller, tutti `[Authorize]` gli attributi sullo stesso controller (o su qualsiasi azione al suo interno) vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="a9fb7-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
