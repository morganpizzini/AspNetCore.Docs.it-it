---
title: Scenari Blazor ASP.NET Core avanzati
author: guardrex
description: Informazioni sugli scenari avanzati in Blazor, tra cui come incorporare la logica RenderTreeBuilder manuale in un'app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 9f1e5ea4d883a027f40ac0eccc7a9bba1435139d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767200"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="1fa99-103">Scenari avanzati di ASP.NET Core Blazer</span><span class="sxs-lookup"><span data-stu-id="1fa99-103">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="1fa99-104">Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="1fa99-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="blazor-server-circuit-handler"></a><span data-ttu-id="1fa99-105">Gestore circuito del server Blazer</span><span class="sxs-lookup"><span data-stu-id="1fa99-105">Blazor Server circuit handler</span></span>

<span data-ttu-id="1fa99-106">Il server Blazer consente al codice di definire un *gestore di circuito*, che consente l'esecuzione di codice in base alle modifiche apportate allo stato del circuito di un utente.</span><span class="sxs-lookup"><span data-stu-id="1fa99-106">Blazor Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="1fa99-107">Un gestore di circuito viene implementato tramite la derivazione da `CircuitHandler` e la registrazione della classe nel contenitore del servizio dell'app.</span><span class="sxs-lookup"><span data-stu-id="1fa99-107">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="1fa99-108">L'esempio seguente di un gestore di circuito tiene traccia delle connessioni a SignalR aperte:</span><span class="sxs-lookup"><span data-stu-id="1fa99-108">The following example of a circuit handler tracks open SignalR connections:</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> _circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => _circuits.Count;
}
```

<span data-ttu-id="1fa99-109">I gestori del circuito vengono registrati usando l'inserimento DI dipendenze.</span><span class="sxs-lookup"><span data-stu-id="1fa99-109">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="1fa99-110">Le istanze con ambito vengono create per ogni istanza di un circuito.</span><span class="sxs-lookup"><span data-stu-id="1fa99-110">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="1fa99-111">`TrackingCircuitHandler` Utilizzando nell'esempio precedente, viene creato un servizio singleton perché è necessario tenere traccia dello stato di tutti i circuiti:</span><span class="sxs-lookup"><span data-stu-id="1fa99-111">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="1fa99-112">Se i metodi di un gestore di circuito personalizzato generano un'eccezione non gestita, l'eccezione è fatale per il circuito del server blazer.</span><span class="sxs-lookup"><span data-stu-id="1fa99-112">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="1fa99-113">Per tollerare le eccezioni nel codice di un gestore o i metodi chiamati, eseguire il wrapping del codice in una o più istruzioni [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con gestione e registrazione degli errori.</span><span class="sxs-lookup"><span data-stu-id="1fa99-113">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="1fa99-114">Quando un circuito termina perché un utente si è disconnesso e il Framework pulisce lo stato del circuito, il Framework Elimina l'ambito di.</span><span class="sxs-lookup"><span data-stu-id="1fa99-114">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="1fa99-115">Con l'eliminazione dell'ambito vengono eliminati tutti i servizi con ambito DI circuito che <xref:System.IDisposable?displayProperty=fullName>implementano.</span><span class="sxs-lookup"><span data-stu-id="1fa99-115">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="1fa99-116">Se un servizio DI INSERIMENTO DI dipendenze genera un'eccezione non gestita durante l'eliminazione, il Framework registra l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="1fa99-116">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="1fa99-117">Logica RenderTreeBuilder manuale</span><span class="sxs-lookup"><span data-stu-id="1fa99-117">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="1fa99-118">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder`fornisce metodi per la modifica di componenti ed elementi, inclusa la compilazione manuale di componenti nel codice C#.</span><span class="sxs-lookup"><span data-stu-id="1fa99-118">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="1fa99-119">L'utilizzo `RenderTreeBuilder` di per la creazione di componenti è uno scenario avanzato.</span><span class="sxs-lookup"><span data-stu-id="1fa99-119">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="1fa99-120">Un componente con formato non valido, ad esempio un tag di markup non chiuso, può causare un comportamento indefinito.</span><span class="sxs-lookup"><span data-stu-id="1fa99-120">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="1fa99-121">Si consideri il componente seguente `PetDetails` , che può essere incorporato manualmente in un altro componente:</span><span class="sxs-lookup"><span data-stu-id="1fa99-121">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="1fa99-122">Nell'esempio seguente il ciclo nel `CreateComponent` metodo genera tre `PetDetails` componenti.</span><span class="sxs-lookup"><span data-stu-id="1fa99-122">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="1fa99-123">Quando si `RenderTreeBuilder` chiamano i metodi per creare i`OpenComponent` componenti `AddAttribute`(e), i numeri di sequenza sono numeri di riga del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="1fa99-123">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="1fa99-124">L'algoritmo di differenza Blaze si basa sui numeri di sequenza corrispondenti a righe di codice distinte, non sulle chiamate di chiamata distinti.</span><span class="sxs-lookup"><span data-stu-id="1fa99-124">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="1fa99-125">Quando si crea un componente `RenderTreeBuilder` con i metodi, impostare come hardcoded gli argomenti per i numeri di sequenza.</span><span class="sxs-lookup"><span data-stu-id="1fa99-125">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="1fa99-126">**L'utilizzo di un calcolo o di un contatore per generare il numero di sequenza può causare un calo delle prestazioni.**</span><span class="sxs-lookup"><span data-stu-id="1fa99-126">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="1fa99-127">Per ulteriori informazioni, vedere la sezione [numeri di sequenza correlati ai numeri di riga del codice e non all'ordine di esecuzione](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="1fa99-127">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="1fa99-128">`BuiltContent`componente</span><span class="sxs-lookup"><span data-stu-id="1fa99-128">`BuiltContent` component:</span></span>

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> <span data-ttu-id="1fa99-129">I tipi in `Microsoft.AspNetCore.Components.RenderTree` consentono l'elaborazione dei *risultati* delle operazioni di rendering.</span><span class="sxs-lookup"><span data-stu-id="1fa99-129">The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="1fa99-130">Questi sono i dettagli interni dell'implementazione del Framework blazer.</span><span class="sxs-lookup"><span data-stu-id="1fa99-130">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="1fa99-131">Questi tipi devono essere considerati *instabili* e soggetti a modifiche nelle versioni future.</span><span class="sxs-lookup"><span data-stu-id="1fa99-131">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="1fa99-132">I numeri di sequenza sono correlati ai numeri di riga del codice e non all'ordine di esecuzione</span><span class="sxs-lookup"><span data-stu-id="1fa99-132">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="1fa99-133">I file dei componenti Razor (*Razor*) vengono sempre compilati.</span><span class="sxs-lookup"><span data-stu-id="1fa99-133">Razor component files (*.razor*) are always compiled.</span></span> <span data-ttu-id="1fa99-134">La compilazione è un potenziale vantaggio rispetto all'interpretazione del codice perché il passaggio di compilazione può essere usato per inserire informazioni che migliorano le prestazioni dell'app in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="1fa99-134">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="1fa99-135">Un esempio fondamentale di questi miglioramenti riguarda i *numeri di sequenza*.</span><span class="sxs-lookup"><span data-stu-id="1fa99-135">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="1fa99-136">I numeri di sequenza indicano al runtime quali output provengono da righe di codice distinte e ordinate.</span><span class="sxs-lookup"><span data-stu-id="1fa99-136">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="1fa99-137">Il runtime usa queste informazioni per generare differenze di albero efficienti nel tempo lineare, che è molto più veloce rispetto a quanto normalmente è possibile per un algoritmo diff della struttura ad albero generale.</span><span class="sxs-lookup"><span data-stu-id="1fa99-137">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="1fa99-138">Si consideri il seguente file di componente Razor (*Razor*):</span><span class="sxs-lookup"><span data-stu-id="1fa99-138">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="1fa99-139">Il codice precedente viene compilato in un modo simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="1fa99-139">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="1fa99-140">Quando il codice viene eseguito per la prima volta, se `someFlag` è `true`, il generatore riceve:</span><span class="sxs-lookup"><span data-stu-id="1fa99-140">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="1fa99-141">Sequenza</span><span class="sxs-lookup"><span data-stu-id="1fa99-141">Sequence</span></span> | <span data-ttu-id="1fa99-142">Type</span><span class="sxs-lookup"><span data-stu-id="1fa99-142">Type</span></span>      | <span data-ttu-id="1fa99-143">Data</span><span class="sxs-lookup"><span data-stu-id="1fa99-143">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="1fa99-144">0</span><span class="sxs-lookup"><span data-stu-id="1fa99-144">0</span></span>        | <span data-ttu-id="1fa99-145">Nodo testo</span><span class="sxs-lookup"><span data-stu-id="1fa99-145">Text node</span></span> | <span data-ttu-id="1fa99-146">First (Primo)</span><span class="sxs-lookup"><span data-stu-id="1fa99-146">First</span></span>  |
| <span data-ttu-id="1fa99-147">1</span><span class="sxs-lookup"><span data-stu-id="1fa99-147">1</span></span>        | <span data-ttu-id="1fa99-148">Nodo testo</span><span class="sxs-lookup"><span data-stu-id="1fa99-148">Text node</span></span> | <span data-ttu-id="1fa99-149">Second</span><span class="sxs-lookup"><span data-stu-id="1fa99-149">Second</span></span> |

