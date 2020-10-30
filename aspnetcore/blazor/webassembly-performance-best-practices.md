---
title: :::no-loc(Blazor WebAssembly):::Procedure consigliate per le prestazioni ASP.NET Core
author: pranavkm
description: 'Suggerimenti per migliorare le prestazioni in ASP.NET Core :::no-loc(Blazor WebAssembly)::: app ed evitare problemi comuni relativi alle prestazioni.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/09/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 423745d734d8da2b8f3f974f9b4dd1a0265d4877
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054736"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a><span data-ttu-id="a0972-103">:::no-loc(Blazor WebAssembly):::Procedure consigliate per le prestazioni ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a0972-103">ASP.NET Core :::no-loc(Blazor WebAssembly)::: performance best practices</span></span>

<span data-ttu-id="a0972-104">Di [Pranav Krishnamoorthy](https://github.com/pranavkm) [Manuel e Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="a0972-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="a0972-105">:::no-loc(Blazor WebAssembly)::: viene accuratamente progettato e ottimizzato per garantire prestazioni elevate in scenari di interfaccia utente dell'applicazione più realistici.</span><span class="sxs-lookup"><span data-stu-id="a0972-105">:::no-loc(Blazor WebAssembly)::: is carefully designed and optimized to enable high performance in most realistic application UI scenarios.</span></span> <span data-ttu-id="a0972-106">Tuttavia, la produzione dei migliori risultati dipende dagli sviluppatori che usano i modelli e le funzionalità corrette.</span><span class="sxs-lookup"><span data-stu-id="a0972-106">However, producing the best results depends on developers using the right patterns and features.</span></span> <span data-ttu-id="a0972-107">Prendere in considerazione gli aspetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="a0972-107">Consider the following aspects:</span></span>

* <span data-ttu-id="a0972-108">**Velocità effettiva di runtime** : il codice .NET viene eseguito in un interprete nel runtime di webassembly, quindi la velocità effettiva della CPU è limitata.</span><span class="sxs-lookup"><span data-stu-id="a0972-108">**Runtime throughput** : The .NET code runs on an interpreter within the WebAssembly runtime, so CPU throughput is limited.</span></span> <span data-ttu-id="a0972-109">In scenari complessi, l'app trae vantaggio dall' [ottimizzazione della velocità di rendering](#optimize-rendering-speed).</span><span class="sxs-lookup"><span data-stu-id="a0972-109">In demanding scenarios, the app benefits from [optimizing rendering speed](#optimize-rendering-speed).</span></span>
* <span data-ttu-id="a0972-110">**Tempo di avvio** : l'app trasferisce un Runtime .NET al browser, quindi è importante usare le funzionalità che [riducono al minimo le dimensioni del download dell'applicazione](#minimize-app-download-size).</span><span class="sxs-lookup"><span data-stu-id="a0972-110">**Startup time** : The app transfers a .NET runtime to the browser, so it's important to use features that [minimize the application download size](#minimize-app-download-size).</span></span>

## <a name="optimize-rendering-speed"></a><span data-ttu-id="a0972-111">Ottimizzare la velocità di rendering</span><span class="sxs-lookup"><span data-stu-id="a0972-111">Optimize rendering speed</span></span>

<span data-ttu-id="a0972-112">Le sezioni seguenti forniscono consigli per ridurre il carico di lavoro di rendering e migliorare la velocità di risposta dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="a0972-112">The following sections provide recommendations to minimize rendering workload and improve UI responsiveness.</span></span> <span data-ttu-id="a0972-113">Seguendo questo Consiglio, è possibile eseguire facilmente un *miglioramento di dieci volte o superiore* nella velocità di rendering dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="a0972-113">Following this advice could easily make a *ten-fold or higher improvement* in UI rendering speeds.</span></span>

### <a name="avoid-unnecessary-rendering-of-component-subtrees"></a><span data-ttu-id="a0972-114">Evitare il rendering superfluo di sottoalberi dei componenti</span><span class="sxs-lookup"><span data-stu-id="a0972-114">Avoid unnecessary rendering of component subtrees</span></span>

<span data-ttu-id="a0972-115">In fase di esecuzione, i componenti esistono come una gerarchia.</span><span class="sxs-lookup"><span data-stu-id="a0972-115">At runtime, components exist as a hierarchy.</span></span> <span data-ttu-id="a0972-116">Un componente radice contiene componenti figlio.</span><span class="sxs-lookup"><span data-stu-id="a0972-116">A root component has child components.</span></span> <span data-ttu-id="a0972-117">A sua volta, i figli della radice hanno i propri componenti figlio e così via.</span><span class="sxs-lookup"><span data-stu-id="a0972-117">In turn, the root's children have their own child components, and so on.</span></span> <span data-ttu-id="a0972-118">Quando si verifica un evento, ad esempio un utente che seleziona un pulsante, questo è il modo :::no-loc(Blazor)::: in cui decide i componenti di cui eseguire il rendering:</span><span class="sxs-lookup"><span data-stu-id="a0972-118">When an event occurs, such as a user selecting a button, this is how :::no-loc(Blazor)::: decides which components to rerender:</span></span>

 1. <span data-ttu-id="a0972-119">L'evento stesso viene inviato a qualsiasi componente di cui è stato eseguito il rendering del gestore dell'evento.</span><span class="sxs-lookup"><span data-stu-id="a0972-119">The event itself is dispatched to whichever component rendered the event's handler.</span></span> <span data-ttu-id="a0972-120">Dopo l'esecuzione del gestore dell'evento, viene eseguito il rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="a0972-120">After executing the event handler, that component is rerendered.</span></span>
 1. <span data-ttu-id="a0972-121">Ogni volta che viene eseguito il rendering di un componente, fornisce una nuova copia dei valori di parametro a ognuno dei relativi componenti figlio.</span><span class="sxs-lookup"><span data-stu-id="a0972-121">Whenever any component is rerendered, it supplies a new copy of the parameter values to each of its child components.</span></span>
 1. <span data-ttu-id="a0972-122">Quando si riceve un nuovo set di valori di parametro, ogni componente sceglie se eseguire nuovamente il rendering.</span><span class="sxs-lookup"><span data-stu-id="a0972-122">When receiving a new set of parameter values, each component chooses whether to rerender.</span></span> <span data-ttu-id="a0972-123">Per impostazione predefinita, i componenti eseguono il rendering se è possibile che i valori dei parametri siano stati modificati, ad esempio se sono oggetti modificabili.</span><span class="sxs-lookup"><span data-stu-id="a0972-123">By default, components rerender if the parameter values may have changed (for example, if they are mutable objects).</span></span>

<span data-ttu-id="a0972-124">Gli ultimi due passaggi di questa sequenza continuano in modo ricorsivo la gerarchia dei componenti.</span><span class="sxs-lookup"><span data-stu-id="a0972-124">The last two steps of this sequence continue recursively down the component hierarchy.</span></span> <span data-ttu-id="a0972-125">In molti casi, viene eseguito il rendering dell'intero sottoalbero.</span><span class="sxs-lookup"><span data-stu-id="a0972-125">In many cases, the entire subtree is rerendered.</span></span> <span data-ttu-id="a0972-126">Questo significa che gli eventi destinati a componenti di alto livello possono causare costosi processi di rirendering perché è necessario eseguire il rendering di tutti gli elementi al di sotto di tale punto.</span><span class="sxs-lookup"><span data-stu-id="a0972-126">This means that events targeting high-level components can cause expensive rerendering processes because everything below that point must be rerendered.</span></span>

<span data-ttu-id="a0972-127">Se si desidera interrompere il processo e impedire il rendering della ricorsione in un sottoalbero particolare, è possibile eseguire una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="a0972-127">If you want to interrupt this process and prevent rendering recursion into a particular subtree, then you can either:</span></span>

 * <span data-ttu-id="a0972-128">Verificare che tutti i parametri di un determinato componente siano tipi non modificabili primitivi (ad esempio,,,, `string` `int` `bool` `DateTime` e altri).</span><span class="sxs-lookup"><span data-stu-id="a0972-128">Ensure that all parameters to a certain component are of primitive immutable types (for example, `string`, `int`, `bool`, `DateTime`, and others).</span></span> <span data-ttu-id="a0972-129">La logica predefinita per il rilevamento delle modifiche ignora automaticamente il rendering se nessuno di questi valori di parametro è stato modificato.</span><span class="sxs-lookup"><span data-stu-id="a0972-129">The built-in logic for detecting changes automatically skips rerendering if none of these parameter values have changed.</span></span> <span data-ttu-id="a0972-130">Se si esegue il rendering di un componente figlio con `<Customer CustomerId="@item.CustomerId" />` , dove `CustomerId` è un `int` valore, non viene nuovamente eseguito il rendering tranne quando viene `item.CustomerId` modificato.</span><span class="sxs-lookup"><span data-stu-id="a0972-130">If you render a child component with `<Customer CustomerId="@item.CustomerId" />`, where `CustomerId` is an `int` value, then it isn't rerendered except when `item.CustomerId` changes.</span></span>
 * <span data-ttu-id="a0972-131">Se è necessario accettare valori di parametro non primitivi, ad esempio tipi di modelli personalizzati, callback di eventi o <xref:Microsoft.AspNetCore.Components.RenderFragment> valori, è possibile eseguire l'override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> di per controllare la decisione relativa all'eventuale rendering, descritto nella sezione [utilizzo `ShouldRender` di](#use-of-shouldrender) .</span><span class="sxs-lookup"><span data-stu-id="a0972-131">If you need to accept nonprimitive parameter values, such as custom model types, event callbacks, or <xref:Microsoft.AspNetCore.Components.RenderFragment> values, then you can override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to control the decision about whether to render, which is described in the [Use of `ShouldRender`](#use-of-shouldrender) section.</span></span>

<span data-ttu-id="a0972-132">Ignorando il rendering di interi sottoalberi, potrebbe essere possibile rimuovere la maggior parte del costo di rendering quando si verifica un evento.</span><span class="sxs-lookup"><span data-stu-id="a0972-132">By skipping rerendering of whole subtrees, you may be able to remove the vast majority of the rendering cost when an event occurs.</span></span>

<span data-ttu-id="a0972-133">È possibile scomporre i componenti figlio in modo specifico, in modo che sia possibile ignorare il rendering della parte dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="a0972-133">You may wish to factor out child components specifically so that you can skip rerendering that part of the UI.</span></span> <span data-ttu-id="a0972-134">Si tratta di un modo valido per ridurre il costo di rendering di un componente padre.</span><span class="sxs-lookup"><span data-stu-id="a0972-134">This is a valid way to reduce the rendering cost of a parent component.</span></span>

#### <a name="use-of-shouldrender"></a><span data-ttu-id="a0972-135">Utilizzo di `ShouldRender`</span><span class="sxs-lookup"><span data-stu-id="a0972-135">Use of `ShouldRender`</span></span>

<span data-ttu-id="a0972-136">Se la creazione di un componente solo interfaccia utente che non cambia mai dopo il rendering iniziale (indipendentemente dai valori dei parametri), configurare <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> per restituire `false` :</span><span class="sxs-lookup"><span data-stu-id="a0972-136">If authoring a UI-only component that never changes after the initial render (regardless of any parameter values), configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="a0972-137">Se il componente richiede solo il rirendering quando i valori dei parametri cambiano in modo particolare, è possibile usare campi privati per tenere traccia delle informazioni necessarie per rilevare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="a0972-137">If the component only requires rerendering when its parameter values mutate in particular ways, then you can use private fields to track the necessary information to detect changes.</span></span> <span data-ttu-id="a0972-138">Nell'esempio seguente, `shouldRender` si basa sul controllo di qualsiasi tipo di modifica o mutazione che dovrebbe richiedere un rirendering.</span><span class="sxs-lookup"><span data-stu-id="a0972-138">In the following example, `shouldRender` is based on checking for any kind of change or mutation that should prompt a rerender.</span></span> <span data-ttu-id="a0972-139">`prevOutboundFlightId` e `prevInboundFlightId` tenere traccia delle informazioni per il prossimo aggiornamento potenziale:</span><span class="sxs-lookup"><span data-stu-id="a0972-139">`prevOutboundFlightId` and `prevInboundFlightId` track information for the next potential update:</span></span>

```razor
@code {
    [Parameter]
    public FlightInfo OutboundFlight { get; set; }
    
    [Parameter]
    public FlightInfo InboundFlight { get; set; }

    private int prevOutboundFlightId;
    private int prevInboundFlightId;
    private bool shouldRender;

    protected override void OnParametersSet()
    {
        shouldRender = OutboundFlight.FlightId != prevOutboundFlightId
            || InboundFlight.FlightId != prevInboundFlightId;

        prevOutboundFlightId = OutboundFlight.FlightId;
        prevInboundFlightId = InboundFlight.FlightId;
    }

    protected override void ShouldRender() => shouldRender;

    // Note that 
}
```

<span data-ttu-id="a0972-140">Nel codice precedente, un gestore eventi può anche impostare `shouldRender` su in `true` modo che il componente venga nuovamente eseguito dopo l'evento.</span><span class="sxs-lookup"><span data-stu-id="a0972-140">In the preceding code, an event handler may also set `shouldRender` to `true` so that the component is rerendered after the event.</span></span>

<span data-ttu-id="a0972-141">Per la maggior parte dei componenti, questo livello di controllo manuale non è necessario.</span><span class="sxs-lookup"><span data-stu-id="a0972-141">For most components, this level of manual control isn't necessary.</span></span> <span data-ttu-id="a0972-142">È consigliabile ignorare il rendering di sottoalberi se tali sottoalberi sono particolarmente costosi per il rendering e causano un ritardo dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="a0972-142">You should only be concerned about skipping rendering subtrees if those subtrees are particularly expensive to render and are causing UI lag.</span></span>

<span data-ttu-id="a0972-143">Per altre informazioni, vedere <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="a0972-143">For more information, see <xref:blazor/components/lifecycle>.</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="virtualization"></a><span data-ttu-id="a0972-144">Virtualizzazione</span><span class="sxs-lookup"><span data-stu-id="a0972-144">Virtualization</span></span>

<span data-ttu-id="a0972-145">Quando si esegue il rendering di grandi quantità di interfaccia utente all'interno di un ciclo, ad esempio un elenco o una griglia con migliaia di voci, la quantità totale di operazioni di rendering può causare un ritardo nel rendering dell'interfaccia utente e quindi un'esperienza utente insufficiente.</span><span class="sxs-lookup"><span data-stu-id="a0972-145">When rendering large amounts of UI within a loop, for example a list or grid with thousands of entries, the sheer quantity of rendering operations can lead to a lag in UI rendering and thus a poor user experience.</span></span> <span data-ttu-id="a0972-146">Dato che l'utente può visualizzare solo un numero ridotto di elementi in una sola volta senza scorrere, sembra superfluo dedicare molto tempo al rendering degli elementi che attualmente non sono visibili.</span><span class="sxs-lookup"><span data-stu-id="a0972-146">Given that the user can only see a small number of elements at once without scrolling, it seems wasteful to spend so much time rendering elements that aren't currently visible.</span></span>

<span data-ttu-id="a0972-147">Per risolvere questo problema, :::no-loc(Blazor)::: fornisce un [ `<Virtualize>` componente](xref:blazor/components/virtualization) incorporato che crea l'aspetto e lo scorrimento dei comportamenti di un elenco di grandi dimensioni, ma esegue effettivamente solo il rendering degli elementi dell'elenco che si trovano all'interno del viewport di scorrimento corrente.</span><span class="sxs-lookup"><span data-stu-id="a0972-147">To address this, :::no-loc(Blazor)::: provides a built-in [`<Virtualize>` component](xref:blazor/components/virtualization) that creates the appearance and scroll behaviors of an arbitrarily-large list but actually only renders the list items that are within the current scroll viewport.</span></span> <span data-ttu-id="a0972-148">Questo significa, ad esempio, che l'app può avere un elenco con 100.000 voci, ma paga solo il costo di rendering di 20 elementi visibili in un momento qualsiasi.</span><span class="sxs-lookup"><span data-stu-id="a0972-148">For example, this means that the app can have a list with 100,000 entries but only pay the rendering cost of 20 items that are visible at any one time.</span></span> <span data-ttu-id="a0972-149">L'uso del `<Virtualize>` componente può aumentare le prestazioni dell'interfaccia utente in base agli ordini di grandezza.</span><span class="sxs-lookup"><span data-stu-id="a0972-149">Use of the `<Virtualize>` component can scale up UI performance by orders of magnitude.</span></span>

<span data-ttu-id="a0972-150">`<Virtualize>` può essere usato nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="a0972-150">`<Virtualize>` can be used when:</span></span>

 * <span data-ttu-id="a0972-151">Rendering di un set di elementi di dati in un ciclo.</span><span class="sxs-lookup"><span data-stu-id="a0972-151">Rendering a set of data items in a loop.</span></span>
 * <span data-ttu-id="a0972-152">La maggior parte degli elementi non è visibile a causa dello scorrimento.</span><span class="sxs-lookup"><span data-stu-id="a0972-152">Most of the items aren't visible due to scrolling.</span></span>
 * <span data-ttu-id="a0972-153">Gli elementi di cui è stato eseguito il rendering hanno esattamente le stesse dimensioni.</span><span class="sxs-lookup"><span data-stu-id="a0972-153">The rendered items are exactly the same size.</span></span> <span data-ttu-id="a0972-154">Quando l'utente scorre fino a un punto arbitrario, il componente può calcolare gli elementi visibili da visualizzare.</span><span class="sxs-lookup"><span data-stu-id="a0972-154">When the user scrolls to an arbitrary point, the component can calculate the visible items to show.</span></span>

<span data-ttu-id="a0972-155">Di seguito viene illustrato un esempio di elenco non virtualizzato:</span><span class="sxs-lookup"><span data-stu-id="a0972-155">The following shows an example of a non-virtualized list:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    }
</div>
```

<span data-ttu-id="a0972-156">Se la `allFlights` raccolta include 10.000 elementi, crea un'istanza ed esegue il rendering delle `<FlightSummary>` istanze del componente 10.000.</span><span class="sxs-lookup"><span data-stu-id="a0972-156">If the `allFlights` collection holds 10,000 items, it instantiates and renders 10,000 `<FlightSummary>` component instances.</span></span> <span data-ttu-id="a0972-157">In confronto, di seguito viene illustrato un esempio di elenco virtualizzato:</span><span class="sxs-lookup"><span data-stu-id="a0972-157">In comparison, the following shows an example of a virtualized list:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    </Virtualize>
</div>
```

<span data-ttu-id="a0972-158">Anche se l'interfaccia utente risultante sembra identica a un utente, dietro le quinte il componente crea solo un'istanza ed esegue il rendering di tutte le `<FlightSummary>` istanze necessarie per riempire l'area di scorrimento.</span><span class="sxs-lookup"><span data-stu-id="a0972-158">Even though the resulting UI looks the same to a user, behind the scenes the component only instantiates and renders as many `<FlightSummary>` instances as are required to fill the scrollable region.</span></span> <span data-ttu-id="a0972-159">Il set di `<FlightSummary>` istanze visualizzato viene ricalcolato e sottoposto a rendering quando l'utente scorre.</span><span class="sxs-lookup"><span data-stu-id="a0972-159">The set of `<FlightSummary>` instances displayed is recalculated and rendered as the user scrolls.</span></span>

<span data-ttu-id="a0972-160">`<Virtualize>` offre anche altri vantaggi.</span><span class="sxs-lookup"><span data-stu-id="a0972-160">`<Virtualize>` has other benefits, too.</span></span> <span data-ttu-id="a0972-161">Ad esempio, quando un componente richiede dati da un'API esterna, `<Virtualize>` consente al componente di recuperare solo la sezione di record che corrispondono all'area visibile corrente, anziché scaricare tutti i dati dalla raccolta.</span><span class="sxs-lookup"><span data-stu-id="a0972-161">For example when a component requests data from an external API, `<Virtualize>` permits the component to only fetch the slice of records that correspond to the current visible region, instead of downloading all the data from the collection.</span></span>

<span data-ttu-id="a0972-162">Per altre informazioni, vedere <xref:blazor/components/virtualization>.</span><span class="sxs-lookup"><span data-stu-id="a0972-162">For more information, see <xref:blazor/components/virtualization>.</span></span>

::: moniker-end

### <a name="create-lightweight-optimized-components"></a><span data-ttu-id="a0972-163">Creazione di componenti semplici e ottimizzati</span><span class="sxs-lookup"><span data-stu-id="a0972-163">Create lightweight, optimized components</span></span>

<span data-ttu-id="a0972-164">La maggior parte dei :::no-loc(Blazor)::: componenti non richiede attività di ottimizzazione aggressive.</span><span class="sxs-lookup"><span data-stu-id="a0972-164">Most :::no-loc(Blazor)::: components don't require aggressive optimization efforts.</span></span> <span data-ttu-id="a0972-165">Questo è dovuto al fatto che la maggior parte dei componenti non si ripete spesso nell'interfaccia utente e non viene eseguito il rendering a frequenza elevata.</span><span class="sxs-lookup"><span data-stu-id="a0972-165">This is because most components don't often repeat in the UI and don't rerender at high frequency.</span></span> <span data-ttu-id="a0972-166">Ad esempio, `@page` componenti e componenti che rappresentano parti dell'interfaccia utente di alto livello come finestre di dialogo o moduli, molto probabilmente vengono visualizzati solo uno alla volta e ne viene eseguito il rendering solo in risposta a un movimento dell'utente.</span><span class="sxs-lookup"><span data-stu-id="a0972-166">For example, `@page` components and components representing high-level UI pieces such as dialogs or forms, most likely appear only one at a time and only rerender in response to a user gesture.</span></span> <span data-ttu-id="a0972-167">Questi componenti non creano un carico di lavoro di rendering elevato, quindi è possibile usare liberamente qualsiasi combinazione di funzionalità del Framework che si vuole senza preoccuparsi delle prestazioni di rendering.</span><span class="sxs-lookup"><span data-stu-id="a0972-167">These components don't create a high rendering workload, so you can freely use any combination of framework features you want without worrying much about rendering performance.</span></span>

<span data-ttu-id="a0972-168">Tuttavia, esistono anche scenari comuni in cui si compilano componenti che devono essere ripetuti su larga scala.</span><span class="sxs-lookup"><span data-stu-id="a0972-168">However, there are also common scenarios where you build components that need to be repeated at scale.</span></span> <span data-ttu-id="a0972-169">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a0972-169">For example:</span></span>

 * <span data-ttu-id="a0972-170">I moduli annidati di grandi dimensioni possono avere centinaia di input singoli, etichette e altri elementi.</span><span class="sxs-lookup"><span data-stu-id="a0972-170">Large nested forms may have hundreds of individual inputs, labels, and other elements.</span></span>
 * <span data-ttu-id="a0972-171">Le griglie possono contenere migliaia di celle.</span><span class="sxs-lookup"><span data-stu-id="a0972-171">Grids may have thousands of cells.</span></span>
 * <span data-ttu-id="a0972-172">I grafici a dispersione possono contenere milioni di punti dati.</span><span class="sxs-lookup"><span data-stu-id="a0972-172">Scatter plots may have millions of data points.</span></span>

<span data-ttu-id="a0972-173">Se si modellano le singole unità come istanze di componenti separate, ne saranno presenti così tante che le prestazioni di rendering diventano critiche.</span><span class="sxs-lookup"><span data-stu-id="a0972-173">If modelling each unit as separate component instances, there will be so many of them that their rendering performance does become critical.</span></span> <span data-ttu-id="a0972-174">In questa sezione vengono fornite indicazioni su come rendere tali componenti leggeri, in modo che l'interfaccia utente rimanga veloce e reattiva.</span><span class="sxs-lookup"><span data-stu-id="a0972-174">This section provides advice on making such components lightweight so that the UI remains fast and responsive.</span></span>

#### <a name="avoid-thousands-of-component-instances"></a><span data-ttu-id="a0972-175">Evitare migliaia di istanze di componenti</span><span class="sxs-lookup"><span data-stu-id="a0972-175">Avoid thousands of component instances</span></span>

<span data-ttu-id="a0972-176">Ogni componente è un'isola separata che può essere sottoposta a rendering indipendentemente dagli elementi padre e figlio.</span><span class="sxs-lookup"><span data-stu-id="a0972-176">Each component is a separate island that can render independently of its parents and children.</span></span> <span data-ttu-id="a0972-177">Scegliendo come suddividere l'interfaccia utente in una gerarchia di componenti, si sta assumendo il controllo della granularità del rendering dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="a0972-177">By choosing how to split up the UI into a hierarchy of components, you are taking control over the granularity of UI rendering.</span></span> <span data-ttu-id="a0972-178">Questo può essere positivo o negativo per le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="a0972-178">This can be either good or bad for performance.</span></span>

 * <span data-ttu-id="a0972-179">Suddividendo l'interfaccia utente in più componenti, è possibile avere parti più piccole del rendering dell'interfaccia utente quando si verificano gli eventi.</span><span class="sxs-lookup"><span data-stu-id="a0972-179">By splitting the UI into more components, you can have smaller portions of the UI rerender when events occur.</span></span> <span data-ttu-id="a0972-180">Ad esempio, quando un utente fa clic su un pulsante in una riga di tabella, è possibile che venga eseguito solo il rirendering di una singola riga anziché l'intera pagina o tabella.</span><span class="sxs-lookup"><span data-stu-id="a0972-180">For example when a user clicks a button in a table row, you may be able to have only that single row rerender instead of the whole page or table.</span></span>
 * <span data-ttu-id="a0972-181">Tuttavia, ogni componente aggiuntivo comporta un sovraccarico di memoria e CPU aggiuntivo per gestire lo stato indipendente e il ciclo di vita di rendering.</span><span class="sxs-lookup"><span data-stu-id="a0972-181">However, each extra component involves some extra memory and CPU overhead to deal with its independent state and rendering lifecycle.</span></span>

<span data-ttu-id="a0972-182">Quando si ottimizzano le prestazioni di :::no-loc(Blazor WebAssembly)::: in .NET 5, è stato misurato un sovraccarico di rendering di circa 0,06 ms per ogni istanza del componente.</span><span class="sxs-lookup"><span data-stu-id="a0972-182">When tuning the performance of :::no-loc(Blazor WebAssembly)::: on .NET 5, we measured a rendering overhead of around 0.06 ms per component instance.</span></span> <span data-ttu-id="a0972-183">Si basa su un componente semplice che accetta tre parametri in esecuzione su un portatile tipico.</span><span class="sxs-lookup"><span data-stu-id="a0972-183">This is based on a simple component that accepts three parameters running on a typical laptop.</span></span> <span data-ttu-id="a0972-184">Internamente, l'overhead è in gran parte dovuto al recupero dello stato per componente dai dizionari e al passaggio e alla ricezione di parametri.</span><span class="sxs-lookup"><span data-stu-id="a0972-184">Internally, the overhead is largely due to retrieving per-component state from dictionaries and passing and receiving parameters.</span></span> <span data-ttu-id="a0972-185">Per moltiplicazione, è possibile notare che l'aggiunta di 2.000 istanze di componenti aggiuntivi aggiungerà 0,12 secondi al tempo di rendering e l'interfaccia utente inizierà a rallentare gli utenti.</span><span class="sxs-lookup"><span data-stu-id="a0972-185">By multiplication, you can see that adding 2,000 extra component instances would add 0.12 seconds to the rendering time and the UI would begin feeling slow to users.</span></span>

<span data-ttu-id="a0972-186">È possibile rendere i componenti più leggeri, in modo da poterli avere più, ma spesso la tecnica più potente non deve avere così tanti componenti.</span><span class="sxs-lookup"><span data-stu-id="a0972-186">It's possible to make components more lightweight so that you can have more of them, but often the more powerful technique is not to have so many components.</span></span> <span data-ttu-id="a0972-187">Le sezioni seguenti descrivono due approcci.</span><span class="sxs-lookup"><span data-stu-id="a0972-187">The following sections describe two approaches.</span></span>

##### <a name="inline-child-components-into-their-parents"></a><span data-ttu-id="a0972-188">Componenti figlio inline nei relativi elementi padre</span><span class="sxs-lookup"><span data-stu-id="a0972-188">Inline child components into their parents</span></span>

<span data-ttu-id="a0972-189">Si consideri il componente seguente che esegue il rendering di una sequenza di componenti figlio:</span><span class="sxs-lookup"><span data-stu-id="a0972-189">Consider the following component that renders a sequence of child components:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <ChatMessageDisplay Message="@message" />
    }
</div>
```

<span data-ttu-id="a0972-190">Per il codice di esempio precedente, il `<ChatMessageDisplay>` componente viene definito in un file `ChatMessageDisplay.razor` contenente:</span><span class="sxs-lookup"><span data-stu-id="a0972-190">For the preceding example code, the `<ChatMessageDisplay>` component is defined in a file `ChatMessageDisplay.razor` containing:</span></span>

```razor
<div class="chat-message">
    <span class="author">@Message.Author</span>
    <span class="text">@Message.Text</span>
</div>

@code {
    [Parameter]
    public ChatMessage Message { get; set; }
}
```

<span data-ttu-id="a0972-191">L'esempio precedente funziona correttamente e viene eseguito correttamente a condizione che migliaia di messaggi non vengano visualizzati contemporaneamente.</span><span class="sxs-lookup"><span data-stu-id="a0972-191">The preceding example works fine and performs well as long as thousands of messages aren't shown at once.</span></span> <span data-ttu-id="a0972-192">Per mostrare migliaia di messaggi contemporaneamente, provare a *non* scomporre il `ChatMessageDisplay` componente separato.</span><span class="sxs-lookup"><span data-stu-id="a0972-192">To show thousands of messages at once, consider *not* factoring out the separate `ChatMessageDisplay` component.</span></span> <span data-ttu-id="a0972-193">Inline invece il rendering direttamente nell'elemento padre:</span><span class="sxs-lookup"><span data-stu-id="a0972-193">Instead, inline the rendering directly into the parent:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    }
</div>
```

<span data-ttu-id="a0972-194">In questo modo si evita l'overhead per componente per il rendering di un numero così elevato di componenti figlio, a scapito del fatto che non è possibile eseguire nuovamente il rendering di ognuno di essi in modo indipendente.</span><span class="sxs-lookup"><span data-stu-id="a0972-194">This avoids the per-component overhead of rendering so many child components at the cost of not being able to rerender each of them independently.</span></span>

##### <a name="define-reusable-renderfragments-in-code"></a><span data-ttu-id="a0972-195">Definire riutilizzabile `RenderFragments` nel codice</span><span class="sxs-lookup"><span data-stu-id="a0972-195">Define reusable `RenderFragments` in code</span></span>

<span data-ttu-id="a0972-196">È possibile scomporre i componenti figlio esclusivamente come un modo per riutilizzare la logica di rendering.</span><span class="sxs-lookup"><span data-stu-id="a0972-196">You may be factoring out child components purely as a way of reusing rendering logic.</span></span> <span data-ttu-id="a0972-197">In tal caso, è comunque possibile riutilizzare la logica di rendering senza dichiarare i componenti effettivi.</span><span class="sxs-lookup"><span data-stu-id="a0972-197">If that's the case, it's still possible to reuse rendering logic without declaring actual components.</span></span> <span data-ttu-id="a0972-198">Nel blocco di un componente è `@code` possibile definire un <xref:Microsoft.AspNetCore.Components.RenderFragment> che genera l'interfaccia utente e può essere chiamato da qualsiasi posizione:</span><span class="sxs-lookup"><span data-stu-id="a0972-198">In any component's `@code` block, you can define a <xref:Microsoft.AspNetCore.Components.RenderFragment> that emits UI and can be called from anywhere:</span></span>

```razor
<h1>Hello, world!</h1>

@RenderWelcomeInfo

@code {
    RenderFragment RenderWelcomeInfo = __builder =>
    {
        <div>
            <p>Welcome to your new app!</p>

            <SurveyPrompt Title="How is :::no-loc(Blazor)::: working for you?" />
        </div>
    };
}
```

<span data-ttu-id="a0972-199">Come demonstated nell'esempio precedente, i componenti possono emettere markup dal codice all'interno del `@code` blocco e al di fuori di esso.</span><span class="sxs-lookup"><span data-stu-id="a0972-199">As demonstated in the preceding example, components can emit markup from code within their `@code` block and outside it.</span></span> <span data-ttu-id="a0972-200">Questo approccio definisce un <xref:Microsoft.AspNetCore.Components.RenderFragment> delegato di cui è possibile eseguire il rendering all'interno dell'output di rendering normale del componente, facoltativamente in più posizioni.</span><span class="sxs-lookup"><span data-stu-id="a0972-200">This approach defines a <xref:Microsoft.AspNetCore.Components.RenderFragment> delegate that you can render inside the component's normal render output, optionally in multiple places.</span></span> <span data-ttu-id="a0972-201">È necessario che il delegato accetti un parametro denominato `__builder` di tipo <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> in modo che il :::no-loc(Razor)::: compilatore possa produrre istruzioni per il rendering.</span><span class="sxs-lookup"><span data-stu-id="a0972-201">It's necessary for the delegate to accept a parameter called `__builder` of type <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> so that the :::no-loc(Razor)::: compiler can produce rendering instructions for it.</span></span>

<span data-ttu-id="a0972-202">Se si desidera rendere questo oggetto riutilizzabile in più componenti, è consigliabile dichiararlo come `public static` membro:</span><span class="sxs-lookup"><span data-stu-id="a0972-202">If you want to make this reusable across multiple components, consider declaring it as a `public static` member:</span></span>

```razor
public static RenderFragment SayHello = __builder =>
{
    <h1>Hello!</h1>
};
```

<span data-ttu-id="a0972-203">Questo ora può essere richiamato da un componente non correlato.</span><span class="sxs-lookup"><span data-stu-id="a0972-203">This could now be invoked from an unrelated component.</span></span> <span data-ttu-id="a0972-204">Questa tecnica è utile per la creazione di librerie di frammenti di markup riutilizzabili che eseguono il rendering senza alcun sovraccarico per componente.</span><span class="sxs-lookup"><span data-stu-id="a0972-204">This technique is useful for building libraries of reusable markup snippets that render without any per-component overhead.</span></span>

<span data-ttu-id="a0972-205"><xref:Microsoft.AspNetCore.Components.RenderFragment> i delegati possono anche accettare parametri.</span><span class="sxs-lookup"><span data-stu-id="a0972-205"><xref:Microsoft.AspNetCore.Components.RenderFragment> delegates can also accept parameters.</span></span> <span data-ttu-id="a0972-206">Per creare l'equivalente del `ChatMessageDisplay` componente dall'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="a0972-206">To create the equivalent of the `ChatMessageDisplay` component from the earlier example:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        @DisplayChatMessage(message)
    }
