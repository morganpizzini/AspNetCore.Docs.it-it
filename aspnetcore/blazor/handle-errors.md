---
title: Gestire gli errori Blazor nelle app di ASP.NET Core
author: guardrex
description: Scopri come Blazor ASP.NET Blazor Core come gestisce le eccezioni non gestite e come sviluppare app che rilevano e gestiscono gli errori.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/29/2020
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: 4fdaf7fb90d126b8f7f029aac3af49eec3b69e74
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80382275"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a><span data-ttu-id="5bf6e-103">Gestire gli errori Blazor nelle app di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5bf6e-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="5bf6e-104">Di [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="5bf6e-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="5bf6e-105">Questo articolo descrive Blazor come gestisce le eccezioni non gestite e come sviluppare app che rilevano e gestiscono gli errori.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="5bf6e-106">Errori dettagliati durante lo sviluppo</span><span class="sxs-lookup"><span data-stu-id="5bf6e-106">Detailed errors during development</span></span>

<span data-ttu-id="5bf6e-107">Quando Blazor un'app non funziona correttamente durante lo sviluppo, la ricezione di informazioni dettagliate sull'errore dall'app consente di risolvere il problema.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="5bf6e-108">Quando si verifica Blazor un errore, le app visualizzano una barra d'oro nella parte inferiore dello schermo:</span><span class="sxs-lookup"><span data-stu-id="5bf6e-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="5bf6e-109">Durante lo sviluppo, la barra d'oro ti indirizza alla console del browser, dove puoi vedere l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="5bf6e-110">In produzione, la barra d'oro notifica all'utente che si è verificato un errore e consiglia di aggiornare il browser.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="5bf6e-111">L'interfaccia utente per questa esperienza Blazor di gestione degli errori fa parte dei modelli di progetto.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="5bf6e-112">In Blazor un'app WebAssembly personalizzare l'esperienza nel file *di wwwroot/index.html:*</span><span class="sxs-lookup"><span data-stu-id="5bf6e-112">In a Blazor WebAssembly app, customize the experience in the *wwwroot/index.html* file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="5bf6e-113">In Blazor un'app Server, personalizza l'esperienza nel file *Pages/_Host.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="5bf6e-113">In a Blazor Server app, customize the experience in the *Pages/_Host.cshtml* file:</span></span>

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

<span data-ttu-id="5bf6e-114">L'elemento `blazor-error-ui` è nascosto dagli Blazor stili inclusi nei modelli (*wwwroot/css/site.css*) e quindi visualizzato quando si verifica un errore:</span><span class="sxs-lookup"><span data-stu-id="5bf6e-114">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (*wwwroot/css/site.css*) and then shown when an error occurs:</span></span>

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

## <a name="how-a-opno-locblazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="5bf6e-115">Reazione Blazor di un'app server alle eccezioni non gestiteHow a Server app reacts to unhandled exceptions</span><span class="sxs-lookup"><span data-stu-id="5bf6e-115">How a Blazor Server app reacts to unhandled exceptions</span></span>

Blazor<span data-ttu-id="5bf6e-116">Server è un framework con stato.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-116"> Server is a stateful framework.</span></span> <span data-ttu-id="5bf6e-117">Mentre gli utenti interagiscono con un'app, mantengono una connessione al server noto come *circuito*.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="5bf6e-118">Il circuito contiene istanze del componente attivo, oltre a molti altri aspetti dello stato, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5bf6e-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="5bf6e-119">L'output di rendering più recente dei componenti.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="5bf6e-120">Set corrente di delegati di gestione degli eventi che potrebbero essere attivati dagli eventi sul lato client.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="5bf6e-121">Se un utente apre l'app in più schede del browser, dispone di più circuiti indipendenti.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