<span data-ttu-id="1fa99-150">Si supponga `someFlag` che `false`diventi e che venga eseguito nuovamente il rendering del markup.</span><span class="sxs-lookup"><span data-stu-id="1fa99-150">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="1fa99-151">Questa volta, il generatore riceve:</span><span class="sxs-lookup"><span data-stu-id="1fa99-151">This time, the builder receives:</span></span>

| <span data-ttu-id="1fa99-152">Sequenza</span><span class="sxs-lookup"><span data-stu-id="1fa99-152">Sequence</span></span> | <span data-ttu-id="1fa99-153">Type</span><span class="sxs-lookup"><span data-stu-id="1fa99-153">Type</span></span>       | <span data-ttu-id="1fa99-154">Data</span><span class="sxs-lookup"><span data-stu-id="1fa99-154">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="1fa99-155">1</span><span class="sxs-lookup"><span data-stu-id="1fa99-155">1</span></span>        | <span data-ttu-id="1fa99-156">Nodo testo</span><span class="sxs-lookup"><span data-stu-id="1fa99-156">Text node</span></span>  | <span data-ttu-id="1fa99-157">Second</span><span class="sxs-lookup"><span data-stu-id="1fa99-157">Second</span></span> |

<span data-ttu-id="1fa99-158">Quando il runtime esegue una diff, rileva che l'elemento in sequenza `0` è stato rimosso, quindi genera lo script di *modifica*semplice seguente:</span><span class="sxs-lookup"><span data-stu-id="1fa99-158">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="1fa99-159">Rimuovere il primo nodo di testo.</span><span class="sxs-lookup"><span data-stu-id="1fa99-159">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="1fa99-160">Il problema della generazione di numeri di sequenza a livello di codice</span><span class="sxs-lookup"><span data-stu-id="1fa99-160">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="1fa99-161">Si supponga invece che sia stata scritta la logica del generatore di albero di rendering seguente:</span><span class="sxs-lookup"><span data-stu-id="1fa99-161">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="1fa99-162">A questo punto, il primo output è:</span><span class="sxs-lookup"><span data-stu-id="1fa99-162">Now, the first output is:</span></span>

