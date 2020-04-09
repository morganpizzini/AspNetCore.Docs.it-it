---
title: Novità di ASP.NET Core 3.1
author: rick-anderson
description: Scopri le nuove funzionalità di ASP.NET Core 3.1.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: f375022ad3ebdea2990f626320ef295926f88c22
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662708"
---
# <a name="whats-new-in-aspnet-core-31"></a>Novità di ASP.NET Core 3.1

In questo articolo vengono evidenziate le modifiche più significative in ASP.NET Core 3.1 con collegamenti alla documentazione pertinente.

## <a name="partial-class-support-for-razor-components"></a>Supporto parziale della classe per i componenti RazorPartial class support for Razor components

I componenti Razor vengono ora generati come classi parziali. Il codice per un componente Razor può essere scritto utilizzando un file code-behind definito come classe parziale anziché definire tutto il codice per il componente in un singolo file. Per ulteriori informazioni, vedere [Supporto di classi parziali](xref:blazor/components#partial-class-support).

## <a name="opno-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>BlazorComponent Tag Helper e passaggio di parametri ai componenti di primo livello

Con Blazor ASP.NET Core 3.0, il rendering dei componenti è`Html.RenderComponentAsync`stato eseguito in pagine e visualizzazioni utilizzando un HTML Helper ( ). In ASP.NET Core 3.1, render a component from a page or view with the new Component Tag Helper:

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

Html Helper rimane supportato in ASP.NET Core 3.1, ma è consigliabile Component Tag Helper.

BlazorLe app server possono ora passare parametri ai componenti di primo livello durante il rendering iniziale. In precedenza era possibile passare parametri solo a un componente di primo livello con [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static). Con questa versione, sono supportati sia [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) che [RenderModel.ServerPrerendered.With this release, both RenderMode.Server and RenderModel.ServerPrerendered are supported.](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) Tutti i valori di parametro specificati vengono serializzati come JSON e inclusi nella risposta iniziale.

Ad esempio, eseguire `Counter` il prerendering`IncrementAmount`di un componente con un importo di incremento ( ):

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

Per ulteriori informazioni, vedere [Integrare componenti nelle pagine Razor e nelle app MVC.](xref:blazor/integrate-components)

## <a name="support-for-shared-queues-in-httpsys"></a>Supporto per code condivise in HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) supporta la creazione di code di richieste anonime. In ASP.NET Core 3.1 è stata aggiunta la possibilità di creare o collegare una coda di richieste HTTP.sys denominata esistente. La creazione o il collegamento a una coda di richieste HTTP.sys denominata esistente abilita scenari in cui il processo del controller HTTP.sys proprietario della coda è indipendente dal processo del listener. Questa indipendenza consente di mantenere le connessioni esistenti e le richieste accodate tra i riavvii del processo del listener:This independence makes it possible to preserve existing connections and enqueued requests between listener process restarts:

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a>Modifiche di rilievo per i cookie SameSite

Il comportamento dei cookie SameSite è cambiato per riflettere le prossime modifiche apportate al browser. Ciò può influire sugli scenari di autenticazione come AzureAd, OpenIdConnect o WsFederation.This may affect authentication scenarios like AzureAd, OpenIdConnect, or WsFederation. Per altre informazioni, vedere <xref:security/samesite>.

## <a name="prevent-default-actions-for-events-in-opno-locblazor-apps"></a>Impedisci azioni predefinite Blazor per gli eventi nelle app

Utilizzare `@on{EVENT}:preventDefault` l'attributo della direttiva per impedire l'azione predefinita per un evento. Nell'esempio seguente, l'azione predefinita di visualizzazione del carattere del tasto nella casella di testo è impedita:

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

Per ulteriori informazioni, consultate [Impedire azioni predefinite.](xref:blazor/event-handling#prevent-default-actions)

## <a name="stop-event-propagation-in-opno-locblazor-apps"></a>Interrompere la propagazione degli eventi nelle Blazor app

Utilizzare `@on{EVENT}:stopPropagation` l'attributo della direttiva per interrompere la propagazione degli eventi. Nell'esempio seguente, la selezione della casella `<div>` di controllo impedisce `<div>`la propagazione degli eventi Click dall'elemento figlio all'elemento padre :

```razor
<input @bind="_stopPropagation" type="checkbox" />

<div @onclick="OnSelectParentDiv">
    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        ...
    </div>
</div>

@code {
    private bool _stopPropagation = false;
}
```

Per ulteriori informazioni, consultate Interrompere la [propagazione degli eventi.](xref:blazor/event-handling#stop-event-propagation)

## <a name="detailed-errors-during-opno-locblazor-app-development"></a>Errori dettagliati Blazor durante lo sviluppo dell'appDetailed errors during app development

Quando Blazor un'app non funziona correttamente durante lo sviluppo, la ricezione di informazioni dettagliate sull'errore dall'app consente di risolvere il problema. Quando si verifica Blazor un errore, le app visualizzano una barra d'oro nella parte inferiore dello schermo:

* Durante lo sviluppo, la barra d'oro ti indirizza alla console del browser, dove puoi vedere l'eccezione.
* In produzione, la barra d'oro notifica all'utente che si è verificato un errore e consiglia di aggiornare il browser.

Per ulteriori informazioni, vedere [Errori dettagliati durante lo sviluppo](xref:blazor/handle-errors#detailed-errors-during-development).