Blazor<span data-ttu-id="5bf6e-122">considera la maggior parte delle eccezioni non gestite come irreversibili per il circuito in cui si verificano.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-122"> treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="5bf6e-123">Se un circuito viene terminato a causa di un'eccezione non gestita, l'utente può continuare a interagire con l'app solo ricaricando la pagina per creare un nuovo circuito.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="5bf6e-124">I circuiti al di fuori di quello che è terminato, che sono circuiti per altri utenti o altre schede del browser, non sono interessati.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="5bf6e-125">Questo scenario è simile a&mdash;un'app desktop che si arresta in modo anomalo l'app arrestata in modo anomalo deve essere riavviata, ma altre app non sono interessate.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-125">This scenario is similar to a desktop app that crashes&mdash;the crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="5bf6e-126">Un circuito viene terminato quando si verifica un'eccezione non gestita per i motivi seguenti:A circuit is terminateed when an unhandled exception occurs for the following reasons:</span><span class="sxs-lookup"><span data-stu-id="5bf6e-126">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="5bf6e-127">Un'eccezione non gestita spesso lascia il circuito in uno stato non definito.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-127">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="5bf6e-128">Il normale funzionamento dell'app non può essere garantito dopo un'eccezione non gestita.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-128">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="5bf6e-129">Le vulnerabilità di sicurezza possono essere visualizzate nell'app se il circuito continua.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-129">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="5bf6e-130">Gestire le eccezioni non gestite nel codice per sviluppatoriManage unhandled exceptions in developer code</span><span class="sxs-lookup"><span data-stu-id="5bf6e-130">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="5bf6e-131">Affinché un'app continui dopo un errore, l'app deve disporre della logica di gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-131">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="5bf6e-132">Nelle sezioni successive di questo articolo vengono descritte le potenziali origini di eccezioni non gestite.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-132">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="5bf6e-133">Nell'ambiente di produzione, non eseguire il rendering dei messaggi di eccezione del framework o delle tracce dello stack nell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-133">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="5bf6e-134">Il rendering dei messaggi di eccezione o delle tracce dello stack potrebbe:Rendering exception messages or stack traces could:</span><span class="sxs-lookup"><span data-stu-id="5bf6e-134">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="5bf6e-135">Divulgare informazioni riservate agli utenti finali.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-135">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="5bf6e-136">Aiuta un utente malintenzionato a individuare i punti deboli in un'app che possono compromettere la sicurezza dell'app, del server o della rete.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-136">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="5bf6e-137">Registrare gli errori con un provider persistenteLog errors with a persistent provider</span><span class="sxs-lookup"><span data-stu-id="5bf6e-137">Log errors with a persistent provider</span></span>

<span data-ttu-id="5bf6e-138">Se si verifica un'eccezione non <xref:Microsoft.Extensions.Logging.ILogger> gestita, l'eccezione viene registrata nelle istanze configurate nel contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-138">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="5bf6e-139">Per impostazione predefinita, Blazor le app registrano l'output della console con il provider di registrazione della console.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-139">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="5bf6e-140">Prendere in considerazione la registrazione in una posizione più permanente con un provider che gestisce le dimensioni del log e la rotazione del log.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-140">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="5bf6e-141">Per altre informazioni, vedere <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-141">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="5bf6e-142">Durante lo Blazor sviluppo, in genere invia i dettagli completi delle eccezioni alla console del browser per facilitare il debug.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-142">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="5bf6e-143">Nell'ambiente di produzione, gli errori dettagliati nella console del browser sono disabilitati per impostazione predefinita, il che significa che gli errori non vengono inviati ai client, ma i dettagli completi dell'eccezione sono ancora registrati sul lato server.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-143">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="5bf6e-144">Per altre informazioni, vedere <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-144">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="5bf6e-145">È necessario decidere quali eventi imprevisti registrare e il livello di gravità degli eventi imprevisti registrati.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-145">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="5bf6e-146">Gli utenti ostili potrebbero essere in grado di attivare gli errori deliberatamente.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-146">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="5bf6e-147">Ad esempio, non registrare un evento imprevisto da un errore in cui viene fornito un sconosciuto `ProductId` nell'URL di un componente che visualizza i dettagli del prodotto.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-147">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="5bf6e-148">Non tutti gli errori devono essere considerati come incidenti con gravità elevata per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-148">Not all errors should be treated as high-severity incidents for logging.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="5bf6e-149">Luoghi in cui possono verificarsi errori</span><span class="sxs-lookup"><span data-stu-id="5bf6e-149">Places where errors may occur</span></span>

