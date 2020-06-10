---
title: Gestione degli errori nelle Blazor app ASP.NET Core
author: guardrex
description: Scopri in che modo ASP.NET Core Blazor come Blazor gestisce le eccezioni non gestite e come sviluppare app che rilevano e gestiscono gli errori.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: a66075b4c45f2d7767d2ed1950a44a0ba88c7d17
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "83864553"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a><span data-ttu-id="f9655-103">Gestione degli errori nelle Blazor app ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f9655-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="f9655-104">Di [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="f9655-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="f9655-105">Questo articolo descrive come Blazor gestire le eccezioni non gestite e come sviluppare app che rilevano e gestiscono gli errori.</span><span class="sxs-lookup"><span data-stu-id="f9655-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="f9655-106">Errori dettagliati durante lo sviluppo</span><span class="sxs-lookup"><span data-stu-id="f9655-106">Detailed errors during development</span></span>

<span data-ttu-id="f9655-107">Quando un' Blazor app non funziona correttamente durante lo sviluppo, la ricezione di informazioni dettagliate sugli errori dall'app è utile per la risoluzione dei problemi e per risolvere il problema.</span><span class="sxs-lookup"><span data-stu-id="f9655-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="f9655-108">Quando si verifica un errore, le Blazor app visualizzano una barra dorata nella parte inferiore della schermata:</span><span class="sxs-lookup"><span data-stu-id="f9655-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="f9655-109">Durante lo sviluppo, la barra dorata indirizza l'utente alla console del browser, in cui è possibile visualizzare l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="f9655-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="f9655-110">In produzione, la barra dorata informa l'utente che si è verificato un errore e consiglia di aggiornare il browser.</span><span class="sxs-lookup"><span data-stu-id="f9655-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="f9655-111">L'interfaccia utente per questa esperienza di gestione degli errori fa parte dei Blazor modelli di progetto.</span><span class="sxs-lookup"><span data-stu-id="f9655-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="f9655-112">In un' Blazor app webassembly personalizzare l'esperienza nel file *wwwroot/index.html* :</span><span class="sxs-lookup"><span data-stu-id="f9655-112">In a Blazor WebAssembly app, customize the experience in the *wwwroot/index.html* file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="f9655-113">In un' Blazor app Server, personalizzare l'esperienza nel file *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f9655-113">In a Blazor Server app, customize the experience in the *Pages/_Host.cshtml* file:</span></span>

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="f9655-114">L' `blazor-error-ui` elemento è nascosto dagli stili inclusi nei Blazor modelli (*wwwroot/CSS/site. CSS*) e quindi mostrati quando si verifica un errore:</span><span class="sxs-lookup"><span data-stu-id="f9655-114">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (*wwwroot/css/site.css*) and then shown when an error occurs:</span></span>

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="f9655-115">BlazorReazione di un'app Server a eccezioni non gestite</span><span class="sxs-lookup"><span data-stu-id="f9655-115">How a Blazor Server app reacts to unhandled exceptions</span></span>

Blazor<span data-ttu-id="f9655-116">Il server è un Framework con stato.</span><span class="sxs-lookup"><span data-stu-id="f9655-116"> Server is a stateful framework.</span></span> <span data-ttu-id="f9655-117">Mentre gli utenti interagiscono con un'app, mantengono una connessione al server noto come *circuito*.</span><span class="sxs-lookup"><span data-stu-id="f9655-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="f9655-118">Il circuito include istanze di componenti attive, oltre a molti altri aspetti dello stato, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f9655-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="f9655-119">Output più recente sottoposto a rendering dei componenti.</span><span class="sxs-lookup"><span data-stu-id="f9655-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="f9655-120">Set corrente di delegati di gestione degli eventi che possono essere attivati da eventi lato client.</span><span class="sxs-lookup"><span data-stu-id="f9655-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="f9655-121">Se un utente apre l'app in più schede del browser, avrà più circuiti indipendenti.</span><span class="sxs-lookup"><span data-stu-id="f9655-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

Blazor<span data-ttu-id="f9655-122">Considera le eccezioni non gestite come irreversibili per il circuito in cui si verificano.</span><span class="sxs-lookup"><span data-stu-id="f9655-122"> treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="f9655-123">Se un circuito viene terminato a causa di un'eccezione non gestita, l'utente può continuare a interagire con l'app ricaricando la pagina per creare un nuovo circuito.</span><span class="sxs-lookup"><span data-stu-id="f9655-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="f9655-124">I circuiti al di fuori di quello terminato, ovvero circuiti per altri utenti o altre schede del browser, non sono interessati.</span><span class="sxs-lookup"><span data-stu-id="f9655-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="f9655-125">Questo scenario è simile a un'applicazione desktop che si arresta in modo anomalo.</span><span class="sxs-lookup"><span data-stu-id="f9655-125">This scenario is similar to a desktop app that crashes.</span></span> <span data-ttu-id="f9655-126">L'app arrestata in modo anomalo deve essere riavviata, ma non sono interessate altre app.</span><span class="sxs-lookup"><span data-stu-id="f9655-126">The crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="f9655-127">Un circuito viene terminato quando si verifica un'eccezione non gestita per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="f9655-127">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="f9655-128">Un'eccezione non gestita spesso lascia il circuito in uno stato non definito.</span><span class="sxs-lookup"><span data-stu-id="f9655-128">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="f9655-129">L'operazione normale dell'app non può essere garantita dopo un'eccezione non gestita.</span><span class="sxs-lookup"><span data-stu-id="f9655-129">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="f9655-130">Le vulnerabilità di sicurezza possono essere visualizzate nell'app se il circuito continua.</span><span class="sxs-lookup"><span data-stu-id="f9655-130">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="f9655-131">Gestire le eccezioni non gestite nel codice dello sviluppatore</span><span class="sxs-lookup"><span data-stu-id="f9655-131">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="f9655-132">Per continuare l'applicazione dopo un errore, l'app deve avere la logica di gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="f9655-132">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="f9655-133">Le sezioni successive di questo articolo descrivono le potenziali fonti di eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="f9655-133">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="f9655-134">In produzione, non eseguire il rendering dei messaggi di eccezione del Framework o delle analisi dello stack nell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="f9655-134">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="f9655-135">Il rendering dei messaggi di eccezione o delle analisi dello stack potrebbe:</span><span class="sxs-lookup"><span data-stu-id="f9655-135">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="f9655-136">Divulgare informazioni riservate agli utenti finali.</span><span class="sxs-lookup"><span data-stu-id="f9655-136">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="f9655-137">Aiutare un utente malintenzionato a individuare i punti deboli in un'app che può compromettere la sicurezza dell'app, del server o della rete.</span><span class="sxs-lookup"><span data-stu-id="f9655-137">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="f9655-138">Registrare gli errori con un provider persistente</span><span class="sxs-lookup"><span data-stu-id="f9655-138">Log errors with a persistent provider</span></span>

<span data-ttu-id="f9655-139">Se si verifica un'eccezione non gestita, l'eccezione viene registrata <xref:Microsoft.Extensions.Logging.ILogger> nelle istanze configurate nel contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="f9655-139">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="f9655-140">Per impostazione predefinita, Blazor le app registrano nell'output della console con il provider di registrazione della console.</span><span class="sxs-lookup"><span data-stu-id="f9655-140">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="f9655-141">Prendere in considerazione la registrazione a una posizione più permanente con un provider che gestisce le dimensioni del log e la rotazione del log.</span><span class="sxs-lookup"><span data-stu-id="f9655-141">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="f9655-142">Per altre informazioni, vedere <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="f9655-142">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="f9655-143">Durante lo sviluppo, Blazor in genere invia i dettagli completi delle eccezioni alla console del browser per facilitare il debug.</span><span class="sxs-lookup"><span data-stu-id="f9655-143">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="f9655-144">In produzione, gli errori dettagliati nella console del browser sono disabilitati per impostazione predefinita, il che significa che gli errori non vengono inviati ai client, ma i dettagli completi dell'eccezione sono ancora registrati sul lato server.</span><span class="sxs-lookup"><span data-stu-id="f9655-144">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="f9655-145">Per altre informazioni, vedere <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="f9655-145">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="f9655-146">È necessario decidere quali eventi imprevisti registrare e il livello di gravità degli eventi imprevisti registrati.</span><span class="sxs-lookup"><span data-stu-id="f9655-146">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="f9655-147">Gli utenti ostili potrebbero essere in grado di attivare intenzionalmente gli errori.</span><span class="sxs-lookup"><span data-stu-id="f9655-147">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="f9655-148">Ad esempio, non registrare un evento imprevisto da un errore in cui viene fornito un oggetto sconosciuto `ProductId` nell'URL di un componente che Visualizza i dettagli del prodotto.</span><span class="sxs-lookup"><span data-stu-id="f9655-148">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="f9655-149">Non tutti gli errori devono essere considerati come eventi imprevisti con gravità elevata per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="f9655-149">Not all errors should be treated as high-severity incidents for logging.</span></span>

<span data-ttu-id="f9655-150">Per altre informazioni, vedere <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="f9655-150">For more information, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="f9655-151">Posizioni in cui possono verificarsi errori</span><span class="sxs-lookup"><span data-stu-id="f9655-151">Places where errors may occur</span></span>

<span data-ttu-id="f9655-152">Il Framework e il codice dell'app possono attivare eccezioni non gestite in uno dei percorsi seguenti:</span><span class="sxs-lookup"><span data-stu-id="f9655-152">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="f9655-153">Creazione di istanze di componenti</span><span class="sxs-lookup"><span data-stu-id="f9655-153">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="f9655-154">Metodi del ciclo di vita</span><span class="sxs-lookup"><span data-stu-id="f9655-154">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="f9655-155">Logica di rendering</span><span class="sxs-lookup"><span data-stu-id="f9655-155">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="f9655-156">Gestori eventi</span><span class="sxs-lookup"><span data-stu-id="f9655-156">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="f9655-157">Eliminazione componenti</span><span class="sxs-lookup"><span data-stu-id="f9655-157">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="f9655-158">Interoperabilità JavaScript</span><span class="sxs-lookup"><span data-stu-id="f9655-158">JavaScript interop</span></span>](#javascript-interop)
* <span data-ttu-id="f9655-159">[BlazorRendering server](#blazor-server-prerendering)</span><span class="sxs-lookup"><span data-stu-id="f9655-159">[Blazor Server rerendering](#blazor-server-prerendering)</span></span>

<span data-ttu-id="f9655-160">Le eccezioni non gestite precedenti sono descritte nelle sezioni seguenti di questo articolo.</span><span class="sxs-lookup"><span data-stu-id="f9655-160">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="f9655-161">Creazione di istanze di componenti</span><span class="sxs-lookup"><span data-stu-id="f9655-161">Component instantiation</span></span>

<span data-ttu-id="f9655-162">Quando Blazor Crea un'istanza di un componente:</span><span class="sxs-lookup"><span data-stu-id="f9655-162">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="f9655-163">Il costruttore del componente viene richiamato.</span><span class="sxs-lookup"><span data-stu-id="f9655-163">The component's constructor is invoked.</span></span>
* <span data-ttu-id="f9655-164">Vengono richiamati i costruttori di tutti i servizi non singleton forniti al costruttore del componente tramite la [`@inject`](xref:mvc/views/razor#inject) direttiva o l' [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) attributo.</span><span class="sxs-lookup"><span data-stu-id="f9655-164">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="f9655-165">Un Blazor circuito server ha esito negativo quando un costruttore eseguito o un setter per qualsiasi `[Inject]` proprietà genera un'eccezione non gestita.</span><span class="sxs-lookup"><span data-stu-id="f9655-165">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="f9655-166">L'eccezione è irreversibile perché il Framework non è in grado di creare un'istanza del componente.</span><span class="sxs-lookup"><span data-stu-id="f9655-166">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="f9655-167">Se la logica del costruttore può generare eccezioni, l'app deve intercettare le eccezioni usando un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con gestione e registrazione degli errori.</span><span class="sxs-lookup"><span data-stu-id="f9655-167">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="f9655-168">Metodi del ciclo di vita</span><span class="sxs-lookup"><span data-stu-id="f9655-168">Lifecycle methods</span></span>

<span data-ttu-id="f9655-169">Durante la durata di un componente, Blazor richiama i metodi del [ciclo](xref:blazor/lifecycle)di vita seguenti:</span><span class="sxs-lookup"><span data-stu-id="f9655-169">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/lifecycle):</span></span>

* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>

<span data-ttu-id="f9655-170">Se un metodo del ciclo di vita genera un'eccezione, in modo sincrono o asincrono, l'eccezione è irreversibile per un Blazor circuito server.</span><span class="sxs-lookup"><span data-stu-id="f9655-170">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="f9655-171">Per i componenti che gestiscono gli errori nei metodi del ciclo di vita, aggiungere la logica di gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="f9655-171">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="f9655-172">Nell'esempio seguente viene <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> chiamato un metodo per ottenere un prodotto:</span><span class="sxs-lookup"><span data-stu-id="f9655-172">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="f9655-173">Un'eccezione generata nel `ProductRepository.GetProductByIdAsync` metodo viene gestita da un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) .</span><span class="sxs-lookup"><span data-stu-id="f9655-173">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="f9655-174">Quando il `catch` blocco viene eseguito:</span><span class="sxs-lookup"><span data-stu-id="f9655-174">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="f9655-175">`loadFailed`è impostato su `true` , che viene utilizzato per visualizzare un messaggio di errore all'utente.</span><span class="sxs-lookup"><span data-stu-id="f9655-175">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="f9655-176">L'errore viene registrato.</span><span class="sxs-lookup"><span data-stu-id="f9655-176">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="f9655-177">Logica di rendering</span><span class="sxs-lookup"><span data-stu-id="f9655-177">Rendering logic</span></span>

<span data-ttu-id="f9655-178">Il markup dichiarativo in un `.razor` file componente viene compilato in un metodo C# denominato <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> .</span><span class="sxs-lookup"><span data-stu-id="f9655-178">The declarative markup in a `.razor` component file is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="f9655-179">Quando viene eseguito il rendering di un componente, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> viene eseguita e compilata una struttura di dati che descrive gli elementi, il testo e i componenti figlio del componente di cui è stato eseguito il rendering.</span><span class="sxs-lookup"><span data-stu-id="f9655-179">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="f9655-180">La logica di rendering può generare un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="f9655-180">Rendering logic can throw an exception.</span></span> <span data-ttu-id="f9655-181">Un esempio di questo scenario si verifica quando `@someObject.PropertyName` viene valutato ma `@someObject` è `null` .</span><span class="sxs-lookup"><span data-stu-id="f9655-181">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="f9655-182">Un'eccezione non gestita generata dalla logica di rendering è irreversibile per un Blazor circuito server.</span><span class="sxs-lookup"><span data-stu-id="f9655-182">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="f9655-183">Per evitare un'eccezione di riferimento null nella logica di rendering, verificare la presenza di un `null` oggetto prima di accedere ai relativi membri.</span><span class="sxs-lookup"><span data-stu-id="f9655-183">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="f9655-184">Nell'esempio seguente, `person.Address` non è possibile accedere alle proprietà se `person.Address` è `null` :</span><span class="sxs-lookup"><span data-stu-id="f9655-184">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="f9655-185">Il codice precedente presuppone che `person` non sia `null` .</span><span class="sxs-lookup"><span data-stu-id="f9655-185">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="f9655-186">Spesso, la struttura del codice garantisce la presenza di un oggetto nel momento in cui viene eseguito il rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="f9655-186">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="f9655-187">In questi casi, non è necessario verificare la presenza di `null` nella logica di rendering.</span><span class="sxs-lookup"><span data-stu-id="f9655-187">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="f9655-188">Nell'esempio precedente, `person` potrebbe essere garantito che esista perché `person` viene creato quando viene creata un'istanza del componente.</span><span class="sxs-lookup"><span data-stu-id="f9655-188">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="f9655-189">Gestori eventi</span><span class="sxs-lookup"><span data-stu-id="f9655-189">Event handlers</span></span>

<span data-ttu-id="f9655-190">Il codice lato client attiva le chiamate del codice C# quando i gestori eventi vengono creati usando:</span><span class="sxs-lookup"><span data-stu-id="f9655-190">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="f9655-191">Altri `@on...` attributi</span><span class="sxs-lookup"><span data-stu-id="f9655-191">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="f9655-192">Il codice del gestore eventi potrebbe generare un'eccezione non gestita in questi scenari.</span><span class="sxs-lookup"><span data-stu-id="f9655-192">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="f9655-193">Se un gestore eventi genera un'eccezione non gestita, ad esempio una query di database ha esito negativo, l'eccezione è irreversibile per un Blazor circuito server.</span><span class="sxs-lookup"><span data-stu-id="f9655-193">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="f9655-194">Se l'app chiama il codice che potrebbe non riuscire per motivi esterni, intercettare le eccezioni usando un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con gestione e registrazione degli errori.</span><span class="sxs-lookup"><span data-stu-id="f9655-194">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="f9655-195">Se il codice utente non intercetta e gestisce l'eccezione, il Framework registra l'eccezione e termina il circuito.</span><span class="sxs-lookup"><span data-stu-id="f9655-195">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="f9655-196">Eliminazione componenti</span><span class="sxs-lookup"><span data-stu-id="f9655-196">Component disposal</span></span>

<span data-ttu-id="f9655-197">Un componente può essere rimosso dall'interfaccia utente, ad esempio perché l'utente ha esplorato un'altra pagina.</span><span class="sxs-lookup"><span data-stu-id="f9655-197">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="f9655-198">Quando un componente che implementa <xref:System.IDisposable?displayProperty=fullName> viene rimosso dall'interfaccia utente, il Framework chiama il metodo del componente <xref:System.IDisposable.Dispose%2A> .</span><span class="sxs-lookup"><span data-stu-id="f9655-198">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="f9655-199">Se il metodo del componente `Dispose` genera un'eccezione non gestita, l'eccezione è irreversibile per un Blazor circuito server.</span><span class="sxs-lookup"><span data-stu-id="f9655-199">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="f9655-200">Se la logica di eliminazione può generare eccezioni, l'app deve intercettare le eccezioni usando un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con gestione e registrazione degli errori.</span><span class="sxs-lookup"><span data-stu-id="f9655-200">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="f9655-201">Per ulteriori informazioni sull'eliminazione dei componenti, vedere <xref:blazor/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="f9655-201">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="f9655-202">Interoperabilità JavaScript</span><span class="sxs-lookup"><span data-stu-id="f9655-202">JavaScript interop</span></span>

<span data-ttu-id="f9655-203"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>consente al codice .NET di effettuare chiamate asincrone al runtime JavaScript nel browser dell'utente.</span><span class="sxs-lookup"><span data-stu-id="f9655-203"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="f9655-204">Le condizioni seguenti si applicano alla gestione degli errori con <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> :</span><span class="sxs-lookup"><span data-stu-id="f9655-204">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="f9655-205">Se una chiamata a ha <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> esito negativo in modo sincrono, si verifica un'eccezione .NET.</span><span class="sxs-lookup"><span data-stu-id="f9655-205">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="f9655-206">Una chiamata a <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> potrebbe non riuscire, ad esempio perché gli argomenti forniti non possono essere serializzati.</span><span class="sxs-lookup"><span data-stu-id="f9655-206">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="f9655-207">Il codice dello sviluppatore deve intercettare l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="f9655-207">Developer code must catch the exception.</span></span> <span data-ttu-id="f9655-208">Se il codice dell'app in un gestore eventi o in un metodo del ciclo di vita dei componenti non gestisce un'eccezione, l'eccezione risultante è fatale per un Blazor circuito server.</span><span class="sxs-lookup"><span data-stu-id="f9655-208">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="f9655-209">Se una chiamata a ha <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> esito negativo in modo asincrono, .NET ha <xref:System.Threading.Tasks.Task> esito negativo.</span><span class="sxs-lookup"><span data-stu-id="f9655-209">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="f9655-210">Una chiamata a <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> potrebbe non riuscire, ad esempio perché il codice sul lato JavaScript genera un'eccezione o restituisce un oggetto `Promise` completato come `rejected` .</span><span class="sxs-lookup"><span data-stu-id="f9655-210">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="f9655-211">Il codice dello sviluppatore deve intercettare l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="f9655-211">Developer code must catch the exception.</span></span> <span data-ttu-id="f9655-212">Se si usa l'operatore [await](/dotnet/csharp/language-reference/keywords/await) , è consigliabile eseguire il wrapping della chiamata al metodo in un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con gestione e registrazione degli errori.</span><span class="sxs-lookup"><span data-stu-id="f9655-212">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="f9655-213">In caso contrario, il codice in errore genera un'eccezione non gestita che è irreversibile per un Blazor circuito server.</span><span class="sxs-lookup"><span data-stu-id="f9655-213">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="f9655-214">Per impostazione predefinita, le chiamate a <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> devono essere completate entro un determinato periodo oppure si verifica il timeout della chiamata. Il periodo di timeout predefinito è di un minuto.</span><span class="sxs-lookup"><span data-stu-id="f9655-214">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="f9655-215">Il timeout protegge il codice da una perdita di connettività di rete o codice JavaScript che non restituisce mai un messaggio di completamento.</span><span class="sxs-lookup"><span data-stu-id="f9655-215">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="f9655-216">Se si verifica il timeout della chiamata, l'oggetto risultante ha <xref:System.Threading.Tasks> esito negativo con un oggetto <xref:System.OperationCanceledException> .</span><span class="sxs-lookup"><span data-stu-id="f9655-216">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="f9655-217">Intercettare ed elaborare l'eccezione con la registrazione.</span><span class="sxs-lookup"><span data-stu-id="f9655-217">Trap and process the exception with logging.</span></span>

<span data-ttu-id="f9655-218">Analogamente, il codice JavaScript può avviare chiamate a metodi .NET indicati dall' [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attributo] (xrif: blazer/Call-DotNet-from-JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f9655-218">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute)](xref:blazor/call-dotnet-from-javascript) attribute.</span></span> <span data-ttu-id="f9655-219">Se questi metodi .NET generano un'eccezione non gestita:</span><span class="sxs-lookup"><span data-stu-id="f9655-219">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="f9655-220">L'eccezione non viene trattata come irreversibile per un Blazor circuito server.</span><span class="sxs-lookup"><span data-stu-id="f9655-220">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="f9655-221">Il lato JavaScript `Promise` viene rifiutato.</span><span class="sxs-lookup"><span data-stu-id="f9655-221">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="f9655-222">È possibile scegliere di usare il codice di gestione degli errori sul lato .NET o sul lato JavaScript della chiamata al metodo.</span><span class="sxs-lookup"><span data-stu-id="f9655-222">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="f9655-223">Per altre informazioni, vedere gli articoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="f9655-223">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="blazor-server-prerendering"></a>Blazor<span data-ttu-id="f9655-224">Prerendering server</span><span class="sxs-lookup"><span data-stu-id="f9655-224"> Server prerendering</span></span>

Blazor<span data-ttu-id="f9655-225">è possibile eseguire il prerendering dei componenti usando l' [Helper Tag Component](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) , in modo che il markup HTML sottoposto a rendering venga restituito come parte della richiesta HTTP iniziale dell'utente.</span><span class="sxs-lookup"><span data-stu-id="f9655-225"> components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="f9655-226">Funziona per:</span><span class="sxs-lookup"><span data-stu-id="f9655-226">This works by:</span></span>

* <span data-ttu-id="f9655-227">Creazione di un nuovo circuito per tutti i componenti di cui è stato eseguito il rendering che fanno parte della stessa pagina.</span><span class="sxs-lookup"><span data-stu-id="f9655-227">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="f9655-228">Generazione del codice HTML iniziale.</span><span class="sxs-lookup"><span data-stu-id="f9655-228">Generating the initial HTML.</span></span>
* <span data-ttu-id="f9655-229">Trattare il circuito come `disconnected` fino a quando il browser dell'utente non stabilisce una SignalR connessione allo stesso server.</span><span class="sxs-lookup"><span data-stu-id="f9655-229">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="f9655-230">Quando viene stabilita la connessione, interattività sul circuito viene ripresa e il markup HTML dei componenti viene aggiornato.</span><span class="sxs-lookup"><span data-stu-id="f9655-230">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="f9655-231">Se un componente genera un'eccezione non gestita durante il prerendering, ad esempio durante un metodo del ciclo di vita o nella logica di rendering:</span><span class="sxs-lookup"><span data-stu-id="f9655-231">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="f9655-232">L'eccezione è fatale per il circuito.</span><span class="sxs-lookup"><span data-stu-id="f9655-232">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="f9655-233">L'eccezione viene generata dallo stack di chiamate dall' <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Helper tag.</span><span class="sxs-lookup"><span data-stu-id="f9655-233">The exception is thrown up the call stack from the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span> <span data-ttu-id="f9655-234">Pertanto, l'intera richiesta HTTP ha esito negativo a meno che l'eccezione non venga intercettata in modo esplicito dal codice dello sviluppatore.</span><span class="sxs-lookup"><span data-stu-id="f9655-234">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="f9655-235">In circostanze normali, quando si verifica un errore di prerendering, continuare a compilare ed eseguire il rendering del componente non ha senso perché non è possibile eseguire il rendering di un componente funzionante.</span><span class="sxs-lookup"><span data-stu-id="f9655-235">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="f9655-236">Per tollerare gli errori che possono verificarsi durante il prerendering, la logica di gestione degli errori deve essere inserita all'interno di un componente che può generare eccezioni.</span><span class="sxs-lookup"><span data-stu-id="f9655-236">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="f9655-237">Usare le istruzioni [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con la gestione e la registrazione degli errori.</span><span class="sxs-lookup"><span data-stu-id="f9655-237">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="f9655-238">Anziché eseguire il wrapping dell' <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Helper tag in un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) , inserire la logica di gestione degli errori nel componente di cui viene eseguito il rendering dall' <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Helper tag.</span><span class="sxs-lookup"><span data-stu-id="f9655-238">Instead of wrapping the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement, place error handling logic in the component rendered by the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="f9655-239">Scenari avanzati</span><span class="sxs-lookup"><span data-stu-id="f9655-239">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="f9655-240">Rendering ricorsivo</span><span class="sxs-lookup"><span data-stu-id="f9655-240">Recursive rendering</span></span>

<span data-ttu-id="f9655-241">I componenti possono essere annidati in modo ricorsivo.</span><span class="sxs-lookup"><span data-stu-id="f9655-241">Components can be nested recursively.</span></span> <span data-ttu-id="f9655-242">Questa operazione è utile per la rappresentazione di strutture di dati ricorsive.</span><span class="sxs-lookup"><span data-stu-id="f9655-242">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="f9655-243">Un componente, ad esempio, `TreeNode` può eseguire il rendering di più `TreeNode` componenti per ognuno dei figli del nodo.</span><span class="sxs-lookup"><span data-stu-id="f9655-243">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="f9655-244">Quando si esegue il rendering in modo ricorsivo, evitare i modelli di codifica che generano una ricorsione</span><span class="sxs-lookup"><span data-stu-id="f9655-244">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="f9655-245">Non esegue il rendering in modo ricorsivo di una struttura di dati che contiene un ciclo.</span><span class="sxs-lookup"><span data-stu-id="f9655-245">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="f9655-246">Ad esempio, non eseguire il rendering di un nodo dell'albero i cui elementi figlio includono se stesso.</span><span class="sxs-lookup"><span data-stu-id="f9655-246">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="f9655-247">Non creare una catena di layout che contiene un ciclo.</span><span class="sxs-lookup"><span data-stu-id="f9655-247">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="f9655-248">Non creare, ad esempio, un layout il cui layout è a sua volta.</span><span class="sxs-lookup"><span data-stu-id="f9655-248">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="f9655-249">Non consentire a un utente finale di violare le invarianti di ricorsione (regole) tramite immissione di dati dannosi o chiamate di interoperabilità JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f9655-249">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="f9655-250">Cicli infiniti durante il rendering:</span><span class="sxs-lookup"><span data-stu-id="f9655-250">Infinite loops during rendering:</span></span>

* <span data-ttu-id="f9655-251">Causa la continuazione del processo di rendering.</span><span class="sxs-lookup"><span data-stu-id="f9655-251">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="f9655-252">Equivale alla creazione di un ciclo senza terminazione.</span><span class="sxs-lookup"><span data-stu-id="f9655-252">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="f9655-253">In questi scenari, un Blazor circuito server interessato ha esito negativo e il thread in genere tenta di:</span><span class="sxs-lookup"><span data-stu-id="f9655-253">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="f9655-254">Utilizzare la quantità di tempo della CPU consentita dal sistema operativo, per un periodo illimitato.</span><span class="sxs-lookup"><span data-stu-id="f9655-254">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="f9655-255">Utilizzare una quantità illimitata di memoria del server.</span><span class="sxs-lookup"><span data-stu-id="f9655-255">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="f9655-256">L'utilizzo di memoria illimitata equivale allo scenario in cui un ciclo senza terminazione aggiunge voci a una raccolta in ogni iterazione.</span><span class="sxs-lookup"><span data-stu-id="f9655-256">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="f9655-257">Per evitare modelli di ricorsione infinita, verificare che il codice di rendering ricorsivo contenga condizioni di arresto appropriate.</span><span class="sxs-lookup"><span data-stu-id="f9655-257">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="f9655-258">Logica dell'albero di rendering personalizzata</span><span class="sxs-lookup"><span data-stu-id="f9655-258">Custom render tree logic</span></span>

<span data-ttu-id="f9655-259">La maggior parte dei Blazor componenti viene implementata come file con *estensione Razor* e viene compilata per produrre la logica che opera su un <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> per eseguire il rendering dell'output.</span><span class="sxs-lookup"><span data-stu-id="f9655-259">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="f9655-260">Uno sviluppatore può implementare manualmente la <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logica usando il codice C# procedurale.</span><span class="sxs-lookup"><span data-stu-id="f9655-260">A developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="f9655-261">Per altre informazioni, vedere <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="f9655-261">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="f9655-262">L'uso della logica del generatore di albero di rendering manuale è considerato uno scenario avanzato e non sicuro, non consigliato per lo sviluppo di componenti generali.</span><span class="sxs-lookup"><span data-stu-id="f9655-262">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="f9655-263">Se il <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> codice viene scritto, lo sviluppatore deve garantire la correttezza del codice.</span><span class="sxs-lookup"><span data-stu-id="f9655-263">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="f9655-264">Ad esempio, lo sviluppatore deve garantire quanto segue:</span><span class="sxs-lookup"><span data-stu-id="f9655-264">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="f9655-265">Le chiamate a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> e <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> sono bilanciate correttamente.</span><span class="sxs-lookup"><span data-stu-id="f9655-265">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="f9655-266">Gli attributi vengono aggiunti solo nei punti corretti.</span><span class="sxs-lookup"><span data-stu-id="f9655-266">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="f9655-267">La logica del generatore di albero di rendering manuale non corretta può causare un comportamento arbitrario non definito, tra cui arresti anomali, blocchi del server e vulnerabilità della sicurezza.</span><span class="sxs-lookup"><span data-stu-id="f9655-267">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="f9655-268">Prendere in considerazione la logica del generatore di albero di rendering manuale sullo stesso livello di complessità e con lo stesso livello di *rischio* di scrivere manualmente codice assembly o istruzioni MSIL.</span><span class="sxs-lookup"><span data-stu-id="f9655-268">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