| <span data-ttu-id="1fa99-163">Sequenza</span><span class="sxs-lookup"><span data-stu-id="1fa99-163">Sequence</span></span> | <span data-ttu-id="1fa99-164">Type</span><span class="sxs-lookup"><span data-stu-id="1fa99-164">Type</span></span>      | <span data-ttu-id="1fa99-165">Data</span><span class="sxs-lookup"><span data-stu-id="1fa99-165">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="1fa99-166">0</span><span class="sxs-lookup"><span data-stu-id="1fa99-166">0</span></span>        | <span data-ttu-id="1fa99-167">Nodo testo</span><span class="sxs-lookup"><span data-stu-id="1fa99-167">Text node</span></span> | <span data-ttu-id="1fa99-168">First (Primo)</span><span class="sxs-lookup"><span data-stu-id="1fa99-168">First</span></span>  |
| <span data-ttu-id="1fa99-169">1</span><span class="sxs-lookup"><span data-stu-id="1fa99-169">1</span></span>        | <span data-ttu-id="1fa99-170">Nodo testo</span><span class="sxs-lookup"><span data-stu-id="1fa99-170">Text node</span></span> | <span data-ttu-id="1fa99-171">Second</span><span class="sxs-lookup"><span data-stu-id="1fa99-171">Second</span></span> |