<span data-ttu-id="5bf6e-150">Il codice del framework e dell'app può generare eccezioni non gestite in una delle posizioni seguenti:Framework and app code may trigger unhandled exceptions in any of the following locations:</span><span class="sxs-lookup"><span data-stu-id="5bf6e-150">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="5bf6e-151">Creazione di istanze dei componenti</span><span class="sxs-lookup"><span data-stu-id="5bf6e-151">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="5bf6e-152">Metodi del ciclo di vita</span><span class="sxs-lookup"><span data-stu-id="5bf6e-152">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="5bf6e-153">Logica di rendering</span><span class="sxs-lookup"><span data-stu-id="5bf6e-153">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="5bf6e-154">Gestori eventi</span><span class="sxs-lookup"><span data-stu-id="5bf6e-154">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="5bf6e-155">Smaltimento dei componenti</span><span class="sxs-lookup"><span data-stu-id="5bf6e-155">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="5bf6e-156">Interoperabilità JavaScript</span><span class="sxs-lookup"><span data-stu-id="5bf6e-156">JavaScript interop</span></span>](#javascript-interop)
* <span data-ttu-id="5bf6e-157">[BlazorRerendering del server](#blazor-server-prerendering)</span><span class="sxs-lookup"><span data-stu-id="5bf6e-157">[Blazor Server rerendering](#blazor-server-prerendering)</span></span>

<span data-ttu-id="5bf6e-158">Le eccezioni non gestite precedenti sono descritte nelle sezioni seguenti di questo articolo.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-158">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="5bf6e-159">Creazione di istanze dei componenti</span><span class="sxs-lookup"><span data-stu-id="5bf6e-159">Component instantiation</span></span>

<span data-ttu-id="5bf6e-160">Quando Blazor si crea un'istanza di un componente:</span><span class="sxs-lookup"><span data-stu-id="5bf6e-160">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="5bf6e-161">Viene richiamato il costruttore del componente.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-161">The component's constructor is invoked.</span></span>
* <span data-ttu-id="5bf6e-162">Vengono richiamati i costruttori di qualsiasi servizio DI non singleton fornito al costruttore del componente tramite la [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) direttiva o l'attributo [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) .</span><span class="sxs-lookup"><span data-stu-id="5bf6e-162">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) directive or the [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="5bf6e-163">Un Blazor circuito Server ha esito negativo quando `[Inject]` qualsiasi costruttore eseguito o un setter per qualsiasi proprietà genera un'eccezione non gestita.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-163">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="5bf6e-164">L'eccezione è irreversibile perché il framework non può creare un'istanza del componente.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-164">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="5bf6e-165">Se la logica del costruttore può generare eccezioni, l'app deve intercettare le eccezioni usando un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con gestione e registrazione degli errori.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-165">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="5bf6e-166">Metodi del ciclo di vita</span><span class="sxs-lookup"><span data-stu-id="5bf6e-166">Lifecycle methods</span></span>

<span data-ttu-id="5bf6e-167">Durante la durata di Blazor un componente, richiama i seguenti [metodi del ciclo di vita:](xref:blazor/lifecycle)</span><span class="sxs-lookup"><span data-stu-id="5bf6e-167">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/lifecycle):</span></span>

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

<span data-ttu-id="5bf6e-168">Se un metodo del ciclo di vita genera un'eccezione, Blazor in modo sincrono o asincrono, l'eccezione viene irreversibile per un circuito Server.If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Server circuit.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-168">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="5bf6e-169">Per i componenti per gestire gli errori nei metodi del ciclo di vita, aggiungere la logica di gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-169">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="5bf6e-170">Nell'esempio seguente `OnParametersSetAsync` in cui chiama un metodo per ottenere un prodotto:In the following example where calls a method to obtain a product:</span><span class="sxs-lookup"><span data-stu-id="5bf6e-170">In the following example where `OnParametersSetAsync` calls a method to obtain a product:</span></span>

* <span data-ttu-id="5bf6e-171">Un'eccezione `ProductRepository.GetProductByIdAsync` generata nel metodo `try-catch` viene gestita da un'istruzione.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-171">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a `try-catch` statement.</span></span>
* <span data-ttu-id="5bf6e-172">Quando `catch` viene eseguito il blocco:</span><span class="sxs-lookup"><span data-stu-id="5bf6e-172">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="5bf6e-173">`loadFailed`è impostato `true`su , che viene utilizzato per visualizzare un messaggio di errore all'utente.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-173">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="5bf6e-174">L'errore viene registrato.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-174">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="5bf6e-175">Logica di rendering</span><span class="sxs-lookup"><span data-stu-id="5bf6e-175">Rendering logic</span></span>

