---
title: Gestione degli errori nelle Blazor app ASP.NET Core
author: guardrex
description: Scopri in che Blazor modo Blazor ASP.NET Core come gestisce le eccezioni non gestite e come sviluppare app che rilevano e gestiscono gli errori.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: 63bb791958785fa9a4a676f1aab79126c6873068
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111045"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a><span data-ttu-id="af15c-103">Gestione degli errori nelle Blazor app ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af15c-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="af15c-104">Di [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="af15c-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="af15c-105">Questo articolo descrive come Blazor gestire le eccezioni non gestite e come sviluppare app che rilevano e gestiscono gli errori.</span><span class="sxs-lookup"><span data-stu-id="af15c-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="af15c-106">Errori dettagliati durante lo sviluppo</span><span class="sxs-lookup"><span data-stu-id="af15c-106">Detailed errors during development</span></span>

<span data-ttu-id="af15c-107">Quando un' Blazor app non funziona correttamente durante lo sviluppo, la ricezione di informazioni dettagliate sugli errori dall'app è utile per la risoluzione dei problemi e per risolvere il problema.</span><span class="sxs-lookup"><span data-stu-id="af15c-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="af15c-108">Quando si verifica un errore Blazor , le app visualizzano una barra dorata nella parte inferiore della schermata:</span><span class="sxs-lookup"><span data-stu-id="af15c-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="af15c-109">Durante lo sviluppo, la barra dorata indirizza l'utente alla console del browser, in cui è possibile visualizzare l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="af15c-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="af15c-110">In produzione, la barra dorata informa l'utente che si è verificato un errore e consiglia di aggiornare il browser.</span><span class="sxs-lookup"><span data-stu-id="af15c-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="af15c-111">L'interfaccia utente per questa esperienza di gestione degli errori fa Blazor parte dei modelli di progetto.</span><span class="sxs-lookup"><span data-stu-id="af15c-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="af15c-112">In un' Blazor app webassembly personalizzare l'esperienza nel file *wwwroot/index.html* :</span><span class="sxs-lookup"><span data-stu-id="af15c-112">In a Blazor WebAssembly app, customize the experience in the *wwwroot/index.html* file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="af15c-113">In un' Blazor app Server, personalizzare l'esperienza nel file *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="af15c-113">In a Blazor Server app, customize the experience in the *Pages/_Host.cshtml* file:</span></span>

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

<span data-ttu-id="af15c-114">L' `blazor-error-ui` elemento è nascosto dagli stili inclusi nei Blazor modelli (*wwwroot/CSS/site. CSS*) e quindi mostrati quando si verifica un errore:</span><span class="sxs-lookup"><span data-stu-id="af15c-114">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (*wwwroot/css/site.css*) and then shown when an error occurs:</span></span>

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

## <a name="how-a-opno-locblazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="af15c-115">Reazione di Blazor un'app Server a eccezioni non gestite</span><span class="sxs-lookup"><span data-stu-id="af15c-115">How a Blazor Server app reacts to unhandled exceptions</span></span>

Blazor<span data-ttu-id="af15c-116">Il server è un Framework con stato.</span><span class="sxs-lookup"><span data-stu-id="af15c-116"> Server is a stateful framework.</span></span> <span data-ttu-id="af15c-117">Mentre gli utenti interagiscono con un'app, mantengono una connessione al server noto come *circuito*.</span><span class="sxs-lookup"><span data-stu-id="af15c-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="af15c-118">Il circuito include istanze di componenti attive, oltre a molti altri aspetti dello stato, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="af15c-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="af15c-119">Output più recente sottoposto a rendering dei componenti.</span><span class="sxs-lookup"><span data-stu-id="af15c-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="af15c-120">Set corrente di delegati di gestione degli eventi che possono essere attivati da eventi lato client.</span><span class="sxs-lookup"><span data-stu-id="af15c-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="af15c-121">Se un utente apre l'app in più schede del browser, avrà più circuiti indipendenti.</span><span class="sxs-lookup"><span data-stu-id="af15c-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