<span data-ttu-id="1fa99-172">Questo risultato è identico al caso precedente, pertanto non esistono problemi negativi.</span><span class="sxs-lookup"><span data-stu-id="1fa99-172">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="1fa99-173">`someFlag`si `false` trova nel secondo rendering e l'output è:</span><span class="sxs-lookup"><span data-stu-id="1fa99-173">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="1fa99-174">Sequenza</span><span class="sxs-lookup"><span data-stu-id="1fa99-174">Sequence</span></span> | <span data-ttu-id="1fa99-175">Type</span><span class="sxs-lookup"><span data-stu-id="1fa99-175">Type</span></span>      | <span data-ttu-id="1fa99-176">Data</span><span class="sxs-lookup"><span data-stu-id="1fa99-176">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="1fa99-177">0</span><span class="sxs-lookup"><span data-stu-id="1fa99-177">0</span></span>        | <span data-ttu-id="1fa99-178">Nodo testo</span><span class="sxs-lookup"><span data-stu-id="1fa99-178">Text node</span></span> | <span data-ttu-id="1fa99-179">Second</span><span class="sxs-lookup"><span data-stu-id="1fa99-179">Second</span></span> |

<span data-ttu-id="1fa99-180">Questa volta, l'algoritmo Diff rileva che si sono verificate *due* modifiche e l'algoritmo genera lo script di modifica seguente:</span><span class="sxs-lookup"><span data-stu-id="1fa99-180">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="1fa99-181">Modificare il valore del primo nodo di testo in `Second`.</span><span class="sxs-lookup"><span data-stu-id="1fa99-181">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="1fa99-182">Rimuovere il secondo nodo di testo.</span><span class="sxs-lookup"><span data-stu-id="1fa99-182">Remove the second text node.</span></span>

<span data-ttu-id="1fa99-183">La generazione dei numeri di sequenza ha perso tutte le informazioni utili sul `if/else` punto in cui i rami e i cicli erano presenti nel codice originale.</span><span class="sxs-lookup"><span data-stu-id="1fa99-183">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="1fa99-184">In questo modo si ottiene una differenza **due volte più a lungo** .</span><span class="sxs-lookup"><span data-stu-id="1fa99-184">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="1fa99-185">Questo è un esempio semplice.</span><span class="sxs-lookup"><span data-stu-id="1fa99-185">This is a trivial example.</span></span> <span data-ttu-id="1fa99-186">Nei casi più realistici con strutture complesse e profondamente annidate e soprattutto con i cicli, il costo delle prestazioni è in genere più elevato.</span><span class="sxs-lookup"><span data-stu-id="1fa99-186">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="1fa99-187">Invece di identificare immediatamente i blocchi o i rami del ciclo che sono stati inseriti o rimossi, l'algoritmo Diff deve ripresentarsi in modo approfondito negli alberi di rendering.</span><span class="sxs-lookup"><span data-stu-id="1fa99-187">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="1fa99-188">Ciò comporta in genere la necessità di compilare script di modifica più lunghi perché l'algoritmo Diff viene informato in modo non più approfondito sulle relazioni tra le vecchie e le nuove strutture.</span><span class="sxs-lookup"><span data-stu-id="1fa99-188">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="1fa99-189">Linee guida e conclusioni</span><span class="sxs-lookup"><span data-stu-id="1fa99-189">Guidance and conclusions</span></span>