<span data-ttu-id="5bf6e-176">Il markup dichiarativo in un `.razor` file di `BuildRenderTree`componente viene compilato in un metodo di C , denominato .</span><span class="sxs-lookup"><span data-stu-id="5bf6e-176">The declarative markup in a `.razor` component file is compiled into a C# method called `BuildRenderTree`.</span></span> <span data-ttu-id="5bf6e-177">Quando un componente `BuildRenderTree` esegue il rendering, l'esecuzione e la creazione di una struttura di dati che descrive gli elementi, il testo e i componenti figlio del componente sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-177">When a component renders, `BuildRenderTree` executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="5bf6e-178">La logica di rendering può generare un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-178">Rendering logic can throw an exception.</span></span> <span data-ttu-id="5bf6e-179">Un esempio di questo `@someObject.PropertyName` scenario `@someObject` si `null`verifica quando viene valutato ma è .</span><span class="sxs-lookup"><span data-stu-id="5bf6e-179">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="5bf6e-180">Un'eccezione non gestita generata Blazor dalla logica di rendering è irreversibile per un circuito Server.An unhandled exception thrown by rendering logic is fatal to a Server circuit.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-180">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="5bf6e-181">Per evitare un'eccezione di riferimento `null` null nella logica di rendering, verificare la presenza di un oggetto prima di accedere ai relativi membri.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-181">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="5bf6e-182">Nell'esempio seguente, `person.Address` l'accesso `person.Address` alle `null`proprietà non è accessibile se è :</span><span class="sxs-lookup"><span data-stu-id="5bf6e-182">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="5bf6e-183">Il codice precedente presuppone `person` che `null`non sia .</span><span class="sxs-lookup"><span data-stu-id="5bf6e-183">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="5bf6e-184">Spesso, la struttura del codice garantisce l'esito di un oggetto nel momento in cui viene eseguito il rendering del componente.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-184">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="5bf6e-185">In questi casi, non è necessario `null` controllare la logica di rendering.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-185">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="5bf6e-186">Nell'esempio precedente, `person` potrebbe essere garantito che esista perché `person` viene creato quando viene creata un'istanza del componente.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-186">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="5bf6e-187">Gestori eventi</span><span class="sxs-lookup"><span data-stu-id="5bf6e-187">Event handlers</span></span>

<span data-ttu-id="5bf6e-188">Il codice lato client attiva le chiamate di codice C , quando i gestori eventi vengono creati usando:Client-side code triggers invocations of C ' code when event handlers are created using:</span><span class="sxs-lookup"><span data-stu-id="5bf6e-188">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="5bf6e-189">Altri `@on...` attributi</span><span class="sxs-lookup"><span data-stu-id="5bf6e-189">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="5bf6e-190">Il codice del gestore eventi potrebbe generare un'eccezione non gestita in questi scenari.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-190">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="5bf6e-191">Se un gestore eventi genera un'eccezione non gestita (ad esempio, Blazor una query di database ha esito negativo), l'eccezione viene irreversibile per un circuito Server.If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Server circuit.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-191">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="5bf6e-192">Se l'app chiama codice che potrebbe non riuscire per motivi esterni, intercettare le eccezioni usando un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con la gestione e la registrazione degli errori.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-192">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="5bf6e-193">Se il codice utente non intercetta e gestisce l'eccezione, il framework registra l'eccezione e termina il circuito.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-193">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="5bf6e-194">Smaltimento dei componenti</span><span class="sxs-lookup"><span data-stu-id="5bf6e-194">Component disposal</span></span>

<span data-ttu-id="5bf6e-195">Un componente può essere rimosso dall'interfaccia utente, ad esempio, perché l'utente è passato a un'altra pagina.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-195">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="5bf6e-196">Quando un componente <xref:System.IDisposable?displayProperty=fullName> che implementa viene rimosso dall'interfaccia <xref:System.IDisposable.Dispose*> utente, il framework chiama il metodo del componente.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-196">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose*> method.</span></span>

