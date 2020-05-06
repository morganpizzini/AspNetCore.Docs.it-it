---
title: Novità di ASP.NET Core 3,1
author: rick-anderson
description: Informazioni sulle nuove funzionalità di ASP.NET Core 3,1.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: 67fc972676549a02265035c129c513f11d303d51
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774047"
---
# <a name="whats-new-in-aspnet-core-31"></a>Novità di ASP.NET Core 3,1

In questo articolo vengono evidenziate le modifiche più significative in ASP.NET Core 3,1 con i collegamenti alla documentazione pertinente.

## <a name="partial-class-support-for-razor-components"></a>Supporto delle classi parziali per i Razor componenti

Razori componenti vengono ora generati come classi parziali. Il codice per Razor un componente può essere scritto utilizzando un file code-behind definito come classe parziale anziché definire tutto il codice per il componente in un unico file. Per ulteriori informazioni, vedere [supporto di classi parziali](xref:blazor/components#partial-class-support).

## <a name="blazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>BlazorHelper Tag Component e passare i parametri ai componenti di primo livello

In Blazor con ASP.NET Core 3,0 i componenti venivano sottoposti a rendering in pagine e visualizzazioni utilizzando un`Html.RenderComponentAsync`helper HTML (). In ASP.NET Core 3,1 eseguire il rendering di un componente da una pagina o da una vista con il nuovo Helper tag di componente:

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

L'helper HTML rimane supportato in ASP.NET Core 3,1, ma è consigliabile usare l'helper Tag Component.

BlazorLe app Server possono ora passare parametri ai componenti di primo livello durante il rendering iniziale. In precedenza era possibile passare i parametri solo a un componente di primo livello con [RenderMode. static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static). Con questa versione sono supportati sia [RenderMode. Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) che [RenderModel. ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) . Qualsiasi valore di parametro specificato viene serializzato come JSON e incluso nella risposta iniziale.

Ad esempio, prerenderizzare `Counter` un componente con un valore di`IncrementAmount`incremento ():

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

Per altre informazioni, vedere [integrare i componenti Razor in pagine e app MVC](xref:blazor/integrate-components).

## <a name="support-for-shared-queues-in-httpsys"></a>Supporto per le code condivise in HTTP. sys

[Http. sys](xref:fundamentals/servers/httpsys) supporta la creazione di code di richieste anonime. In ASP.NET Core 3,1 è stata aggiunta la possibilità di creare o connettersi a una coda di richieste HTTP. sys denominata esistente. La creazione o la connessione a una coda di richieste HTTP. sys denominata esistente Abilita scenari in cui il processo del controller HTTP. sys proprietario della coda è indipendente dal processo del listener. Questa indipendenza rende possibile la conservazione delle connessioni esistenti e delle richieste accodate tra i riavvii del processo del listener:

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a>Modifiche di rilievo per i cookie navigava sullostesso sito

Il comportamento dei cookie navigava sullostesso sito è stato modificato in modo da riflettere le modifiche imminenti del browser. Questo può influire su scenari di autenticazione come AzureAd, OpenIdConnect o WsFederation. Per altre informazioni, vedere <xref:security/samesite>.

## <a name="prevent-default-actions-for-events-in-blazor-apps"></a>Impedisci azioni predefinite per gli Blazor eventi nelle app

Utilizzare l' `@on{EVENT}:preventDefault` attributo Directive per impedire l'azione predefinita per un evento. Nell'esempio seguente viene impedita l'azione predefinita di visualizzazione del carattere della chiave nella casella di testo:

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

Per ulteriori informazioni, vedere [Impedisci azioni predefinite](xref:blazor/event-handling#prevent-default-actions).

## <a name="stop-event-propagation-in-blazor-apps"></a>Arresta propagazione eventi Blazor nelle app

Utilizzare l' `@on{EVENT}:stopPropagation` attributo Directive per arrestare la propagazione degli eventi. Nell'esempio seguente la selezione della casella di controllo impedisce la propagazione degli `<div>` eventi click dall'elemento figlio all' `<div>`elemento padre:

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

Per altre informazioni, vedere [arrestare la propagazione degli eventi](xref:blazor/event-handling#stop-event-propagation).

## <a name="detailed-errors-during-blazor-app-development"></a>Errori dettagliati Blazor durante lo sviluppo di app

Quando un' Blazor app non funziona correttamente durante lo sviluppo, la ricezione di informazioni dettagliate sugli errori dall'app è utile per la risoluzione dei problemi e per risolvere il problema. Quando si verifica un errore Blazor , le app visualizzano una barra dorata nella parte inferiore della schermata:

* Durante lo sviluppo, la barra dorata indirizza l'utente alla console del browser, in cui è possibile visualizzare l'eccezione.
* In produzione, la barra dorata informa l'utente che si è verificato un errore e consiglia di aggiornare il browser.

Per ulteriori informazioni, vedere [errori dettagliati durante lo sviluppo](xref:blazor/handle-errors#detailed-errors-during-development).