* <span data-ttu-id="1fa99-190">Le prestazioni dell'app soffrono se i numeri di sequenza vengono generati dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="1fa99-190">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="1fa99-191">Il Framework non è in grado di creare automaticamente i propri numeri di sequenza in fase di esecuzione perché le informazioni necessarie non esistono a meno che non vengano acquisite in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="1fa99-191">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="1fa99-192">Non scrivere blocchi lunghi di logica implementata `RenderTreeBuilder` manualmente.</span><span class="sxs-lookup"><span data-stu-id="1fa99-192">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="1fa99-193">Preferire i file *Razor* e consentire al compilatore di gestire i numeri di sequenza.</span><span class="sxs-lookup"><span data-stu-id="1fa99-193">Prefer *.razor* files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="1fa99-194">Se non si è in grado di `RenderTreeBuilder` evitare la logica manuale, suddividere i blocchi di codice lunghi `OpenRegion` / `CloseRegion` in parti più piccole racchiuse tra le chiamate.</span><span class="sxs-lookup"><span data-stu-id="1fa99-194">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="1fa99-195">Ogni area ha il proprio spazio separato dei numeri di sequenza, quindi è possibile riavviare da zero (o qualsiasi altro numero arbitrario) all'interno di ogni area.</span><span class="sxs-lookup"><span data-stu-id="1fa99-195">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="1fa99-196">Se i numeri di sequenza sono hardcoded, l'algoritmo Diff richiede solo che i numeri di sequenza aumentino nel valore.</span><span class="sxs-lookup"><span data-stu-id="1fa99-196">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="1fa99-197">Il valore iniziale e i gap sono irrilevanti.</span><span class="sxs-lookup"><span data-stu-id="1fa99-197">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="1fa99-198">Una delle opzioni legittime consiste nell'usare il numero di riga del codice come numero di sequenza oppure iniziare da zero e aumentare di uno o di centinaia (o qualsiasi intervallo preferito).</span><span class="sxs-lookup"><span data-stu-id="1fa99-198">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="1fa99-199">USA i numeri di sequenza, mentre altri Framework dell'interfaccia utente con differenze tra gli alberi non li usano.</span><span class="sxs-lookup"><span data-stu-id="1fa99-199"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="1fa99-200">La diffing è molto più veloce quando si usano i numeri Blazor di sequenza e presenta il vantaggio di un passaggio di compilazione che gestisce automaticamente i numeri di sequenza per gli sviluppatori che creano file con *estensione Razor* .</span><span class="sxs-lookup"><span data-stu-id="1fa99-200">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="perform-large-data-transfers-in-blazor-server-apps"></a><span data-ttu-id="1fa99-201">Eseguire trasferimenti di dati di Blazor grandi dimensioni nelle app Server</span><span class="sxs-lookup"><span data-stu-id="1fa99-201">Perform large data transfers in Blazor Server apps</span></span>

<span data-ttu-id="1fa99-202">In alcuni scenari, è necessario trasferire grandi quantità di dati tra JavaScript e Blazor.</span><span class="sxs-lookup"><span data-stu-id="1fa99-202">In some scenarios, large amounts of data must be transferred between JavaScript and Blazor.</span></span> <span data-ttu-id="1fa99-203">Generalmente, i trasferimenti di dati di grandi dimensioni si verificano quando:</span><span class="sxs-lookup"><span data-stu-id="1fa99-203">Typically, large data transfers occur when:</span></span>

* <span data-ttu-id="1fa99-204">Le API del browser file system vengono usate per caricare o scaricare un file.</span><span class="sxs-lookup"><span data-stu-id="1fa99-204">Browser file system APIs are used to upload or download a file.</span></span>
* <span data-ttu-id="1fa99-205">È necessaria l'interoperabilità con una libreria di terze parti.</span><span class="sxs-lookup"><span data-stu-id="1fa99-205">Interop with a third party library is required.</span></span>