<span data-ttu-id="5bf6e-197">Se il metodo `Dispose` del componente genera un'eccezione non gestita, l'eccezione viene irreversibile per un Blazor circuito Server.If the component's method throws an unhandled exception, the exception is fatal to a Server circuit.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-197">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="5bf6e-198">Se la logica di eliminazione può generare eccezioni, l'app deve intercettare le eccezioni usando un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con gestione e registrazione degli errori.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-198">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="5bf6e-199">Per ulteriori informazioni sullo <xref:blazor/lifecycle#component-disposal-with-idisposable>smaltimento dei componenti, vedere .</span><span class="sxs-lookup"><span data-stu-id="5bf6e-199">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="5bf6e-200">Interoperabilità JavaScript</span><span class="sxs-lookup"><span data-stu-id="5bf6e-200">JavaScript interop</span></span>

<span data-ttu-id="5bf6e-201">`IJSRuntime.InvokeAsync<T>`consente al codice .NET di effettuare chiamate asincrone al runtime JavaScript nel browser dell'utente.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-201">`IJSRuntime.InvokeAsync<T>` allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="5bf6e-202">Le seguenti condizioni si `InvokeAsync<T>`applicano alla gestione degli errori con:</span><span class="sxs-lookup"><span data-stu-id="5bf6e-202">The following conditions apply to error handling with `InvokeAsync<T>`:</span></span>

* <span data-ttu-id="5bf6e-203">Se una `InvokeAsync<T>` chiamata a ha esito negativo in modo sincrono, si verifica un'eccezione .NET.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-203">If a call to `InvokeAsync<T>` fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="5bf6e-204">Una chiamata `InvokeAsync<T>` a potrebbe non riuscire, ad esempio, perché gli argomenti forniti non possono essere serializzati.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-204">A call to `InvokeAsync<T>` may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="5bf6e-205">Il codice dello sviluppatore deve intercettare l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-205">Developer code must catch the exception.</span></span> <span data-ttu-id="5bf6e-206">Se il codice dell'app in un gestore eventi o nel metodo Blazor del ciclo di vita del componente non gestisce un'eccezione, l'eccezione risultante viene irreversibile per un circuito Server.If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Server circuit.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-206">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="5bf6e-207">Se una `InvokeAsync<T>` chiamata a ha esito negativo in modo asincrono, .NET <xref:System.Threading.Tasks.Task> ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-207">If a call to `InvokeAsync<T>` fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="5bf6e-208">Una chiamata `InvokeAsync<T>` a potrebbe non riuscire, ad esempio, perché il codice `Promise` lato `rejected`JavaScript genera un'eccezione o restituisce un oggetto che viene completato come .</span><span class="sxs-lookup"><span data-stu-id="5bf6e-208">A call to `InvokeAsync<T>` may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="5bf6e-209">Il codice dello sviluppatore deve intercettare l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-209">Developer code must catch the exception.</span></span> <span data-ttu-id="5bf6e-210">Se si usa l'operatore [await,](/dotnet/csharp/language-reference/keywords/await) è consigliabile eseguire il wrapping della chiamata al metodo in un'istruzione [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con la gestione e la registrazione degli errori.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-210">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="5bf6e-211">In caso contrario, il codice non riuscito genera Blazor un'eccezione non gestita irreversibile per un circuito Server.Otherwise, the failing code results in an unhandled exception that's fatal to a Server circuit.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-211">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="5bf6e-212">Per impostazione `InvokeAsync<T>` predefinita, le chiamate a devono essere completate entro un determinato periodo, altrimenti la chiamata scade. Il periodo di timeout predefinito è un minuto.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-212">By default, calls to `InvokeAsync<T>` must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="5bf6e-213">Il timeout protegge il codice da una perdita di connettività di rete o codice JavaScript che non restituisce mai un messaggio di completamento.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-213">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="5bf6e-214">Se la chiamata scade, l'operazione risultante `Task` ha esito negativo con un file <xref:System.OperationCanceledException>.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-214">If the call times out, the resulting `Task` fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="5bf6e-215">Trap ed elaborare l'eccezione con la registrazione.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-215">Trap and process the exception with logging.</span></span>