</div>

@code {
    RenderFragment<ChatMessage> DisplayChatMessage = message => __builder =>
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    };
}
```

<span data-ttu-id="a0972-207">Questo approccio offre il vantaggio di riutilizzare la logica di rendering senza overhead per componente.</span><span class="sxs-lookup"><span data-stu-id="a0972-207">This approach provides the benefit of reusing rendering logic without per-component overhead.</span></span> <span data-ttu-id="a0972-208">Tuttavia, non è in grado di aggiornare il sottoalbero dell'interfaccia utente in modo indipendente, né di ignorare il rendering del sottoalbero dell'interfaccia utente quando viene eseguito il rendering dell'elemento padre, dal momento che non esiste alcun limite per i componenti.</span><span class="sxs-lookup"><span data-stu-id="a0972-208">However, it doesn't have the benefit of being able to refresh its subtree of the UI independently, nor does it have the ability to skip rendering that subtree of the UI when its parent renders, since there's no component boundary.</span></span>

#### <a name="dont-receive-too-many-parameters"></a><span data-ttu-id="a0972-209">Non ricevere troppi parametri</span><span class="sxs-lookup"><span data-stu-id="a0972-209">Don't receive too many parameters</span></span>

<span data-ttu-id="a0972-210">Se un componente si ripete molto spesso, ad esempio centinaia o migliaia di volte, tenere presente che l'overhead associato al passaggio e alla ricezione di ogni parametro viene compilato.</span><span class="sxs-lookup"><span data-stu-id="a0972-210">If a component repeats extremely often, for example hundreds or thousands of times, then bear in mind that the overhead of passing and receiving each parameter builds up.</span></span>

<span data-ttu-id="a0972-211">È raro che troppi parametri limitino notevolmente le prestazioni, ma può essere un fattore.</span><span class="sxs-lookup"><span data-stu-id="a0972-211">It's rare that too many parameters severely restricts performance, but it can be a factor.</span></span> <span data-ttu-id="a0972-212">Per un `<TableCell>` componente che esegue il rendering di 1.000 volte all'interno di una griglia, ogni parametro aggiuntivo passato a esso potrebbe aggiungere circa 15 ms al costo totale di rendering.</span><span class="sxs-lookup"><span data-stu-id="a0972-212">For a `<TableCell>` component that renders 1,000 times within a grid, each extra parameter passed to it could add around 15 ms to the total rendering cost.</span></span> <span data-ttu-id="a0972-213">Se ogni cella accettava 10 parametri, il passaggio del parametro richiede circa 150 ms per ogni componente e, di conseguenza, 150.000 ms (150 secondi) e, di conseguenza, un'interfaccia utente ritardata.</span><span class="sxs-lookup"><span data-stu-id="a0972-213">If each cell accepted 10 parameters, parameter passing takes around 150 ms per component render and  thus perhaps 150,000 ms (150 seconds) and on its own cause a laggy UI.</span></span>

<span data-ttu-id="a0972-214">Per ridurre questo carico, è possibile raggruppare più parametri tramite classi personalizzate.</span><span class="sxs-lookup"><span data-stu-id="a0972-214">To reduce this load, you could bundle together multiple parameters via custom classes.</span></span> <span data-ttu-id="a0972-215">È ad esempio possibile che un `<TableCell>` componente accetti:</span><span class="sxs-lookup"><span data-stu-id="a0972-215">For example, a `<TableCell>` component might accept:</span></span>

```razor
@typeparam TItem

