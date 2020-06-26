---
title: Novità di ASP.NET Core 3,1
author: rick-anderson
description: Informazioni sulle nuove funzionalità di ASP.NET Core 3,1.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: 92804d168381526100ddb8a368f71d201bd4cad9
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407668"
---
# <a name="whats-new-in-aspnet-core-31"></a><span data-ttu-id="fc5a3-103">Novità di ASP.NET Core 3,1</span><span class="sxs-lookup"><span data-stu-id="fc5a3-103">What's new in ASP.NET Core 3.1</span></span>

<span data-ttu-id="fc5a3-104">In questo articolo vengono evidenziate le modifiche più significative in ASP.NET Core 3,1 con i collegamenti alla documentazione pertinente.</span><span class="sxs-lookup"><span data-stu-id="fc5a3-104">This article highlights the most significant changes in ASP.NET Core 3.1 with links to relevant documentation.</span></span>

## <a name="partial-class-support-for-razor-components"></a><span data-ttu-id="fc5a3-105">Supporto delle classi parziali per i Razor componenti</span><span class="sxs-lookup"><span data-stu-id="fc5a3-105">Partial class support for Razor components</span></span>

Razor<span data-ttu-id="fc5a3-106">i componenti vengono ora generati come classi parziali.</span><span class="sxs-lookup"><span data-stu-id="fc5a3-106"> components are now generated as partial classes.</span></span> <span data-ttu-id="fc5a3-107">Il codice per un Razor componente può essere scritto utilizzando un file code-behind definito come classe parziale anziché definire tutto il codice per il componente in un unico file.</span><span class="sxs-lookup"><span data-stu-id="fc5a3-107">Code for a Razor component can be written using a code-behind file defined as a partial class rather than defining all the code for the component in a single file.</span></span> <span data-ttu-id="fc5a3-108">Per ulteriori informazioni, vedere [supporto di classi parziali](xref:blazor/components/index#partial-class-support).</span><span class="sxs-lookup"><span data-stu-id="fc5a3-108">For more information, see [Partial class support](xref:blazor/components/index#partial-class-support).</span></span>

## <a name="blazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor<span data-ttu-id="fc5a3-109">Helper Tag Component e passare i parametri ai componenti di primo livello</span><span class="sxs-lookup"><span data-stu-id="fc5a3-109"> Component Tag Helper and pass parameters to top-level components</span></span>

<span data-ttu-id="fc5a3-110">In Blazor con ASP.NET Core 3,0 i componenti venivano sottoposti a rendering in pagine e visualizzazioni utilizzando un helper HTML ( `Html.RenderComponentAsync` ).</span><span class="sxs-lookup"><span data-stu-id="fc5a3-110">In Blazor with ASP.NET Core 3.0, components were rendered into pages and views using an HTML Helper (`Html.RenderComponentAsync`).</span></span> <span data-ttu-id="fc5a3-111">In ASP.NET Core 3,1 eseguire il rendering di un componente da una pagina o da una vista con il nuovo Helper tag di componente:</span><span class="sxs-lookup"><span data-stu-id="fc5a3-111">In ASP.NET Core 3.1, render a component from a page or view with the new Component Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="fc5a3-112">L'helper HTML rimane supportato in ASP.NET Core 3,1, ma è consigliabile usare l'helper Tag Component.</span><span class="sxs-lookup"><span data-stu-id="fc5a3-112">The HTML Helper remains supported in ASP.NET Core 3.1, but the Component Tag Helper is recommended.</span></span>

Blazor Server<span data-ttu-id="fc5a3-113">le app possono ora passare parametri ai componenti di primo livello durante il rendering iniziale.</span><span class="sxs-lookup"><span data-stu-id="fc5a3-113"> apps can now pass parameters to top-level components during the initial render.</span></span> <span data-ttu-id="fc5a3-114">In precedenza era possibile passare i parametri solo a un componente di primo livello con [RenderMode. static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span><span class="sxs-lookup"><span data-stu-id="fc5a3-114">Previously you could only pass parameters to a top-level component with [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span></span> <span data-ttu-id="fc5a3-115">Con questa versione sono supportati sia [RenderMode. Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) che [RenderMode. ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) .</span><span class="sxs-lookup"><span data-stu-id="fc5a3-115">With this release, both [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) and [RenderMode.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) are supported.</span></span> <span data-ttu-id="fc5a3-116">Qualsiasi valore di parametro specificato viene serializzato come JSON e incluso nella risposta iniziale.</span><span class="sxs-lookup"><span data-stu-id="fc5a3-116">Any specified parameter values are serialized as JSON and included in the initial response.</span></span>

<span data-ttu-id="fc5a3-117">Ad esempio, prerenderizzare un `Counter` componente con un valore di incremento ( `IncrementAmount` ):</span><span class="sxs-lookup"><span data-stu-id="fc5a3-117">For example, prerender a `Counter` component with an increment amount (`IncrementAmount`):</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="fc5a3-118">Per altre informazioni, vedere [integrare i componenti in Razor pagine e app MVC](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps).</span><span class="sxs-lookup"><span data-stu-id="fc5a3-118">For more information, see [Integrate components into Razor Pages and MVC apps](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps).</span></span>

## <a name="support-for-shared-queues-in-httpsys"></a><span data-ttu-id="fc5a3-119">Supporto per le code condivise in HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="fc5a3-119">Support for shared queues in HTTP.sys</span></span>

<span data-ttu-id="fc5a3-120">[HTTP.sys](xref:fundamentals/servers/httpsys) supporta la creazione di code di richieste anonime.</span><span class="sxs-lookup"><span data-stu-id="fc5a3-120">[HTTP.sys](xref:fundamentals/servers/httpsys) supports creating anonymous request queues.</span></span> <span data-ttu-id="fc5a3-121">In ASP.NET Core 3,1 è stata aggiunta la possibilità di creare o connettersi a una coda di richieste HTTP.sys denominata esistente.</span><span class="sxs-lookup"><span data-stu-id="fc5a3-121">In ASP.NET Core 3.1, we've added to ability to create or attach to an existing named HTTP.sys request queue.</span></span> <span data-ttu-id="fc5a3-122">La creazione o la connessione a una coda di richieste HTTP.sys denominata esistente Abilita scenari in cui il processo del controller HTTP.sys proprietario della coda è indipendente dal processo del listener.</span><span class="sxs-lookup"><span data-stu-id="fc5a3-122">Creating or attaching to an existing named HTTP.sys request queue enables scenarios where the HTTP.sys controller process that owns the queue is independent of the listener process.</span></span> <span data-ttu-id="fc5a3-123">Questa indipendenza rende possibile la conservazione delle connessioni esistenti e delle richieste accodate tra i riavvii del processo del listener:</span><span class="sxs-lookup"><span data-stu-id="fc5a3-123">This independence makes it possible to preserve existing connections and enqueued requests between listener process restarts:</span></span>

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a><span data-ttu-id="fc5a3-124">Modifiche di rilievo per i cookie navigava sullostesso sito</span><span class="sxs-lookup"><span data-stu-id="fc5a3-124">Breaking changes for SameSite cookies</span></span>

<span data-ttu-id="fc5a3-125">Il comportamento dei cookie navigava sullostesso sito è stato modificato in modo da riflettere le modifiche imminenti del browser.</span><span class="sxs-lookup"><span data-stu-id="fc5a3-125">The behavior of SameSite cookies has changed to reflect upcoming browser changes.</span></span> <span data-ttu-id="fc5a3-126">Questo può influire su scenari di autenticazione come AzureAd, OpenIdConnect o WsFederation.</span><span class="sxs-lookup"><span data-stu-id="fc5a3-126">This may affect authentication scenarios like AzureAd, OpenIdConnect, or WsFederation.</span></span> <span data-ttu-id="fc5a3-127">Per altre informazioni, vedere <xref:security/samesite>.</span><span class="sxs-lookup"><span data-stu-id="fc5a3-127">For more information, see <xref:security/samesite>.</span></span>

## <a name="prevent-default-actions-for-events-in-blazor-apps"></a><span data-ttu-id="fc5a3-128">Impedisci azioni predefinite per gli eventi nelle Blazor app</span><span class="sxs-lookup"><span data-stu-id="fc5a3-128">Prevent default actions for events in Blazor apps</span></span>

<span data-ttu-id="fc5a3-129">Utilizzare l' `@on{EVENT}:preventDefault` attributo Directive per impedire l'azione predefinita per un evento.</span><span class="sxs-lookup"><span data-stu-id="fc5a3-129">Use the `@on{EVENT}:preventDefault` directive attribute to prevent the default action for an event.</span></span> <span data-ttu-id="fc5a3-130">Nell'esempio seguente viene impedita l'azione predefinita di visualizzazione del carattere della chiave nella casella di testo:</span><span class="sxs-lookup"><span data-stu-id="fc5a3-130">In the following example, the default action of displaying the key's character in the text box is prevented:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

<span data-ttu-id="fc5a3-131">Per ulteriori informazioni, vedere [Impedisci azioni predefinite](xref:blazor/components/event-handling#prevent-default-actions).</span><span class="sxs-lookup"><span data-stu-id="fc5a3-131">For more information, see [Prevent default actions](xref:blazor/components/event-handling#prevent-default-actions).</span></span>

## <a name="stop-event-propagation-in-blazor-apps"></a><span data-ttu-id="fc5a3-132">Arresta propagazione eventi nelle Blazor app</span><span class="sxs-lookup"><span data-stu-id="fc5a3-132">Stop event propagation in Blazor apps</span></span>

<span data-ttu-id="fc5a3-133">Utilizzare l' `@on{EVENT}:stopPropagation` attributo Directive per arrestare la propagazione degli eventi.</span><span class="sxs-lookup"><span data-stu-id="fc5a3-133">Use the `@on{EVENT}:stopPropagation` directive attribute to stop event propagation.</span></span> <span data-ttu-id="fc5a3-134">Nell'esempio seguente la selezione della casella di controllo impedisce la propagazione degli eventi click dall'elemento figlio `<div>` all'elemento padre `<div>` :</span><span class="sxs-lookup"><span data-stu-id="fc5a3-134">In the following example, selecting the check box prevents click events from the child `<div>` from propagating to the parent `<div>`:</span></span>

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

<span data-ttu-id="fc5a3-135">Per altre informazioni, vedere [arrestare la propagazione degli eventi](xref:blazor/components/event-handling#stop-event-propagation).</span><span class="sxs-lookup"><span data-stu-id="fc5a3-135">For more information, see [Stop event propagation](xref:blazor/components/event-handling#stop-event-propagation).</span></span>

## <a name="detailed-errors-during-blazor-app-development"></a><span data-ttu-id="fc5a3-136">Errori dettagliati durante Blazor lo sviluppo di app</span><span class="sxs-lookup"><span data-stu-id="fc5a3-136">Detailed errors during Blazor app development</span></span>

<span data-ttu-id="fc5a3-137">Quando un' Blazor app non funziona correttamente durante lo sviluppo, la ricezione di informazioni dettagliate sugli errori dall'app è utile per la risoluzione dei problemi e per risolvere il problema.</span><span class="sxs-lookup"><span data-stu-id="fc5a3-137">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="fc5a3-138">Quando si verifica un errore, le Blazor app visualizzano una barra dorata nella parte inferiore della schermata:</span><span class="sxs-lookup"><span data-stu-id="fc5a3-138">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="fc5a3-139">Durante lo sviluppo, la barra dorata indirizza l'utente alla console del browser, in cui è possibile visualizzare l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="fc5a3-139">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="fc5a3-140">In produzione, la barra dorata informa l'utente che si è verificato un errore e consiglia di aggiornare il browser.</span><span class="sxs-lookup"><span data-stu-id="fc5a3-140">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="fc5a3-141">Per ulteriori informazioni, vedere [errori dettagliati durante lo sviluppo](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development).</span><span class="sxs-lookup"><span data-stu-id="fc5a3-141">For more information, see [Detailed errors during development](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development).</span></span>