Blazor<span data-ttu-id="af15c-122">Considera le eccezioni non gestite come irreversibili per il circuito in cui si verificano.</span><span class="sxs-lookup"><span data-stu-id="af15c-122"> treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="af15c-123">Se un circuito viene terminato a causa di un'eccezione non gestita, l'utente può continuare a interagire con l'app ricaricando la pagina per creare un nuovo circuito.</span><span class="sxs-lookup"><span data-stu-id="af15c-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="af15c-124">I circuiti al di fuori di quello terminato, ovvero circuiti per altri utenti o altre schede del browser, non sono interessati.</span><span class="sxs-lookup"><span data-stu-id="af15c-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="af15c-125">Questo scenario è simile a un'applicazione desktop che arresta&mdash;l'arresto anomalo dell'app arrestata in modo anomalo, ma altre app non sono interessate.</span><span class="sxs-lookup"><span data-stu-id="af15c-125">This scenario is similar to a desktop app that crashes&mdash;the crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="af15c-126">Un circuito viene terminato quando si verifica un'eccezione non gestita per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="af15c-126">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="af15c-127">Un'eccezione non gestita spesso lascia il circuito in uno stato non definito.</span><span class="sxs-lookup"><span data-stu-id="af15c-127">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="af15c-128">L'operazione normale dell'app non può essere garantita dopo un'eccezione non gestita.</span><span class="sxs-lookup"><span data-stu-id="af15c-128">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="af15c-129">Le vulnerabilità di sicurezza possono essere visualizzate nell'app se il circuito continua.</span><span class="sxs-lookup"><span data-stu-id="af15c-129">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="af15c-130">Gestire le eccezioni non gestite nel codice dello sviluppatore</span><span class="sxs-lookup"><span data-stu-id="af15c-130">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="af15c-131">Per continuare l'applicazione dopo un errore, l'app deve avere la logica di gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="af15c-131">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="af15c-132">Le sezioni successive di questo articolo descrivono le potenziali fonti di eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="af15c-132">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="af15c-133">In produzione, non eseguire il rendering dei messaggi di eccezione del Framework o delle analisi dello stack nell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="af15c-133">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="af15c-134">Il rendering dei messaggi di eccezione o delle analisi dello stack potrebbe:</span><span class="sxs-lookup"><span data-stu-id="af15c-134">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="af15c-135">Divulgare informazioni riservate agli utenti finali.</span><span class="sxs-lookup"><span data-stu-id="af15c-135">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="af15c-136">Aiutare un utente malintenzionato a individuare i punti deboli in un'app che può compromettere la sicurezza dell'app, del server o della rete.</span><span class="sxs-lookup"><span data-stu-id="af15c-136">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="af15c-137">Registrare gli errori con un provider persistente</span><span class="sxs-lookup"><span data-stu-id="af15c-137">Log errors with a persistent provider</span></span>

<span data-ttu-id="af15c-138">Se si verifica un'eccezione non gestita, l'eccezione viene registrata nelle <xref:Microsoft.Extensions.Logging.ILogger> istanze configurate nel contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="af15c-138">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="af15c-139">Per impostazione predefinita Blazor , le app registrano nell'output della console con il provider di registrazione della console.</span><span class="sxs-lookup"><span data-stu-id="af15c-139">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="af15c-140">Prendere in considerazione la registrazione a una posizione più permanente con un provider che gestisce le dimensioni del log e la rotazione del log.</span><span class="sxs-lookup"><span data-stu-id="af15c-140">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="af15c-141">Per altre informazioni, vedere <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="af15c-141">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="af15c-142">Durante lo sviluppo Blazor , in genere invia i dettagli completi delle eccezioni alla console del browser per facilitare il debug.</span><span class="sxs-lookup"><span data-stu-id="af15c-142">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="af15c-143">In produzione, gli errori dettagliati nella console del browser sono disabilitati per impostazione predefinita, il che significa che gli errori non vengono inviati ai client, ma i dettagli completi dell'eccezione sono ancora registrati sul lato server.</span><span class="sxs-lookup"><span data-stu-id="af15c-143">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="af15c-144">Per altre informazioni, vedere <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="af15c-144">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="af15c-145">È necessario decidere quali eventi imprevisti registrare e il livello di gravità degli eventi imprevisti registrati.</span><span class="sxs-lookup"><span data-stu-id="af15c-145">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="af15c-146">Gli utenti ostili potrebbero essere in grado di attivare intenzionalmente gli errori.</span><span class="sxs-lookup"><span data-stu-id="af15c-146">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="af15c-147">Ad esempio, non registrare un evento imprevisto da un errore in `ProductId` cui viene fornito un oggetto sconosciuto nell'URL di un componente che Visualizza i dettagli del prodotto.</span><span class="sxs-lookup"><span data-stu-id="af15c-147">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="af15c-148">Non tutti gli errori devono essere considerati come eventi imprevisti con gravità elevata per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="af15c-148">Not all errors should be treated as high-severity incidents for logging.</span></span>