...

@code {
    [Parameter]
    public TItem Data { get; set; }
    
    [Parameter]
    public GridOptions Options { get; set; }
}
```

<span data-ttu-id="a0972-216">Nell'esempio precedente, `Data` è diverso per ogni cella, ma `Options` è comune in tutti gli elementi.</span><span class="sxs-lookup"><span data-stu-id="a0972-216">In the preceding example, `Data` is different for every cell, but `Options` is common across all of them.</span></span> <span data-ttu-id="a0972-217">Naturalmente, potrebbe trattarsi di un miglioramento per non avere un `<TableCell>` componente e inline la relativa logica nel componente padre.</span><span class="sxs-lookup"><span data-stu-id="a0972-217">Of course, it might be an improvement not to have a `<TableCell>` component and instead inline its logic into the parent component.</span></span>

#### <a name="ensure-cascading-parameters-are-fixed"></a><span data-ttu-id="a0972-218">Verificare che i parametri a catena siano corretti</span><span class="sxs-lookup"><span data-stu-id="a0972-218">Ensure cascading parameters are fixed</span></span>

<span data-ttu-id="a0972-219">Il `<CascadingValue>` componente ha un parametro facoltativo denominato `IsFixed` .</span><span class="sxs-lookup"><span data-stu-id="a0972-219">The `<CascadingValue>` component has an optional parameter called `IsFixed`.</span></span>

 * <span data-ttu-id="a0972-220">Se il `IsFixed` valore è `false` (impostazione predefinita), ogni destinatario del valore a cascata imposta una sottoscrizione per la ricezione delle notifiche di modifica.</span><span class="sxs-lookup"><span data-stu-id="a0972-220">If the `IsFixed` value is `false` (the default), then every recipient of the cascaded value sets up a subscription to receive change notifications.</span></span> <span data-ttu-id="a0972-221">In questo caso ognuno di essi `[CascadingParameter]` è **sostanzialmente più costoso** di un normale `[Parameter]` a causa del rilevamento della sottoscrizione.</span><span class="sxs-lookup"><span data-stu-id="a0972-221">In this case, each each `[CascadingParameter]` is **substantially more expensive** than a regular `[Parameter]` due to the subscription tracking.</span></span>
 * <span data-ttu-id="a0972-222">Se il `IsFixed` valore è `true` (ad esempio, `<CascadingValue Value="@someValue" IsFixed="true">` ), destinatari riceverà il valore iniziale ma *non* configurarà alcuna sottoscrizione per la ricezione degli aggiornamenti.</span><span class="sxs-lookup"><span data-stu-id="a0972-222">If the `IsFixed` value is `true` (for example, `<CascadingValue Value="@someValue" IsFixed="true">`), then receipients receive the initial value but do *not* set up any subscription to receive updates.</span></span> <span data-ttu-id="a0972-223">In questo caso, ogni `[CascadingParameter]` è leggero e **non è più costoso** di un normale `[Parameter]` .</span><span class="sxs-lookup"><span data-stu-id="a0972-223">In this case, each `[CascadingParameter]` is lightweight and **no more expensive** than a regular `[Parameter]`.</span></span>

<span data-ttu-id="a0972-224">Quindi, laddove possibile, è consigliabile usare `IsFixed="true"` i valori a cascata.</span><span class="sxs-lookup"><span data-stu-id="a0972-224">So wherever possible, you should use `IsFixed="true"` on cascaded values.</span></span> <span data-ttu-id="a0972-225">Questa operazione può essere eseguita ogni volta che il valore fornito non cambia nel tempo.</span><span class="sxs-lookup"><span data-stu-id="a0972-225">You can do this whenever the value being supplied doesn't change over time.</span></span> <span data-ttu-id="a0972-226">Nel modello comune in cui un componente passa `this` come valore a catena, è necessario usare `IsFixed="true"` :</span><span class="sxs-lookup"><span data-stu-id="a0972-226">In the common pattern where a component passes `this` as a cascaded value, you should use `IsFixed="true"`:</span></span>

```razor
<CascadingValue Value="this" IsFixed="true">
    <SomeOtherComponents>