<span data-ttu-id="1fa99-206">In Blazor server è prevista una limitazione per impedire il passaggio di messaggi di grandi dimensioni che possono causare problemi di prestazioni.</span><span class="sxs-lookup"><span data-stu-id="1fa99-206">In Blazor Server, a limitation is in place to prevent passing single large messages that may result in performance issues.</span></span>

<span data-ttu-id="1fa99-207">Quando si sviluppa codice che trasferisce i dati tra JavaScript, Blazortenere presenti le linee guida seguenti:</span><span class="sxs-lookup"><span data-stu-id="1fa99-207">Consider the following guidance when developing code that transfers data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="1fa99-208">Sezionare i dati in parti più piccole e inviare i segmenti di dati in sequenza fino a quando non vengono ricevuti tutti i dati dal server.</span><span class="sxs-lookup"><span data-stu-id="1fa99-208">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="1fa99-209">Non allocare oggetti di grandi dimensioni in codice JavaScript e C#.</span><span class="sxs-lookup"><span data-stu-id="1fa99-209">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="1fa99-210">Non bloccare il thread principale dell'interfaccia utente per periodi prolungati durante l'invio o la ricezione di dati.</span><span class="sxs-lookup"><span data-stu-id="1fa99-210">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="1fa99-211">Liberare la memoria utilizzata quando il processo viene completato o annullato.</span><span class="sxs-lookup"><span data-stu-id="1fa99-211">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="1fa99-212">Per motivi di sicurezza, applicare i seguenti requisiti aggiuntivi:</span><span class="sxs-lookup"><span data-stu-id="1fa99-212">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="1fa99-213">Dichiarare le dimensioni massime di file o dati che è possibile passare.</span><span class="sxs-lookup"><span data-stu-id="1fa99-213">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="1fa99-214">Dichiarare la velocità di caricamento minima dal client al server.</span><span class="sxs-lookup"><span data-stu-id="1fa99-214">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="1fa99-215">Dopo la ricezione dei dati da parte del server, i dati possono essere:</span><span class="sxs-lookup"><span data-stu-id="1fa99-215">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="1fa99-216">Archiviati temporaneamente in un buffer di memoria fino a quando non vengono raccolti tutti i segmenti.</span><span class="sxs-lookup"><span data-stu-id="1fa99-216">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="1fa99-217">Utilizzato immediatamente.</span><span class="sxs-lookup"><span data-stu-id="1fa99-217">Consumed immediately.</span></span> <span data-ttu-id="1fa99-218">Ad esempio, i dati possono essere archiviati immediatamente in un database o scritti su disco durante la ricezione di ogni segmento.</span><span class="sxs-lookup"><span data-stu-id="1fa99-218">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

<span data-ttu-id="1fa99-219">La classe del caricatore di file seguente gestisce l'interoperabilità JS con il client.</span><span class="sxs-lookup"><span data-stu-id="1fa99-219">The following file uploader class handles JS interop with the client.</span></span> <span data-ttu-id="1fa99-220">La classe Uploader usa l'interoperabilità JS per:</span><span class="sxs-lookup"><span data-stu-id="1fa99-220">The uploader class uses JS interop to:</span></span>