<span data-ttu-id="5bf6e-216">Analogamente, il codice JavaScript può avviare [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) chiamate ai metodi .NET indicati dall'attributo .</span><span class="sxs-lookup"><span data-stu-id="5bf6e-216">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) attribute.</span></span> <span data-ttu-id="5bf6e-217">Se questi metodi .NET generano un'eccezione non gestita:</span><span class="sxs-lookup"><span data-stu-id="5bf6e-217">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="5bf6e-218">L'eccezione non viene considerata Blazor irreversibile per un circuito Server.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-218">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="5bf6e-219">Il lato `Promise` JavaScript viene rifiutato.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-219">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="5bf6e-220">È possibile utilizzare il codice di gestione degli errori sul lato .NET o sul lato JavaScript della chiamata al metodo.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-220">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="5bf6e-221">Per altre informazioni, vedere gli articoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bf6e-221">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="opno-locblazor-server-prerendering"></a>Blazor<span data-ttu-id="5bf6e-222">Prerendering del server</span><span class="sxs-lookup"><span data-stu-id="5bf6e-222"> Server prerendering</span></span>

Blazor<span data-ttu-id="5bf6e-223">i componenti possono essere sottoposti a prerendering utilizzando [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) in modo che il markup HTML di cui è stato eseguito il rendering venga restituito come parte della richiesta HTTP iniziale dell'utente.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-223"> components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="5bf6e-224">Questo funziona da:</span><span class="sxs-lookup"><span data-stu-id="5bf6e-224">This works by:</span></span>