</CascadingValue>
```

<span data-ttu-id="a0972-227">Questo fa una grande differenza se è presente un numero elevato di altri componenti che ricevono il valore a cascata.</span><span class="sxs-lookup"><span data-stu-id="a0972-227">This makes a huge difference if there are a large number of other components that receive the cascaded value.</span></span> <span data-ttu-id="a0972-228">Per altre informazioni, vedere <xref:blazor/components/cascading-values-and-parameters>.</span><span class="sxs-lookup"><span data-stu-id="a0972-228">For more information, see <xref:blazor/components/cascading-values-and-parameters>.</span></span>

#### <a name="avoid-attribute-splatting-with-captureunmatchedvalues"></a><span data-ttu-id="a0972-229">Evitare l'attributo splatting con `CaptureUnmatchedValues`</span><span class="sxs-lookup"><span data-stu-id="a0972-229">Avoid attribute splatting with `CaptureUnmatchedValues`</span></span>

<span data-ttu-id="a0972-230">I componenti possono scegliere di ricevere i valori di parametro "senza corrispondenza" utilizzando il <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> flag:</span><span class="sxs-lookup"><span data-stu-id="a0972-230">Components can elect to receive "unmatched" parameter values using the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> flag:</span></span>

```razor
<div @attributes="OtherAttributes">...</div>