* <span data-ttu-id="1fa99-221">Eseguire il polling del client per inviare un segmento di dati.</span><span class="sxs-lookup"><span data-stu-id="1fa99-221">Poll the client to send a data segment.</span></span>
* <span data-ttu-id="1fa99-222">Interrompere la transazione se si verifica il timeout del polling.</span><span class="sxs-lookup"><span data-stu-id="1fa99-222">Abort the transaction if polling times out.</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private readonly int _segmentSize = 6144;
    private readonly int _maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> _thisReference;
    private List<IMemoryOwner<byte>> _uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await _jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)_segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await _jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < _maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(_segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, _segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          _uploadedSegments.Add(current);
        }

        var segments = _uploadedSegments;
        _uploadedSegments = null;

        return new SegmentedStream(segments, _segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (_uploadedSegments != null)
        {
            foreach (var segment in _uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

<span data-ttu-id="1fa99-223">Nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="1fa99-223">In the preceding example:</span></span>

* <span data-ttu-id="1fa99-224">`_maxBase64SegmentSize` È impostato su `8192`, che viene calcolato da `_maxBase64SegmentSize = _segmentSize * 4 / 3`.</span><span class="sxs-lookup"><span data-stu-id="1fa99-224">The `_maxBase64SegmentSize` is set to `8192`, which is calculated from `_maxBase64SegmentSize = _segmentSize * 4 / 3`.</span></span>
* <span data-ttu-id="1fa99-225">Le API di gestione della memoria .NET Core di basso livello vengono usate per archiviare i segmenti di memoria `_uploadedSegments`nel server in.</span><span class="sxs-lookup"><span data-stu-id="1fa99-225">Low-level .NET Core memory management APIs are used to store the memory segments on the server in `_uploadedSegments`.</span></span>
* <span data-ttu-id="1fa99-226">Viene `ReceiveFile` usato un metodo per gestire il caricamento tramite l'interoperabilità JS:</span><span class="sxs-lookup"><span data-stu-id="1fa99-226">A `ReceiveFile` method is used to handle the upload through JS interop:</span></span>
  * <span data-ttu-id="1fa99-227">Le dimensioni del file vengono determinate in byte tramite l'interoperabilità JS con `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span><span class="sxs-lookup"><span data-stu-id="1fa99-227">The file size is determined in bytes through JS interop with `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span></span>
  * <span data-ttu-id="1fa99-228">Il numero di segmenti da ricevere viene calcolato e archiviato in `numberOfSegments`.</span><span class="sxs-lookup"><span data-stu-id="1fa99-228">The number of segments to receive are calculated and stored in `numberOfSegments`.</span></span>
  * <span data-ttu-id="1fa99-229">I segmenti sono richiesti in un `for` ciclo tramite l'interoperabilità js con `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span><span class="sxs-lookup"><span data-stu-id="1fa99-229">The segments are requested in a `for` loop through JS interop with `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span></span> <span data-ttu-id="1fa99-230">Tutti i segmenti ma l'ultimo devono essere 8.192 byte prima della decodifica.</span><span class="sxs-lookup"><span data-stu-id="1fa99-230">All segments but the last must be 8,192 bytes before decoding.</span></span> <span data-ttu-id="1fa99-231">Il client è forzato a inviare i dati in modo efficiente.</span><span class="sxs-lookup"><span data-stu-id="1fa99-231">The client is forced to send the data in an efficient manner.</span></span>
  * <span data-ttu-id="1fa99-232">Per ogni segmento ricevuto, i controlli vengono eseguiti prima della decodifica <xref:System.Convert.TryFromBase64String*>con.</span><span class="sxs-lookup"><span data-stu-id="1fa99-232">For each segment received, checks are performed before decoding with <xref:System.Convert.TryFromBase64String*>.</span></span>
  * <span data-ttu-id="1fa99-233">Un flusso con i dati viene restituito come nuovo <xref:System.IO.Stream> (`SegmentedStream`) dopo il completamento del caricamento.</span><span class="sxs-lookup"><span data-stu-id="1fa99-233">A stream with the data is returned as a new <xref:System.IO.Stream> (`SegmentedStream`) after the upload is complete.</span></span>

<span data-ttu-id="1fa99-234">La classe Stream segmentata espone l'elenco di segmenti come ReadOnly non ricercabile <xref:System.IO.Stream>:</span><span class="sxs-lookup"><span data-stu-id="1fa99-234">The segmented stream class exposes the list of segments as a readonly non-seekable <xref:System.IO.Stream>:</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> _sequence;
    private long _currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            _sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        _sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(_currentPosition + count < _sequence.Length ? 
            count : _sequence.Length - _currentPosition);
        var data = _sequence.Slice(_currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        _currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

<span data-ttu-id="1fa99-235">Il codice seguente implementa le funzioni JavaScript per ricevere i dati:</span><span class="sxs-lookup"><span data-stu-id="1fa99-235">The following code implements JavaScript functions to receive the data:</span></span>

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```
