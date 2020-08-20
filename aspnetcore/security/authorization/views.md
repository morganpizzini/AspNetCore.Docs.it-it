---
title: Autorizzazione basata sulla visualizzazione in ASP.NET Core MVC
author: rick-anderson
description: In questo documento viene illustrato come inserire e utilizzare il servizio di autorizzazione all'interno di una Razor vista ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/views
ms.openlocfilehash: 775ebdffe2b0753de18bf07d9ff1193235a45b17
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629886"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a>Autorizzazione basata sulla visualizzazione in ASP.NET Core MVC

Spesso uno sviluppatore desidera visualizzare, nascondere o modificare un'interfaccia utente in base all'identità dell'utente corrente. È possibile accedere al servizio di autorizzazione all'interno delle visualizzazioni MVC tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection). Per inserire il servizio di autorizzazione in una Razor vista, utilizzare la `@inject` direttiva:

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

Se si vuole che il servizio di autorizzazione sia in ogni visualizzazione, inserire la `@inject` direttiva nel file *_ViewImports. cshtml* della directory *views* . Per altre informazioni, vedere [Inserimento di dipendenze in visualizzazioni](xref:mvc/views/dependency-injection).

Usare il servizio di autorizzazione inserito per richiamare `AuthorizeAsync` esattamente nello stesso modo in cui si verifica durante l' [autorizzazione basata sulle risorse](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

In alcuni casi, la risorsa sarà il modello di visualizzazione. Richiama `AuthorizeAsync` esattamente nello stesso modo in cui si verificherà durante l' [autorizzazione basata sulle risorse](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

Nel codice precedente, il modello viene passato come una risorsa che deve prendere in considerazione la valutazione dei criteri.

> [!WARNING]
> Non fare affidamento sulla visibilità degli elementi dell'interfaccia utente dell'app come unico controllo di autorizzazione. Nascondere un elemento dell'interfaccia utente potrebbe non impedire completamente l'accesso all'azione del controller associato. Si consideri ad esempio il pulsante nel frammento di codice precedente. Un utente può richiamare il `Edit` metodo di azione se sa che l'URL relativo della risorsa è */Document/Edit/1*. Per questo motivo, il `Edit` metodo di azione deve eseguire il proprio controllo di autorizzazione.