@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public IDictionary<string, object> OtherAttributes { get; set; }
}
```

<span data-ttu-id="a0972-231">Questo approccio consente di passare gli attributi aggiuntivi arbitrari all'elemento.</span><span class="sxs-lookup"><span data-stu-id="a0972-231">This approach allows passing through arbitrary additional attributes to the element.</span></span> <span data-ttu-id="a0972-232">Tuttavia, è anche piuttosto costoso perché il renderer deve:</span><span class="sxs-lookup"><span data-stu-id="a0972-232">However, it is also quite expensive because the renderer must:</span></span>

* <span data-ttu-id="a0972-233">Corrisponde a tutti i parametri forniti rispetto al set di parametri noti per compilare un dizionario.</span><span class="sxs-lookup"><span data-stu-id="a0972-233">Match all of the supplied parameters against the set of known parameters to build a dictionary.</span></span>
* <span data-ttu-id="a0972-234">Tenere traccia della sovrascrittura di più copie dello stesso attributo.</span><span class="sxs-lookup"><span data-stu-id="a0972-234">Keep track of how multiple copies of the same attribute overwrite each other.</span></span>

<span data-ttu-id="a0972-235"><xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>È possibile utilizzare su componenti non critici per le prestazioni, ad esempio quelli che non vengono ripetuti spesso.</span><span class="sxs-lookup"><span data-stu-id="a0972-235">Feel free to use <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> on non-performance-critical components, such as ones that are not repeated frequently.</span></span> <span data-ttu-id="a0972-236">Tuttavia, per i componenti che eseguono il rendering su larga scala, ad esempio ogni elemento in un elenco di grandi dimensioni o celle di una griglia, provare a evitare l'attributo splatting.</span><span class="sxs-lookup"><span data-stu-id="a0972-236">However for components that render at scale, such as each items in a large list or cells in a grid, try to avoid attribute splatting.</span></span>

<span data-ttu-id="a0972-237">Per altre informazioni, vedere <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="a0972-237">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

#### <a name="implement-setparametersasync-manually"></a><span data-ttu-id="a0972-238">Implementare `SetParametersAsync` manualmente</span><span class="sxs-lookup"><span data-stu-id="a0972-238">Implement `SetParametersAsync` manually</span></span>

<span data-ttu-id="a0972-239">Uno degli aspetti principali del sovraccarico di rendering per componente consiste nel scrivere i valori dei parametri in ingresso nelle `[Parameter]` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="a0972-239">One of the main aspects of the per-component rendering overhead is writing incoming parameter values to the `[Parameter]` properties.</span></span> <span data-ttu-id="a0972-240">Per eseguire questa operazione, il renderer deve utilizzare la reflection.</span><span class="sxs-lookup"><span data-stu-id="a0972-240">The renderer has to use reflection to do this.</span></span> <span data-ttu-id="a0972-241">Anche se si tratta di un po' ottimizzato, l'assenza del supporto JIT nel runtime di webassembly impone limiti.</span><span class="sxs-lookup"><span data-stu-id="a0972-241">Even though this is somewhat optimized, the absence of JIT support on the WebAssembly runtime imposes limits.</span></span>

<span data-ttu-id="a0972-242">In alcuni casi estremi, può essere utile evitare la reflection e implementare manualmente la logica di impostazione dei parametri.</span><span class="sxs-lookup"><span data-stu-id="a0972-242">In some extreme cases, you may wish to avoid the reflection and implement your own parameter setting logic manually.</span></span> <span data-ttu-id="a0972-243">Questo può essere applicabile nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="a0972-243">This may be applicable when:</span></span>

 * <span data-ttu-id="a0972-244">Si dispone di un componente che esegue il rendering molto spesso (ad esempio, sono presenti centinaia o migliaia di copie dell'interfaccia utente).</span><span class="sxs-lookup"><span data-stu-id="a0972-244">You have a component that renders extremely often (for example, there are hundreds or thousands of copies of it in the UI).</span></span>
 * <span data-ttu-id="a0972-245">Accetta molti parametri.</span><span class="sxs-lookup"><span data-stu-id="a0972-245">It accepts many parameters.</span></span>
 * <span data-ttu-id="a0972-246">Si noterà che l'overhead della ricezione dei parametri ha un impatto osservabile sulla velocità di risposta dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="a0972-246">You find that the overhead of receiving parameters has an observable impact on UI responsiveness.</span></span>

<span data-ttu-id="a0972-247">In questi casi, è possibile eseguire l'override del metodo virtuale del componente <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> e implementare la logica specifica del componente.</span><span class="sxs-lookup"><span data-stu-id="a0972-247">In these cases, you can override the component's virtual <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> method and implement your own component-specific logic.</span></span> <span data-ttu-id="a0972-248">Nell'esempio seguente vengono deliberatamente evitate le ricerche nel dizionario:</span><span class="sxs-lookup"><span data-stu-id="a0972-248">The following example deliberately avoids any dictionary lookups:</span></span>

```razor
@code {
    [Parameter]
    public int MessageId { get; set; }

    [Parameter]
    public string Text { get; set; }

    [Parameter]
    public EventCallback<string> TextChanged { get; set; }

    [Parameter]
    public Theme CurrentTheme { get; set; }

    public override Task SetParametersAsync(ParameterView parameters)
    {
        foreach (var parameter in parameters)
        {
            switch (parameter.Name)
            {
                case nameof(MessageId):
                    MessageId = (int)parameter.Value;
                    break;
                case nameof(Text):
                    Text = (string)parameter.Value;
                    break;
                case nameof(TextChanged):
                    TextChanged = (EventCallback<string>)parameter.Value;
                    break;
                case nameof(CurrentTheme):
                    CurrentTheme = (Theme)parameter.Value;
                    break;
                default:
                    throw new ArgumentException($"Unknown parameter: {parameter.Name}");
            }
        }

        return base.SetParametersAsync(ParameterView.Empty);
    }
}
```

<span data-ttu-id="a0972-249">Nel codice precedente, la restituzione della classe base <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> esegue i normali metodi del ciclo di vita senza assegnare nuovamente parametri.</span><span class="sxs-lookup"><span data-stu-id="a0972-249">In the preceding code, returning the base class <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> runs the normal lifecycle methods without assigning parameters again.</span></span>

<span data-ttu-id="a0972-250">Come si può vedere nel codice precedente, l'override <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> e la fornitura di logica personalizzata sono complicate e laboriose, pertanto non è consigliabile usare questo approccio in generale.</span><span class="sxs-lookup"><span data-stu-id="a0972-250">As you can see in the preceding code, overriding <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> and supplying custom logic is complicated and laborious, so we don't recommend this approach in general.</span></span> <span data-ttu-id="a0972-251">In casi estremi, può migliorare le prestazioni di rendering del 20-25%, ma è opportuno considerare questo approccio solo negli scenari elencati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="a0972-251">In extreme cases, it can improve rendering performance by 20-25%, but you should only consider this approach in the scenarios listed earlier.</span></span>

### <a name="dont-trigger-events-too-rapidly"></a><span data-ttu-id="a0972-252">Non attivare eventi troppo rapidamente</span><span class="sxs-lookup"><span data-stu-id="a0972-252">Don't trigger events too rapidly</span></span>

<span data-ttu-id="a0972-253">Alcuni eventi del browser vengono attivati molto spesso, ad esempio `onmousemove` e `onscroll` , che possono essere attivati decine o centinaia di volte al secondo.</span><span class="sxs-lookup"><span data-stu-id="a0972-253">Some browser events fire extremely frequently, for example `onmousemove` and `onscroll`, which can fire tens or hundreds of times per second.</span></span> <span data-ttu-id="a0972-254">Nella maggior parte dei casi, non è necessario eseguire gli aggiornamenti dell'interfaccia utente in modo frequente.</span><span class="sxs-lookup"><span data-stu-id="a0972-254">In most cases, you don't need to perform UI updates this frequently.</span></span> <span data-ttu-id="a0972-255">Se si tenta di eseguire questa operazione, si potrebbe danneggiare la velocità di risposta dell'interfaccia utente o utilizzare un tempo di CPU eccessivo.</span><span class="sxs-lookup"><span data-stu-id="a0972-255">If you try to do so, you may harm UI responsiveness or consume excessive CPU time.</span></span>

<span data-ttu-id="a0972-256">Anziché usare eventi nativi `@onmousemove` o `@onscroll` , può essere preferibile usare l'interoperabilità js per registrare un callback che viene attivato meno di frequente.</span><span class="sxs-lookup"><span data-stu-id="a0972-256">Rather than using native `@onmousemove` or `@onscroll` events, you may prefer to use JS interop to register a callback that fires less frequently.</span></span> <span data-ttu-id="a0972-257">Ad esempio, il componente seguente ( `MyComponent.razor` ) Visualizza la posizione del mouse, ma solo gli aggiornamenti al massimo ogni 500 ms:</span><span class="sxs-lookup"><span data-stu-id="a0972-257">For example, the following component (`MyComponent.razor`) displays the position of the mouse but only updates at most once every 500 ms:</span></span>

```razor
@inject IJSRuntime JS
@implements IDisposable