* <span data-ttu-id="5bf6e-225">Creazione di un nuovo circuito per tutti i componenti prerenderizzati che fanno parte della stessa pagina.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-225">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="5bf6e-226">Generazione del codice HTML iniziale.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-226">Generating the initial HTML.</span></span>
* <span data-ttu-id="5bf6e-227">Trattare il `disconnected` circuito come fino a quando SignalR il browser dell'utente stabilisce una connessione allo stesso server.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-227">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="5bf6e-228">Quando viene stabilita la connessione, l'interattività sul circuito viene ripresa e il markup HTML dei componenti viene aggiornato.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-228">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="5bf6e-229">Se un componente genera un'eccezione non gestita durante il prerendering, ad esempio, durante un metodo del ciclo di vita o nella logica di rendering:If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span><span class="sxs-lookup"><span data-stu-id="5bf6e-229">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="5bf6e-230">L'eccezione è irreversibile per il circuito.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-230">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="5bf6e-231">L'eccezione viene generata `Component` nello stack di chiamate dall'helper tag.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-231">The exception is thrown up the call stack from the `Component` Tag Helper.</span></span> <span data-ttu-id="5bf6e-232">Pertanto, l'intera richiesta HTTP ha esito negativo a meno che l'eccezione viene rilevata in modo esplicito dal codice dello sviluppatore.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-232">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="5bf6e-233">In circostanze normali in cui il prerendering non riesce, continuare a compilare e eseguire il rendering del componente non ha senso perché non è possibile eseguire il rendering di un componente funzionante.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-233">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="5bf6e-234">Per tollerare gli errori che possono verificarsi durante il prerendering, la logica di gestione degli errori deve essere inserita all'interno di un componente che può generare eccezioni.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-234">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="5bf6e-235">Usare le istruzioni [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con la gestione e la registrazione degli errori.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-235">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="5bf6e-236">Anziché eseguire `Component` il wrapping `try-catch` di Tag Helper in un'istruzione, inserire la logica di gestione degli errori nel componente di cui è stato eseguito il `Component` rendering da Tag Helper.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-236">Instead of wrapping the `Component` Tag Helper in a `try-catch` statement, place error handling logic in the component rendered by the `Component` Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="5bf6e-237">Scenari avanzati</span><span class="sxs-lookup"><span data-stu-id="5bf6e-237">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="5bf6e-238">Rendering ricorsivo</span><span class="sxs-lookup"><span data-stu-id="5bf6e-238">Recursive rendering</span></span>

<span data-ttu-id="5bf6e-239">I componenti possono essere nidificati in modo ricorsivo.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-239">Components can be nested recursively.</span></span> <span data-ttu-id="5bf6e-240">Ciò è utile per rappresentare strutture di dati ricorsive.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-240">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="5bf6e-241">Ad esempio, `TreeNode` un componente `TreeNode` può eseguire il rendering di più componenti per ognuno degli elementi figlio del nodo.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-241">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="5bf6e-242">Quando si esegue il rendering in modo ricorsivo, evitare modelli di codifica che generano una ricorsione infinita:When rendering recursively, avoid coding patterns that result in infinite recursion:</span><span class="sxs-lookup"><span data-stu-id="5bf6e-242">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="5bf6e-243">Non eseguire il rendering ricorsivo di una struttura di dati che contiene un ciclo.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-243">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="5bf6e-244">Ad esempio, non eseguire il rendering di un nodo della struttura ad albero i cui elementi figlio includono se stesso.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-244">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="5bf6e-245">Non creare una catena di layout che contengono un ciclo.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-245">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="5bf6e-246">Ad esempio, non creare un layout il cui layout è se stesso.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-246">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="5bf6e-247">Non consentire a un utente finale di violare le invarianti di ricorsione (regole) tramite l'immissione di dati dannosi o chiamate di interoperabilità JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-247">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="5bf6e-248">Cicli infiniti durante il rendering:</span><span class="sxs-lookup"><span data-stu-id="5bf6e-248">Infinite loops during rendering:</span></span>

* <span data-ttu-id="5bf6e-249">Fa sì che il processo di rendering continui per sempre.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-249">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="5bf6e-250">Equivale alla creazione di un ciclo non terminato.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-250">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="5bf6e-251">In questi scenari, Blazor un circuito Server interessato ha esito negativo e il thread tenta in genere di:In these scenarios, an affected Server circuit fails, and the thread usually attempts to:</span><span class="sxs-lookup"><span data-stu-id="5bf6e-251">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="5bf6e-252">Consumare quanto tempo della CPU consentito dal sistema operativo, a tempo indeterminato.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-252">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="5bf6e-253">Consumare una quantità illimitata di memoria del server.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-253">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="5bf6e-254">L'utilizzo di memoria illimitata equivale allo scenario in cui un ciclo senza terminazione aggiunge voci a una raccolta a ogni iterazione.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-254">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="5bf6e-255">Per evitare modelli di ricorsione infinita, assicurarsi che il codice di rendering ricorsivo contenga condizioni di arresto adatte.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-255">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="5bf6e-256">Logica dell'albero di rendering personalizzata</span><span class="sxs-lookup"><span data-stu-id="5bf6e-256">Custom render tree logic</span></span>

<span data-ttu-id="5bf6e-257">La Blazor maggior parte dei componenti vengono implementati come file con `RenderTreeBuilder` estensione *razor* e vengono compilati per produrre la logica che opera su un per eseguire il rendering dell'output.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-257">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a `RenderTreeBuilder` to render their output.</span></span> <span data-ttu-id="5bf6e-258">Uno sviluppatore `RenderTreeBuilder` può implementare manualmente la logica utilizzando il codice procedurale di C.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-258">A developer may manually implement `RenderTreeBuilder` logic using procedural C# code.</span></span> <span data-ttu-id="5bf6e-259">Per altre informazioni, vedere <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-259">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="5bf6e-260">L'uso della logica del generatore di albero di rendering manuale è considerato uno scenario avanzato e non sicuro, non consigliato per lo sviluppo di componenti generali.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-260">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="5bf6e-261">Se `RenderTreeBuilder` viene scritto codice, lo sviluppatore deve garantire la correttezza del codice.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-261">If `RenderTreeBuilder` code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="5bf6e-262">Ad esempio, lo sviluppatore deve assicurarsi che:For example, the developer must ensure that:</span><span class="sxs-lookup"><span data-stu-id="5bf6e-262">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="5bf6e-263">Le `OpenElement` chiamate `CloseElement` a e sono bilanciate correttamente.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-263">Calls to `OpenElement` and `CloseElement` are correctly balanced.</span></span>
* <span data-ttu-id="5bf6e-264">Gli attributi vengono aggiunti solo nelle posizioni corrette.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-264">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="5bf6e-265">Una logica non corretta del generatore di albero di rendering manuale può causare un comportamento arbitrario non definito, inclusi arresti anomali, blocchi del server e vulnerabilità della sicurezza.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-265">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="5bf6e-266">Prendere in considerazione la logica del generatore di albero di rendering manuale sullo stesso livello di complessità e con lo stesso livello di *pericolo* della scrittura manuale del codice assembly o delle istruzioni MSIL.</span><span class="sxs-lookup"><span data-stu-id="5bf6e-266">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