<span data-ttu-id="af15c-149">Per altre informazioni, vedere <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="af15c-149">For more information, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="af15c-150">Posizioni in cui possono verificarsi errori</span><span class="sxs-lookup"><span data-stu-id="af15c-150">Places where errors may occur</span></span>

<span data-ttu-id="af15c-151">Il Framework e il codice dell'app possono attivare eccezioni non gestite in uno dei percorsi seguenti:</span><span class="sxs-lookup"><span data-stu-id="af15c-151">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="af15c-152">Creazione di istanze di componenti</span><span class="sxs-lookup"><span data-stu-id="af15c-152">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="af15c-153">Metodi del ciclo di vita</span><span class="sxs-lookup"><span data-stu-id="af15c-153">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="af15c-154">Logica di rendering</span><span class="sxs-lookup"><span data-stu-id="af15c-154">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="af15c-155">Gestori eventi</span><span class="sxs-lookup"><span data-stu-id="af15c-155">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="af15c-156">Eliminazione componenti</span><span class="sxs-lookup"><span data-stu-id="af15c-156">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="af15c-157">Interoperabilità JavaScript</span><span class="sxs-lookup"><span data-stu-id="af15c-157">JavaScript interop</span></span>](#javascript-interop)
* <span data-ttu-id="af15c-158">[BlazorRendering server](#blazor-server-prerendering)</span><span class="sxs-lookup"><span data-stu-id="af15c-158">[Blazor Server rerendering](#blazor-server-prerendering)</span></span>

<span data-ttu-id="af15c-159">Le eccezioni non gestite precedenti sono descritte nelle sezioni seguenti di questo articolo.</span><span class="sxs-lookup"><span data-stu-id="af15c-159">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="af15c-160">Creazione di istanze di componenti</span><span class="sxs-lookup"><span data-stu-id="af15c-160">Component instantiation</span></span>

<span data-ttu-id="af15c-161">Quando Blazor crea un'istanza di un componente:</span><span class="sxs-lookup"><span data-stu-id="af15c-161">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="af15c-162">Il costruttore del componente viene richiamato.</span><span class="sxs-lookup"><span data-stu-id="af15c-162">The component's constructor is invoked.</span></span>
* <span data-ttu-id="af15c-163">Vengono richiamati i costruttori di tutti i servizi non singleton forniti al costruttore del componente tramite [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) la direttiva o [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) l'attributo.</span><span class="sxs-lookup"><span data-stu-id="af15c-163">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) directive or the [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="af15c-164">Un Blazor circuito server ha esito negativo quando un costruttore eseguito o un `[Inject]` Setter per qualsiasi proprietà genera un'eccezione non gestita.</span><span class="sxs-lookup"><span data-stu-id="af15c-164">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="af15c-165">L'eccezione è irreversibile perché il Framework non è in grado di creare un'istanza del componente.</span><span class="sxs-lookup"><span data-stu-id="af15c-165">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="af15c-166">Se la logica del costruttore può generare eccezioni, l'app deve intercettare le eccezioni usando un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con gestione e registrazione degli errori.</span><span class="sxs-lookup"><span data-stu-id="af15c-166">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="af15c-167">Metodi del ciclo di vita</span><span class="sxs-lookup"><span data-stu-id="af15c-167">Lifecycle methods</span></span>

<span data-ttu-id="af15c-168">Durante la durata di un componente, Blazor richiama i metodi del [ciclo](xref:blazor/lifecycle)di vita seguenti:</span><span class="sxs-lookup"><span data-stu-id="af15c-168">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/lifecycle):</span></span>

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