<h1>@message</h1>

<div @ref="myMouseMoveElement" style="border:1px dashed red;height:200px;">
    Move mouse here
</div>

@code {
    ElementReference myMouseMoveElement;
    DotNetObjectReference<MyComponent> selfReference;
    private string message = "Move the mouse in the box";

    [JSInvokable]
    public void HandleMouseMove(int x, int y)
    {
        message = $"Mouse move at {x}, {y}";
        StateHasChanged();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            selfReference = DotNetObjectReference.Create(this);
            var minInterval = 500; // Only notify every 500 ms
            await JS.InvokeVoidAsync("onThrottledMouseMove", 
                myMouseMoveElement, selfReference, minInterval);
        }
    }

    public void Dispose() => selfReference?.Dispose();
}
```

<span data-ttu-id="a0972-258">Il codice JavaScript corrispondente, che può essere inserito nella `index.html` pagina o caricato come modulo ES6, registra il listener di eventi DOM effettivo.</span><span class="sxs-lookup"><span data-stu-id="a0972-258">The corresponding JavaScript code, which can be placed in the `index.html` page or loaded as an ES6 module, registers the actual DOM event listener.</span></span> <span data-ttu-id="a0972-259">In questo esempio, il listener di eventi usa la [ `throttle` funzione di Lodash](https://lodash.com/docs/4.17.15#throttle) per limitare la frequenza delle chiamate:</span><span class="sxs-lookup"><span data-stu-id="a0972-259">In this example, the event listener uses [Lodash's `throttle` function](https://lodash.com/docs/4.17.15#throttle) to limit the rate of invocations:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.20/lodash.min.js"></script>
<script>
  function onThrottledMouseMove(elem, component, interval) {
    elem.addEventListener('mousemove', _.throttle(e => {
      component.invokeMethodAsync('HandleMouseMove', e.offsetX, e.offsetY);
    }, interval));
  }
</script>
```

<span data-ttu-id="a0972-260">Questa tecnica può essere ancora più importante per :::no-loc(Blazor Server)::: , perché ogni chiamata di evento comporta il recapito di un messaggio tramite la rete.</span><span class="sxs-lookup"><span data-stu-id="a0972-260">This technique can be even more important for :::no-loc(Blazor Server):::, since each event invocation involves delivering a message over the network.</span></span> <span data-ttu-id="a0972-261">È utile per :::no-loc(Blazor WebAssembly)::: perché può ridurre notevolmente la quantità di lavoro di rendering.</span><span class="sxs-lookup"><span data-stu-id="a0972-261">It's valuable for :::no-loc(Blazor WebAssembly)::: because it can greatly reduce the amount of rendering work.</span></span>

## <a name="optimize-javascript-interop-speed"></a><span data-ttu-id="a0972-262">Ottimizza la velocità di interoperabilità JavaScript</span><span class="sxs-lookup"><span data-stu-id="a0972-262">Optimize JavaScript interop speed</span></span>

