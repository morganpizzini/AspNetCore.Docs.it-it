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
# <a name="whats-new-in-aspnet-core-31"></a><span data-ttu-id="14fc9-103">Novità di ASP.NET Core 3.1</span><span class="sxs-lookup"><span data-stu-id="14fc9-103">What's new in ASP.NET Core 3.1</span></span>

<span data-ttu-id="14fc9-104">In questo articolo vengono evidenziate le modifiche più significative in ASP.NET Core 3.1 con collegamenti alla documentazione pertinente.</span><span class="sxs-lookup"><span data-stu-id="14fc9-104">This article highlights the most significant changes in ASP.NET Core 3.1 with links to relevant documentation.</span></span>

## <a name="partial-class-support-for-razor-components"></a><span data-ttu-id="14fc9-105">Supporto parziale della classe per i componenti RazorPartial class support for Razor components</span><span class="sxs-lookup"><span data-stu-id="14fc9-105">Partial class support for Razor components</span></span>

<span data-ttu-id="14fc9-106">I componenti Razor vengono ora generati come classi parziali.</span><span class="sxs-lookup"><span data-stu-id="14fc9-106">Razor components are now generated as partial classes.</span></span> <span data-ttu-id="14fc9-107">Il codice per un componente Razor può essere scritto utilizzando un file code-behind definito come classe parziale anziché definire tutto il codice per il componente in un singolo file.</span><span class="sxs-lookup"><span data-stu-id="14fc9-107">Code for a Razor component can be written using a code-behind file defined as a partial class rather than defining all the code for the component in a single file.</span></span> <span data-ttu-id="14fc9-108">Per ulteriori informazioni, vedere [Supporto di classi parziali](xref:blazor/components#partial-class-support).</span><span class="sxs-lookup"><span data-stu-id="14fc9-108">For more information, see [Partial class support](xref:blazor/components#partial-class-support).</span></span>

## <a name="opno-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor<span data-ttu-id="14fc9-109">Component Tag Helper e passaggio di parametri ai componenti di primo livello</span><span class="sxs-lookup"><span data-stu-id="14fc9-109"> Component Tag Helper and pass parameters to top-level components</span></span>

<span data-ttu-id="14fc9-110">Con Blazor ASP.NET Core 3.0, il rendering dei componenti è`Html.RenderComponentAsync`stato eseguito in pagine e visualizzazioni utilizzando un HTML Helper ( ).</span><span class="sxs-lookup"><span data-stu-id="14fc9-110">In Blazor with ASP.NET Core 3.0, components were rendered into pages and views using an HTML Helper (`Html.RenderComponentAsync`).</span></span> <span data-ttu-id="14fc9-111">In ASP.NET Core 3.1, render a component from a page or view with the new Component Tag Helper:</span><span class="sxs-lookup"><span data-stu-id="14fc9-111">In ASP.NET Core 3.1, render a component from a page or view with the new Component Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="14fc9-112">Html Helper rimane supportato in ASP.NET Core 3.1, ma è consigliabile Component Tag Helper.</span><span class="sxs-lookup"><span data-stu-id="14fc9-112">The HTML Helper remains supported in ASP.NET Core 3.1, but the Component Tag Helper is recommended.</span></span>

Blazor<span data-ttu-id="14fc9-113">Le app server possono ora passare parametri ai componenti di primo livello durante il rendering iniziale.</span><span class="sxs-lookup"><span data-stu-id="14fc9-113"> Server apps can now pass parameters to top-level components during the initial render.</span></span> <span data-ttu-id="14fc9-114">In precedenza era possibile passare parametri solo a un componente di primo livello con [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span><span class="sxs-lookup"><span data-stu-id="14fc9-114">Previously you could only pass parameters to a top-level component with [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span></span> <span data-ttu-id="14fc9-115">Con questa versione, sono supportati sia [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) che [RenderModel.ServerPrerendered.With this release, both RenderMode.Server and RenderModel.ServerPrerendered are supported.](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered)</span><span class="sxs-lookup"><span data-stu-id="14fc9-115">With this release, both [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) and [RenderModel.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) are supported.</span></span> <span data-ttu-id="14fc9-116">Tutti i valori di parametro specificati vengono serializzati come JSON e inclusi nella risposta iniziale.</span><span class="sxs-lookup"><span data-stu-id="14fc9-116">Any specified parameter values are serialized as JSON and included in the initial response.</span></span>

<span data-ttu-id="14fc9-117">Ad esempio, eseguire `Counter` il prerendering`IncrementAmount`di un componente con un importo di incremento ( ):</span><span class="sxs-lookup"><span data-stu-id="14fc9-117">For example, prerender a `Counter` component with an increment amount (`IncrementAmount`):</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="14fc9-118">Per ulteriori informazioni, vedere [Integrare componenti nelle pagine Razor e nelle app MVC.](xref:blazor/integrate-components)</span><span class="sxs-lookup"><span data-stu-id="14fc9-118">For more information, see [Integrate components into Razor Pages and MVC apps](xref:blazor/integrate-components).</span></span>

## <a name="support-for-shared-queues-in-httpsys"></a><span data-ttu-id="14fc9-119">Supporto per code condivise in HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="14fc9-119">Support for shared queues in HTTP.sys</span></span>

<span data-ttu-id="14fc9-120">[HTTP.sys](xref:fundamentals/servers/httpsys) supporta la creazione di code di richieste anonime.</span><span class="sxs-lookup"><span data-stu-id="14fc9-120">[HTTP.sys](xref:fundamentals/servers/httpsys) supports creating anonymous request queues.</span></span> <span data-ttu-id="14fc9-121">In ASP.NET Core 3.1 è stata aggiunta la possibilità di creare o collegare una coda di richieste HTTP.sys denominata esistente.</span><span class="sxs-lookup"><span data-stu-id="14fc9-121">In ASP.NET Core 3.1, we've added to ability to create or attach to an existing named HTTP.sys request queue.</span></span> <span data-ttu-id="14fc9-122">La creazione o il collegamento a una coda di richieste HTTP.sys denominata esistente abilita scenari in cui il processo del controller HTTP.sys proprietario della coda è indipendente dal processo del listener.</span><span class="sxs-lookup"><span data-stu-id="14fc9-122">Creating or attaching to an existing named HTTP.sys request queue enables scenarios where the HTTP.sys controller process that owns the queue is independent of the listener process.</span></span> <span data-ttu-id="14fc9-123">Questa indipendenza consente di mantenere le connessioni esistenti e le richieste accodate tra i riavvii del processo del listener:This independence makes it possible to preserve existing connections and enqueued requests between listener process restarts:</span><span class="sxs-lookup"><span data-stu-id="14fc9-123">This independence makes it possible to preserve existing connections and enqueued requests between listener process restarts:</span></span>

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a><span data-ttu-id="14fc9-124">Modifiche di rilievo per i cookie SameSite</span><span class="sxs-lookup"><span data-stu-id="14fc9-124">Breaking changes for SameSite cookies</span></span>

<span data-ttu-id="14fc9-125">Il comportamento dei cookie SameSite è cambiato per riflettere le prossime modifiche apportate al browser.</span><span class="sxs-lookup"><span data-stu-id="14fc9-125">The behavior of SameSite cookies has changed to reflect upcoming browser changes.</span></span> <span data-ttu-id="14fc9-126">Ciò può influire sugli scenari di autenticazione come AzureAd, OpenIdConnect o WsFederation.This may affect authentication scenarios like AzureAd, OpenIdConnect, or WsFederation.</span><span class="sxs-lookup"><span data-stu-id="14fc9-126">This may affect authentication scenarios like AzureAd, OpenIdConnect, or WsFederation.</span></span> <span data-ttu-id="14fc9-127">Per altre informazioni, vedere <xref:security/samesite>.</span><span class="sxs-lookup"><span data-stu-id="14fc9-127">For more information, see <xref:security/samesite>.</span></span>

## <a name="prevent-default-actions-for-events-in-opno-locblazor-apps"></a><span data-ttu-id="14fc9-128">Impedisci azioni predefinite Blazor per gli eventi nelle app</span><span class="sxs-lookup"><span data-stu-id="14fc9-128">Prevent default actions for events in Blazor apps</span></span>

<span data-ttu-id="14fc9-129">Utilizzare `@on{EVENT}:preventDefault` l'attributo della direttiva per impedire l'azione predefinita per un evento.</span><span class="sxs-lookup"><span data-stu-id="14fc9-129">Use the `@on{EVENT}:preventDefault` directive attribute to prevent the default action for an event.</span></span> <span data-ttu-id="14fc9-130">Nell'esempio seguente, l'azione predefinita di visualizzazione del carattere del tasto nella casella di testo è impedita:</span><span class="sxs-lookup"><span data-stu-id="14fc9-130">In the following example, the default action of displaying the key's character in the text box is prevented:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

<span data-ttu-id="14fc9-131">Per ulteriori informazioni, consultate [Impedire azioni predefinite.](xref:blazor/event-handling#prevent-default-actions)</span><span class="sxs-lookup"><span data-stu-id="14fc9-131">For more information, see [Prevent default actions](xref:blazor/event-handling#prevent-default-actions).</span></span>

## <a name="stop-event-propagation-in-opno-locblazor-apps"></a><span data-ttu-id="14fc9-132">Interrompere la propagazione degli eventi nelle Blazor app</span><span class="sxs-lookup"><span data-stu-id="14fc9-132">Stop event propagation in Blazor apps</span></span>

<span data-ttu-id="14fc9-133">Utilizzare `@on{EVENT}:stopPropagation` l'attributo della direttiva per interrompere la propagazione degli eventi.</span><span class="sxs-lookup"><span data-stu-id="14fc9-133">Use the `@on{EVENT}:stopPropagation` directive attribute to stop event propagation.</span></span> <span data-ttu-id="14fc9-134">Nell'esempio seguente, la selezione della casella `<div>` di controllo impedisce `<div>`la propagazione degli eventi Click dall'elemento figlio all'elemento padre :</span><span class="sxs-lookup"><span data-stu-id="14fc9-134">In the following example, selecting the check box prevents click events from the child `<div>` from propagating to the parent `<div>`:</span></span>

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

<span data-ttu-id="14fc9-135">Per ulteriori informazioni, consultate Interrompere la [propagazione degli eventi.](xref:blazor/event-handling#stop-event-propagation)</span><span class="sxs-lookup"><span data-stu-id="14fc9-135">For more information, see [Stop event propagation](xref:blazor/event-handling#stop-event-propagation).</span></span>

## <a name="detailed-errors-during-opno-locblazor-app-development"></a><span data-ttu-id="14fc9-136">Errori dettagliati Blazor durante lo sviluppo dell'appDetailed errors during app development</span><span class="sxs-lookup"><span data-stu-id="14fc9-136">Detailed errors during Blazor app development</span></span>

<span data-ttu-id="14fc9-137">Quando Blazor un'app non funziona correttamente durante lo sviluppo, la ricezione di informazioni dettagliate sull'errore dall'app consente di risolvere il problema.</span><span class="sxs-lookup"><span data-stu-id="14fc9-137">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="14fc9-138">Quando si verifica Blazor un errore, le app visualizzano una barra d'oro nella parte inferiore dello schermo:</span><span class="sxs-lookup"><span data-stu-id="14fc9-138">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="14fc9-139">Durante lo sviluppo, la barra d'oro ti indirizza alla console del browser, dove puoi vedere l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="14fc9-139">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="14fc9-140">In produzione, la barra d'oro notifica all'utente che si è verificato un errore e consiglia di aggiornare il browser.</span><span class="sxs-lookup"><span data-stu-id="14fc9-140">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="14fc9-141">Per ulteriori informazioni, vedere [Errori dettagliati durante lo sviluppo](xref:blazor/handle-errors#detailed-errors-during-development).</span><span class="sxs-lookup"><span data-stu-id="14fc9-141">For more information, see [Detailed errors during development](xref:blazor/handle-errors#detailed-errors-during-development).</span></span>