<span data-ttu-id="af15c-169">Se un metodo del ciclo di vita genera un'eccezione, in modo sincrono o asincrono, l'eccezione Blazor è irreversibile per un circuito server.</span><span class="sxs-lookup"><span data-stu-id="af15c-169">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="af15c-170">Per i componenti che gestiscono gli errori nei metodi del ciclo di vita, aggiungere la logica di gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="af15c-170">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="af15c-171">Nell'esempio seguente viene `OnParametersSetAsync` chiamato un metodo per ottenere un prodotto:</span><span class="sxs-lookup"><span data-stu-id="af15c-171">In the following example where `OnParametersSetAsync` calls a method to obtain a product:</span></span>

* <span data-ttu-id="af15c-172">Un'eccezione generata nel `ProductRepository.GetProductByIdAsync` metodo viene gestita da un' `try-catch` istruzione.</span><span class="sxs-lookup"><span data-stu-id="af15c-172">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a `try-catch` statement.</span></span>
* <span data-ttu-id="af15c-173">Quando il `catch` blocco viene eseguito:</span><span class="sxs-lookup"><span data-stu-id="af15c-173">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="af15c-174">`loadFailed`è impostato su `true`, che viene utilizzato per visualizzare un messaggio di errore all'utente.</span><span class="sxs-lookup"><span data-stu-id="af15c-174">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="af15c-175">L'errore viene registrato.</span><span class="sxs-lookup"><span data-stu-id="af15c-175">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="af15c-176">Logica di rendering</span><span class="sxs-lookup"><span data-stu-id="af15c-176">Rendering logic</span></span>

<span data-ttu-id="af15c-177">Il markup dichiarativo `.razor` in un file componente viene compilato in un metodo `BuildRenderTree`C# denominato.</span><span class="sxs-lookup"><span data-stu-id="af15c-177">The declarative markup in a `.razor` component file is compiled into a C# method called `BuildRenderTree`.</span></span> <span data-ttu-id="af15c-178">Quando viene eseguito il rendering di `BuildRenderTree` un componente, viene eseguita e compilata una struttura di dati che descrive gli elementi, il testo e i componenti figlio del componente di cui è stato eseguito il rendering.</span><span class="sxs-lookup"><span data-stu-id="af15c-178">When a component renders, `BuildRenderTree` executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="af15c-179">La logica di rendering può generare un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="af15c-179">Rendering logic can throw an exception.</span></span> <span data-ttu-id="af15c-180">Un esempio di questo scenario si verifica `@someObject.PropertyName` quando viene valutato `@someObject` ma `null`è.</span><span class="sxs-lookup"><span data-stu-id="af15c-180">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="af15c-181">Un'eccezione non gestita generata dalla logica di rendering è irreversibile per un Blazor circuito server.</span><span class="sxs-lookup"><span data-stu-id="af15c-181">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="af15c-182">Per evitare un'eccezione di riferimento null nella logica di rendering, verificare `null` la presenza di un oggetto prima di accedere ai relativi membri.</span><span class="sxs-lookup"><span data-stu-id="af15c-182">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="af15c-183">Nell'esempio seguente, `person.Address` non è possibile accedere alle `person.Address` proprietà `null`se è:</span><span class="sxs-lookup"><span data-stu-id="af15c-183">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="af15c-184">Il codice precedente presuppone che `person` non `null`sia.</span><span class="sxs-lookup"><span data-stu-id="af15c-184">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="af15c-185">Spesso, la struttura del codice garantisce la presenza di un oggetto nel momento in cui viene eseguito il rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="af15c-185">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="af15c-186">In questi casi, non è necessario verificare la `null` presenza di nella logica di rendering.</span><span class="sxs-lookup"><span data-stu-id="af15c-186">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="af15c-187">Nell'esempio precedente, `person` potrebbe essere garantito che esista perché `person` viene creato quando viene creata un'istanza del componente.</span><span class="sxs-lookup"><span data-stu-id="af15c-187">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="af15c-188">Gestori eventi</span><span class="sxs-lookup"><span data-stu-id="af15c-188">Event handlers</span></span>