<span data-ttu-id="a0972-263">Le chiamate tra .NET e JavaScript comportano un sovraccarico aggiuntivo perché:</span><span class="sxs-lookup"><span data-stu-id="a0972-263">Calls between .NET and JavaScript involve some additional overhead because:</span></span>

 * <span data-ttu-id="a0972-264">Per impostazione predefinita, le chiamate sono asincrone.</span><span class="sxs-lookup"><span data-stu-id="a0972-264">By default, calls are asynchronous.</span></span>
 * <span data-ttu-id="a0972-265">Per impostazione predefinita, i parametri e i valori restituiti sono serializzati in JSON.</span><span class="sxs-lookup"><span data-stu-id="a0972-265">By default, parameters and return values are JSON-serialized.</span></span> <span data-ttu-id="a0972-266">Questo consente di fornire un meccanismo di conversione di facile comprensione tra i tipi .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a0972-266">This is to provide an easy-to-understand conversion mechanism between .NET and JavaScript types.</span></span>

<span data-ttu-id="a0972-267">Inoltre :::no-loc(Blazor Server)::: , queste chiamate vengono passate attraverso la rete.</span><span class="sxs-lookup"><span data-stu-id="a0972-267">Additionally on :::no-loc(Blazor Server):::, these calls are passed across the network.</span></span>

### <a name="avoid-excessively-fine-grained-calls"></a><span data-ttu-id="a0972-268">Evitare chiamate con granularità eccessiva</span><span class="sxs-lookup"><span data-stu-id="a0972-268">Avoid excessively fine-grained calls</span></span>

<span data-ttu-id="a0972-269">Poiché ogni chiamata implica un sovraccarico, può essere utile ridurre il numero di chiamate.</span><span class="sxs-lookup"><span data-stu-id="a0972-269">Since each call involves some overhead, it can be valuable to reduce the number of calls.</span></span> <span data-ttu-id="a0972-270">Si consideri il codice seguente, in cui viene archiviata una raccolta di elementi nell'archivio del browser `localStorage` :</span><span class="sxs-lookup"><span data-stu-id="a0972-270">Consider the following code, which stores a collection of items in the browser's `localStorage` store:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    foreach (var item in items)
    {
        await JS.InvokeVoidAsync("localStorage.setItem", item.Id, 
            JsonSerializer.Serialize(item));
    }
}
```

<span data-ttu-id="a0972-271">L'esempio precedente esegue una chiamata di interoperabilità JS separata per ogni elemento.</span><span class="sxs-lookup"><span data-stu-id="a0972-271">The preceding example makes a separate JS interop call for each item.</span></span> <span data-ttu-id="a0972-272">Al contrario, l'approccio seguente riduce l'interoperabilità JS a una singola chiamata:</span><span class="sxs-lookup"><span data-stu-id="a0972-272">Instead, the following approach reduces the JS interop to a single call:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    await JS.InvokeVoidAsync("storeAllInLocalStorage", items);
}
```

<span data-ttu-id="a0972-273">La funzione JavaScript corrispondente definita nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="a0972-273">The corresponding JavaScript function defined as follows:</span></span>

```javascript
function storeAllInLocalStorage(items) {
  items.forEach(item => {
    localStorage.setItem(item.id, JSON.stringify(item));
  });
}
```

<span data-ttu-id="a0972-274">Per :::no-loc(Blazor WebAssembly)::: , questo è in genere importante solo se si sta effettuando un numero elevato di chiamate di interoperabilità js.</span><span class="sxs-lookup"><span data-stu-id="a0972-274">For :::no-loc(Blazor WebAssembly):::, this usually only matters if you're making a large number of JS interop calls.</span></span>

### <a name="consider-making-synchronous-calls"></a><span data-ttu-id="a0972-275">Prendere in considerazione l'esecuzione di chiamate sincrone</span><span class="sxs-lookup"><span data-stu-id="a0972-275">Consider making synchronous calls</span></span>

<span data-ttu-id="a0972-276">Per impostazione predefinita, le chiamate di interoperabilità JavaScript sono asincrone, indipendentemente dal fatto che il codice chiamato sia sincrono o asincrono.</span><span class="sxs-lookup"><span data-stu-id="a0972-276">JavaScript interop calls are asynchronous by default, regardless of whether the code being called is synchronous or asynchronous.</span></span> <span data-ttu-id="a0972-277">Ciò consente di garantire che i componenti siano compatibili con :::no-loc(Blazor WebAssembly)::: e :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="a0972-277">This is to ensure components are compatible with both :::no-loc(Blazor WebAssembly)::: and :::no-loc(Blazor Server):::.</span></span> <span data-ttu-id="a0972-278">In :::no-loc(Blazor Server)::: , tutte le chiamate di interoperabilità JavaScript devono essere asincrone perché vengono inviate tramite una connessione di rete.</span><span class="sxs-lookup"><span data-stu-id="a0972-278">On :::no-loc(Blazor Server):::, all JavaScript interop calls must be asynchronous because they are sent over a network connection.</span></span>

<span data-ttu-id="a0972-279">Se si è certi che l'app venga eseguita solo in :::no-loc(Blazor WebAssembly)::: , è possibile scegliere di eseguire chiamate di interoperabilità JavaScript sincrone.</span><span class="sxs-lookup"><span data-stu-id="a0972-279">If you know for certain that your app only ever runs on :::no-loc(Blazor WebAssembly):::, you can choose to make synchronous JavaScript interop calls.</span></span> <span data-ttu-id="a0972-280">Questa operazione ha un sovraccarico leggermente inferiore rispetto all'esecuzione di chiamate asincrone e può comportare un minor numero di cicli di rendering perché non esiste alcuno stato intermedio durante l'attesa dei risultati.</span><span class="sxs-lookup"><span data-stu-id="a0972-280">This has slightly less overhead than making asynchronous calls and can result in fewer render cycles because there is no intermediate state while awaiting results.</span></span>

<span data-ttu-id="a0972-281">Per eseguire una chiamata sincrona da .NET a JavaScript, eseguire il cast <xref:Microsoft.JSInterop.IJSRuntime> a <xref:Microsoft.JSInterop.IJSInProcessRuntime> :</span><span class="sxs-lookup"><span data-stu-id="a0972-281">To make a synchronous call from .NET to JavaScript, cast <xref:Microsoft.JSInterop.IJSRuntime> to <xref:Microsoft.JSInterop.IJSInProcessRuntime>:</span></span>

```razor
@inject IJSRuntime JS

...

@code {
    protected override void HandleSomeEvent()
    {
        var jsInProcess = (IJSInProcessRuntime)JS;
        var value = jsInProcess.Invoke<string>("javascriptFunctionIdentifier");
    }
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a0972-282">Quando si utilizza `IJSObjectReference` , è possibile effettuare una chiamata sincrona eseguendo il cast a `IJSInProcessObjectReference` .</span><span class="sxs-lookup"><span data-stu-id="a0972-282">When working with `IJSObjectReference`, you can make a synchronous call by casting to `IJSInProcessObjectReference`.</span></span>

::: moniker-end

<span data-ttu-id="a0972-283">Per eseguire una chiamata sincrona da JavaScript a .NET, usare `DotNet.invokeMethod` anziché `DotNet.invokeMethodAsync` .</span><span class="sxs-lookup"><span data-stu-id="a0972-283">To make a synchronous call from JavaScript to .NET, use `DotNet.invokeMethod` instead of `DotNet.invokeMethodAsync`.</span></span>

<span data-ttu-id="a0972-284">Le chiamate sincrone funzionano se:</span><span class="sxs-lookup"><span data-stu-id="a0972-284">Synchronous calls work if:</span></span>

* <span data-ttu-id="a0972-285">L'app è in esecuzione in :::no-loc(Blazor WebAssembly)::: , non :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="a0972-285">The app is running on :::no-loc(Blazor WebAssembly):::, not :::no-loc(Blazor Server):::.</span></span>
* <span data-ttu-id="a0972-286">La funzione chiamata restituisce un valore in modo sincrono (non è un `async` metodo e non restituisce .NET <xref:System.Threading.Tasks.Task> o JavaScript `Promise` ).</span><span class="sxs-lookup"><span data-stu-id="a0972-286">The called function returns a value synchronously (it isn't an `async` method and doesn't return a .NET <xref:System.Threading.Tasks.Task> or JavaScript `Promise`).</span></span>

<span data-ttu-id="a0972-287">Per altre informazioni, vedere <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="a0972-287">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

::: moniker range=">= aspnetcore-5.0"
 
### <a name="consider-making-unmarshalled-calls"></a><span data-ttu-id="a0972-288">Prendere in considerazione l'esecuzione di chiamate non Marshall</span><span class="sxs-lookup"><span data-stu-id="a0972-288">Consider making unmarshalled calls</span></span>

<span data-ttu-id="a0972-289">Quando si esegue in :::no-loc(Blazor WebAssembly)::: , è possibile effettuare chiamate non Marshall da .NET a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a0972-289">When running on :::no-loc(Blazor WebAssembly):::, it's possible to make unmarshalled calls from .NET to JavaScript.</span></span> <span data-ttu-id="a0972-290">Si tratta di chiamate sincrone che non eseguono la serializzazione JSON di argomenti o valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="a0972-290">These are synchronous calls that don't perform JSON serialization of arguments or return values.</span></span> <span data-ttu-id="a0972-291">Tutti gli aspetti della gestione della memoria e delle traduzioni tra le rappresentazioni .NET e JavaScript vengono lasciati allo sviluppatore.</span><span class="sxs-lookup"><span data-stu-id="a0972-291">All aspects of memory management and translations between .NET and JavaScript representations are left up to the developer.</span></span>

> [!WARNING]
> <span data-ttu-id="a0972-292">Quando `IJSUnmarshalledRuntime` si usa ha il minor sovraccarico degli approcci di interoperabilità js, le API JavaScript necessarie per interagire con queste API non sono attualmente documentate e sono soggette a modifiche di rilievo nelle versioni future.</span><span class="sxs-lookup"><span data-stu-id="a0972-292">While using `IJSUnmarshalledRuntime` has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

```javascript
function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
}
```

```razor
@inject IJSRuntime JS