<span data-ttu-id="af15c-189">Il codice lato client attiva le chiamate del codice C# quando i gestori eventi vengono creati usando:</span><span class="sxs-lookup"><span data-stu-id="af15c-189">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="af15c-190">Altri `@on...` attributi</span><span class="sxs-lookup"><span data-stu-id="af15c-190">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="af15c-191">Il codice del gestore eventi potrebbe generare un'eccezione non gestita in questi scenari.</span><span class="sxs-lookup"><span data-stu-id="af15c-191">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="af15c-192">Se un gestore eventi genera un'eccezione non gestita, ad esempio una query di database ha esito negativo, l'eccezione è irreversibile per Blazor un circuito server.</span><span class="sxs-lookup"><span data-stu-id="af15c-192">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="af15c-193">Se l'app chiama il codice che potrebbe non riuscire per motivi esterni, intercettare le eccezioni usando un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con gestione e registrazione degli errori.</span><span class="sxs-lookup"><span data-stu-id="af15c-193">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="af15c-194">Se il codice utente non intercetta e gestisce l'eccezione, il Framework registra l'eccezione e termina il circuito.</span><span class="sxs-lookup"><span data-stu-id="af15c-194">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="af15c-195">Eliminazione componenti</span><span class="sxs-lookup"><span data-stu-id="af15c-195">Component disposal</span></span>

<span data-ttu-id="af15c-196">Un componente può essere rimosso dall'interfaccia utente, ad esempio perché l'utente ha esplorato un'altra pagina.</span><span class="sxs-lookup"><span data-stu-id="af15c-196">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="af15c-197">Quando un componente che implementa <xref:System.IDisposable?displayProperty=fullName> viene rimosso dall'interfaccia utente, il Framework chiama il <xref:System.IDisposable.Dispose*> metodo del componente.</span><span class="sxs-lookup"><span data-stu-id="af15c-197">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose*> method.</span></span>

<span data-ttu-id="af15c-198">Se il `Dispose` metodo del componente genera un'eccezione non gestita, l'eccezione è irreversibile per un Blazor circuito server.</span><span class="sxs-lookup"><span data-stu-id="af15c-198">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="af15c-199">Se la logica di eliminazione può generare eccezioni, l'app deve intercettare le eccezioni usando un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con gestione e registrazione degli errori.</span><span class="sxs-lookup"><span data-stu-id="af15c-199">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="af15c-200">Per ulteriori informazioni sull'eliminazione dei componenti, <xref:blazor/lifecycle#component-disposal-with-idisposable>vedere.</span><span class="sxs-lookup"><span data-stu-id="af15c-200">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="af15c-201">Interoperabilità JavaScript</span><span class="sxs-lookup"><span data-stu-id="af15c-201">JavaScript interop</span></span>

<span data-ttu-id="af15c-202">`IJSRuntime.InvokeAsync<T>`consente al codice .NET di effettuare chiamate asincrone al runtime JavaScript nel browser dell'utente.</span><span class="sxs-lookup"><span data-stu-id="af15c-202">`IJSRuntime.InvokeAsync<T>` allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="af15c-203">Le condizioni seguenti si applicano alla gestione `InvokeAsync<T>`degli errori con:</span><span class="sxs-lookup"><span data-stu-id="af15c-203">The following conditions apply to error handling with `InvokeAsync<T>`:</span></span>