@code {
    protected override void OnInitialized()
    {
        var unmarshalledJs = (IJSUnmarshalledRuntime)JS;
        var value = unmarshalledJs.InvokeUnmarshalled<string>("jsInteropCall");
    }
}
```

::: moniker-end

## <a name="minimize-app-download-size"></a><span data-ttu-id="a0972-293">Ridurre le dimensioni del download dell'app</span><span class="sxs-lookup"><span data-stu-id="a0972-293">Minimize app download size</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a><span data-ttu-id="a0972-294">Troncamento del linguaggio intermedio (IL)</span><span class="sxs-lookup"><span data-stu-id="a0972-294">Intermediate Language (IL) trimming</span></span>

<span data-ttu-id="a0972-295">Il [trimming degli assembly inutilizzati da un' :::no-loc(Blazor WebAssembly)::: app](xref:blazor/host-and-deploy/configure-trimmer) consente di ridurre le dimensioni dell'app rimuovendo il codice inutilizzato nei file binari dell'app.</span><span class="sxs-lookup"><span data-stu-id="a0972-295">[Trimming unused assemblies from a :::no-loc(Blazor WebAssembly)::: app](xref:blazor/host-and-deploy/configure-trimmer) reduces the app's size by removing unused code in the app's binaries.</span></span> <span data-ttu-id="a0972-296">Per impostazione predefinita, il trimmer viene eseguito durante la pubblicazione di un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="a0972-296">By default, the Trimmer is executed when publishing an application.</span></span> <span data-ttu-id="a0972-297">Per trarre vantaggio dal taglio, pubblicare l'app per la distribuzione usando il [`dotnet publish`](/dotnet/core/tools/dotnet-publish) comando con l'opzione [-c |--Configuration](/dotnet/core/tools/dotnet-publish#options) impostata su `Release` :</span><span class="sxs-lookup"><span data-stu-id="a0972-297">To benefit from trimming, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="a0972-298">Collegamento del linguaggio intermedio (IL)</span><span class="sxs-lookup"><span data-stu-id="a0972-298">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="a0972-299">Il [collegamento di un' :::no-loc(Blazor WebAssembly)::: app](xref:blazor/host-and-deploy/configure-linker) consente di ridurre le dimensioni dell'app, ritagliando il codice inutilizzato nei file binari dell'app.</span><span class="sxs-lookup"><span data-stu-id="a0972-299">[Linking a :::no-loc(Blazor WebAssembly)::: app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="a0972-300">Per impostazione predefinita, il linker linguaggio intermedio (IL) viene abilitato solo quando si compila nella `Release` configurazione.</span><span class="sxs-lookup"><span data-stu-id="a0972-300">By default, the Intermediate Language (IL) Linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="a0972-301">Per trarre vantaggio da questo problema, pubblicare l'app per la distribuzione usando il [`dotnet publish`](/dotnet/core/tools/dotnet-publish) comando con l'opzione [-c |--Configuration](/dotnet/core/tools/dotnet-publish#options) impostata su `Release` :</span><span class="sxs-lookup"><span data-stu-id="a0972-301">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="use-systemtextjson"></a><span data-ttu-id="a0972-302">USA System.Text.Js</span><span class="sxs-lookup"><span data-stu-id="a0972-302">Use System.Text.Json</span></span>

<span data-ttu-id="a0972-303">:::no-loc(Blazor):::l'implementazione dell'interoperabilità JS di è basata su <xref:System.Text.Json> , ovvero una libreria di serializzazione JSON a prestazioni elevate con allocazione di memoria insufficiente.</span><span class="sxs-lookup"><span data-stu-id="a0972-303">:::no-loc(Blazor):::'s JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="a0972-304">L'uso <xref:System.Text.Json> di non comporta dimensioni aggiuntive del payload dell'app rispetto all'aggiunta di una o più librerie JSON alternative.</span><span class="sxs-lookup"><span data-stu-id="a0972-304">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="a0972-305">Per informazioni aggiuntive sulla migrazione, vedere [come eseguire la migrazione da `Newtonsoft.Json` a `System.Text.Json` ](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="a0972-305">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

### <a name="lazy-load-assemblies"></a><span data-ttu-id="a0972-306">Assembly di caricamento lazy</span><span class="sxs-lookup"><span data-stu-id="a0972-306">Lazy load assemblies</span></span>

<span data-ttu-id="a0972-307">Caricare gli assembly in fase di esecuzione quando gli assembly sono necessari per una route.</span><span class="sxs-lookup"><span data-stu-id="a0972-307">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="a0972-308">Per altre informazioni, vedere <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="a0972-308">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="a0972-309">Compressione</span><span class="sxs-lookup"><span data-stu-id="a0972-309">Compression</span></span>

<span data-ttu-id="a0972-310">Quando :::no-loc(Blazor WebAssembly)::: viene pubblicata un'app, l'output viene compresso in modo statico durante la pubblicazione per ridurre le dimensioni dell'app e rimuovere l'overhead per la compressione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="a0972-310">When a :::no-loc(Blazor WebAssembly)::: app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="a0972-311">:::no-loc(Blazor)::: si basa sul server per eseguire il contenuto negotation e gestire file compressi in modo statico.</span><span class="sxs-lookup"><span data-stu-id="a0972-311">:::no-loc(Blazor)::: relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="a0972-312">Dopo che un'app è stata distribuita, verificare che l'app servi i file compressi.</span><span class="sxs-lookup"><span data-stu-id="a0972-312">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="a0972-313">Controllare la scheda rete nel Strumenti di sviluppo del browser e verificare che i file siano serviti con `Content-Encoding: br` o `Content-Encoding: gz` .</span><span class="sxs-lookup"><span data-stu-id="a0972-313">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="a0972-314">Se l'host non serve file compressi, seguire le istruzioni riportate in <xref:blazor/host-and-deploy/webassembly#compression> .</span><span class="sxs-lookup"><span data-stu-id="a0972-314">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="a0972-315">Disabilitare le funzionalità inutilizzate</span><span class="sxs-lookup"><span data-stu-id="a0972-315">Disable unused features</span></span>

<span data-ttu-id="a0972-316">:::no-loc(Blazor WebAssembly):::il runtime di include le seguenti funzionalità .NET che possono essere disabilitate se l'app non le richiede per una dimensione di payload inferiore:</span><span class="sxs-lookup"><span data-stu-id="a0972-316">:::no-loc(Blazor WebAssembly):::'s runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="a0972-317">Un file di dati è incluso per rendere corrette le informazioni sul fuso orario.</span><span class="sxs-lookup"><span data-stu-id="a0972-317">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="a0972-318">Se l'app non richiede questa funzionalità, provare a disabilitarla impostando la `:::no-loc(Blazor):::EnableTimeZoneSupport` Proprietà MSBuild nel file di progetto dell'app su `false` :</span><span class="sxs-lookup"><span data-stu-id="a0972-318">If the app doesn't require this feature, consider disabling it by setting the `:::no-loc(Blazor):::EnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <:::no-loc(Blazor):::EnableTimeZoneSupport>false</:::no-loc(Blazor):::EnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="a0972-319">Per impostazione predefinita, include :::no-loc(Blazor WebAssembly)::: le risorse di globalizzazione necessarie per visualizzare i valori, ad esempio le date e la valuta, nelle impostazioni cultura dell'utente.</span><span class="sxs-lookup"><span data-stu-id="a0972-319">By default, :::no-loc(Blazor WebAssembly)::: carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="a0972-320">Se l'app non richiede la localizzazione, è possibile [configurare l'app in modo che supporti la lingua inglese](xref:blazor/globalization-localization), che è basata sulle `en-US` impostazioni cultura:</span><span class="sxs-lookup"><span data-stu-id="a0972-320">If the app doesn't require localization, you may [configure the app to support the invariant culture](xref:blazor/globalization-localization), which is based on the `en-US` culture:</span></span>

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="a0972-321">Le informazioni sulle regole di confronto sono incluse per fare in modo che le API <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> funzionino correttamente.</span><span class="sxs-lookup"><span data-stu-id="a0972-321">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="a0972-322">Se si è certi che l'app non richiede i dati delle regole di confronto, provare a disabilitarla impostando la `:::no-loc(Blazor):::WebAssemblyPreserveCollationData` Proprietà MSBuild nel file di progetto dell'app su `false` :</span><span class="sxs-lookup"><span data-stu-id="a0972-322">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `:::no-loc(Blazor):::WebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <:::no-loc(Blazor):::WebAssemblyPreserveCollationData>false</:::no-loc(Blazor):::WebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