* <span data-ttu-id="af15c-204">Se una chiamata a `InvokeAsync<T>` ha esito negativo in modo sincrono, si verifica un'eccezione .NET.</span><span class="sxs-lookup"><span data-stu-id="af15c-204">If a call to `InvokeAsync<T>` fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="af15c-205">Una chiamata a `InvokeAsync<T>` potrebbe non riuscire, ad esempio perché gli argomenti forniti non possono essere serializzati.</span><span class="sxs-lookup"><span data-stu-id="af15c-205">A call to `InvokeAsync<T>` may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="af15c-206">Il codice dello sviluppatore deve intercettare l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="af15c-206">Developer code must catch the exception.</span></span> <span data-ttu-id="af15c-207">Se il codice dell'app in un gestore eventi o in un metodo del ciclo di vita dei componenti non gestisce un'eccezione Blazor , l'eccezione risultante è fatale per un circuito server.</span><span class="sxs-lookup"><span data-stu-id="af15c-207">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="af15c-208">Se una chiamata a `InvokeAsync<T>` ha esito negativo in modo <xref:System.Threading.Tasks.Task> asincrono, .NET ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="af15c-208">If a call to `InvokeAsync<T>` fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="af15c-209">Una chiamata a `InvokeAsync<T>` potrebbe non riuscire, ad esempio perché il codice sul lato JavaScript genera un'eccezione o restituisce `Promise` un oggetto completato `rejected`come.</span><span class="sxs-lookup"><span data-stu-id="af15c-209">A call to `InvokeAsync<T>` may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="af15c-210">Il codice dello sviluppatore deve intercettare l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="af15c-210">Developer code must catch the exception.</span></span> <span data-ttu-id="af15c-211">Se si usa l'operatore [await](/dotnet/csharp/language-reference/keywords/await) , è consigliabile eseguire il wrapping della chiamata al metodo in un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con gestione e registrazione degli errori.</span><span class="sxs-lookup"><span data-stu-id="af15c-211">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="af15c-212">In caso contrario, il codice in errore genera un'eccezione non gestita che è irreversibile per Blazor un circuito server.</span><span class="sxs-lookup"><span data-stu-id="af15c-212">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="af15c-213">Per impostazione predefinita, le `InvokeAsync<T>` chiamate a devono essere completate entro un determinato periodo oppure si verifica il timeout della chiamata. Il periodo di timeout predefinito è di un minuto.</span><span class="sxs-lookup"><span data-stu-id="af15c-213">By default, calls to `InvokeAsync<T>` must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="af15c-214">Il timeout protegge il codice da una perdita di connettività di rete o codice JavaScript che non restituisce mai un messaggio di completamento.</span><span class="sxs-lookup"><span data-stu-id="af15c-214">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="af15c-215">Se si verifica il timeout della chiamata, `Task` l'oggetto risultante <xref:System.OperationCanceledException>ha esito negativo con un oggetto.</span><span class="sxs-lookup"><span data-stu-id="af15c-215">If the call times out, the resulting `Task` fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="af15c-216">Intercettare ed elaborare l'eccezione con la registrazione.</span><span class="sxs-lookup"><span data-stu-id="af15c-216">Trap and process the exception with logging.</span></span>

<span data-ttu-id="af15c-217">Analogamente, il [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) codice JavaScript può avviare chiamate a metodi .NET indicati dall'attributo.</span><span class="sxs-lookup"><span data-stu-id="af15c-217">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) attribute.</span></span> <span data-ttu-id="af15c-218">Se questi metodi .NET generano un'eccezione non gestita:</span><span class="sxs-lookup"><span data-stu-id="af15c-218">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="af15c-219">L'eccezione non viene trattata come irreversibile Blazor per un circuito server.</span><span class="sxs-lookup"><span data-stu-id="af15c-219">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="af15c-220">Il lato `Promise` JavaScript viene rifiutato.</span><span class="sxs-lookup"><span data-stu-id="af15c-220">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="af15c-221">È possibile scegliere di usare il codice di gestione degli errori sul lato .NET o sul lato JavaScript della chiamata al metodo.</span><span class="sxs-lookup"><span data-stu-id="af15c-221">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="af15c-222">Per altre informazioni, vedere gli articoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="af15c-222">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="opno-locblazor-server-prerendering"></a>Blazor<span data-ttu-id="af15c-223">Prerendering server</span><span class="sxs-lookup"><span data-stu-id="af15c-223"> Server prerendering</span></span>

Blazor<span data-ttu-id="af15c-224">è possibile eseguire il prerendering dei componenti usando l' [Helper Tag Component](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) , in modo che il markup HTML sottoposto a rendering venga restituito come parte della richiesta HTTP iniziale dell'utente.</span><span class="sxs-lookup"><span data-stu-id="af15c-224"> components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="af15c-225">Funziona per:</span><span class="sxs-lookup"><span data-stu-id="af15c-225">This works by:</span></span>

* <span data-ttu-id="af15c-226">Creazione di un nuovo circuito per tutti i componenti di cui è stato eseguito il rendering che fanno parte della stessa pagina.</span><span class="sxs-lookup"><span data-stu-id="af15c-226">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="af15c-227">Generazione del codice HTML iniziale.</span><span class="sxs-lookup"><span data-stu-id="af15c-227">Generating the initial HTML.</span></span>
* <span data-ttu-id="af15c-228">Trattare il circuito come `disconnected` fino a quando il browser dell'utente SignalR non stabilisce una connessione allo stesso server.</span><span class="sxs-lookup"><span data-stu-id="af15c-228">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="af15c-229">Quando viene stabilita la connessione, interattività sul circuito viene ripresa e il markup HTML dei componenti viene aggiornato.</span><span class="sxs-lookup"><span data-stu-id="af15c-229">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="af15c-230">Se un componente genera un'eccezione non gestita durante il prerendering, ad esempio durante un metodo del ciclo di vita o nella logica di rendering:</span><span class="sxs-lookup"><span data-stu-id="af15c-230">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="af15c-231">L'eccezione è fatale per il circuito.</span><span class="sxs-lookup"><span data-stu-id="af15c-231">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="af15c-232">L'eccezione viene generata dallo stack di chiamate dall'helper `Component` tag.</span><span class="sxs-lookup"><span data-stu-id="af15c-232">The exception is thrown up the call stack from the `Component` Tag Helper.</span></span> <span data-ttu-id="af15c-233">Pertanto, l'intera richiesta HTTP ha esito negativo a meno che l'eccezione non venga intercettata in modo esplicito dal codice dello sviluppatore.</span><span class="sxs-lookup"><span data-stu-id="af15c-233">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="af15c-234">In circostanze normali, quando si verifica un errore di prerendering, continuare a compilare ed eseguire il rendering del componente non ha senso perché non è possibile eseguire il rendering di un componente funzionante.</span><span class="sxs-lookup"><span data-stu-id="af15c-234">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="af15c-235">Per tollerare gli errori che possono verificarsi durante il prerendering, la logica di gestione degli errori deve essere inserita all'interno di un componente che può generare eccezioni.</span><span class="sxs-lookup"><span data-stu-id="af15c-235">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="af15c-236">Usare le istruzioni [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con la gestione e la registrazione degli errori.</span><span class="sxs-lookup"><span data-stu-id="af15c-236">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="af15c-237">Anziché eseguire il wrapping `Component` dell'helper tag in un' `try-catch` istruzione, inserire la logica di gestione degli errori nel componente di `Component` cui viene eseguito il rendering dall'helper tag.</span><span class="sxs-lookup"><span data-stu-id="af15c-237">Instead of wrapping the `Component` Tag Helper in a `try-catch` statement, place error handling logic in the component rendered by the `Component` Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="af15c-238">Scenari avanzati</span><span class="sxs-lookup"><span data-stu-id="af15c-238">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="af15c-239">Rendering ricorsivo</span><span class="sxs-lookup"><span data-stu-id="af15c-239">Recursive rendering</span></span>

<span data-ttu-id="af15c-240">I componenti possono essere annidati in modo ricorsivo.</span><span class="sxs-lookup"><span data-stu-id="af15c-240">Components can be nested recursively.</span></span> <span data-ttu-id="af15c-241">Questa operazione è utile per la rappresentazione di strutture di dati ricorsive.</span><span class="sxs-lookup"><span data-stu-id="af15c-241">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="af15c-242">Un `TreeNode` componente, ad esempio, può eseguire `TreeNode` il rendering di più componenti per ognuno dei figli del nodo.</span><span class="sxs-lookup"><span data-stu-id="af15c-242">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="af15c-243">Quando si esegue il rendering in modo ricorsivo, evitare i modelli di codifica che generano una ricorsione</span><span class="sxs-lookup"><span data-stu-id="af15c-243">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="af15c-244">Non esegue il rendering in modo ricorsivo di una struttura di dati che contiene un ciclo.</span><span class="sxs-lookup"><span data-stu-id="af15c-244">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="af15c-245">Ad esempio, non eseguire il rendering di un nodo dell'albero i cui elementi figlio includono se stesso.</span><span class="sxs-lookup"><span data-stu-id="af15c-245">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="af15c-246">Non creare una catena di layout che contiene un ciclo.</span><span class="sxs-lookup"><span data-stu-id="af15c-246">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="af15c-247">Non creare, ad esempio, un layout il cui layout è a sua volta.</span><span class="sxs-lookup"><span data-stu-id="af15c-247">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="af15c-248">Non consentire a un utente finale di violare le invarianti di ricorsione (regole) tramite immissione di dati dannosi o chiamate di interoperabilità JavaScript.</span><span class="sxs-lookup"><span data-stu-id="af15c-248">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="af15c-249">Cicli infiniti durante il rendering:</span><span class="sxs-lookup"><span data-stu-id="af15c-249">Infinite loops during rendering:</span></span>

* <span data-ttu-id="af15c-250">Causa la continuazione del processo di rendering.</span><span class="sxs-lookup"><span data-stu-id="af15c-250">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="af15c-251">Equivale alla creazione di un ciclo senza terminazione.</span><span class="sxs-lookup"><span data-stu-id="af15c-251">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="af15c-252">In questi scenari, un circuito Blazor server interessato ha esito negativo e il thread in genere tenta di:</span><span class="sxs-lookup"><span data-stu-id="af15c-252">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="af15c-253">Utilizzare la quantità di tempo della CPU consentita dal sistema operativo, per un periodo illimitato.</span><span class="sxs-lookup"><span data-stu-id="af15c-253">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="af15c-254">Utilizzare una quantità illimitata di memoria del server.</span><span class="sxs-lookup"><span data-stu-id="af15c-254">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="af15c-255">L'utilizzo di memoria illimitata equivale allo scenario in cui un ciclo senza terminazione aggiunge voci a una raccolta in ogni iterazione.</span><span class="sxs-lookup"><span data-stu-id="af15c-255">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="af15c-256">Per evitare modelli di ricorsione infinita, verificare che il codice di rendering ricorsivo contenga condizioni di arresto appropriate.</span><span class="sxs-lookup"><span data-stu-id="af15c-256">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="af15c-257">Logica dell'albero di rendering personalizzata</span><span class="sxs-lookup"><span data-stu-id="af15c-257">Custom render tree logic</span></span>

<span data-ttu-id="af15c-258">La Blazor maggior parte dei componenti viene implementata come file con *estensione Razor* e viene compilata `RenderTreeBuilder` per produrre la logica che opera su un per eseguire il rendering dell'output.</span><span class="sxs-lookup"><span data-stu-id="af15c-258">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a `RenderTreeBuilder` to render their output.</span></span> <span data-ttu-id="af15c-259">Uno sviluppatore può implementare `RenderTreeBuilder` manualmente la logica usando il codice C# procedurale.</span><span class="sxs-lookup"><span data-stu-id="af15c-259">A developer may manually implement `RenderTreeBuilder` logic using procedural C# code.</span></span> <span data-ttu-id="af15c-260">Per altre informazioni, vedere <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="af15c-260">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="af15c-261">L'uso della logica del generatore di albero di rendering manuale è considerato uno scenario avanzato e non sicuro, non consigliato per lo sviluppo di componenti generali.</span><span class="sxs-lookup"><span data-stu-id="af15c-261">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="af15c-262">Se `RenderTreeBuilder` il codice viene scritto, lo sviluppatore deve garantire la correttezza del codice.</span><span class="sxs-lookup"><span data-stu-id="af15c-262">If `RenderTreeBuilder` code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="af15c-263">Ad esempio, lo sviluppatore deve garantire quanto segue:</span><span class="sxs-lookup"><span data-stu-id="af15c-263">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="af15c-264">Le chiamate `OpenElement` a `CloseElement` e sono bilanciate correttamente.</span><span class="sxs-lookup"><span data-stu-id="af15c-264">Calls to `OpenElement` and `CloseElement` are correctly balanced.</span></span>
* <span data-ttu-id="af15c-265">Gli attributi vengono aggiunti solo nei punti corretti.</span><span class="sxs-lookup"><span data-stu-id="af15c-265">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="af15c-266">La logica del generatore di albero di rendering manuale non corretta può causare un comportamento arbitrario non definito, tra cui arresti anomali, blocchi del server e vulnerabilità della sicurezza.</span><span class="sxs-lookup"><span data-stu-id="af15c-266">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="af15c-267">Prendere in considerazione la logica del generatore di albero di rendering manuale sullo stesso livello di complessità e con lo stesso livello di *rischio* di scrivere manualmente codice assembly o istruzioni MSIL.</span><span class="sxs-lookup"><span data-stu-id="af15c-267">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
