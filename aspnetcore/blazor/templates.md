---
title: Modelli di ASP.NET Core Blazor
author: guardrex
description: Informazioni su ASP.NET Core Blazor modelli di app e la Blazor struttura del progetto.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: ce46d562285b95ff656ed43b3a63ca5e7315f4c8
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243213"
---
# <a name="aspnet-core-blazor-templates"></a><span data-ttu-id="ead98-103">Modelli di ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="ead98-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="ead98-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ead98-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ead98-105">Il Blazor Framework fornisce modelli per lo sviluppo di app per ognuno dei Blazor modelli di hosting:</span><span class="sxs-lookup"><span data-stu-id="ead98-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="ead98-106">Webassembly ( `blazorwasm` )</span><span class="sxs-lookup"><span data-stu-id="ead98-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="ead98-107">Server ( `blazorserver` )</span><span class="sxs-lookup"><span data-stu-id="ead98-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="ead98-108">Per ulteriori informazioni sui Blazor modelli di hosting di, vedere <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="ead98-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="ead98-109">Per istruzioni dettagliate sulla creazione di un' Blazor app da un modello, vedere <xref:blazor/get-started> .</span><span class="sxs-lookup"><span data-stu-id="ead98-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

<span data-ttu-id="ead98-110">Le opzioni del modello sono disponibili passando l' `--help` opzione al [`dotnet new`](/dotnet/core/tools/dotnet-new) comando CLI:</span><span class="sxs-lookup"><span data-stu-id="ead98-110">Template options are available by passing the `--help` option to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazor<span data-ttu-id="ead98-111">struttura del progetto</span><span class="sxs-lookup"><span data-stu-id="ead98-111"> project structure</span></span>

<span data-ttu-id="ead98-112">I file e le cartelle seguenti costituiscono un' Blazor app generata da un Blazor modello:</span><span class="sxs-lookup"><span data-stu-id="ead98-112">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="ead98-113">`Program.cs`: Il punto di ingresso dell'app che configura:</span><span class="sxs-lookup"><span data-stu-id="ead98-113">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="ead98-114">[Host](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="ead98-114">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="ead98-115">Host webassembly (webassembly Blazor ): il codice in questo file è univoco per le app create dal Blazor modello webassembly ( `blazorwasm` ).</span><span class="sxs-lookup"><span data-stu-id="ead98-115">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="ead98-116">Il `App` componente, che è il componente radice dell'app, viene specificato come `app` elemento DOM per il `Add` metodo.</span><span class="sxs-lookup"><span data-stu-id="ead98-116">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="ead98-117">I servizi possono essere configurati con il `ConfigureServices` metodo nel generatore host (ad esempio, `builder.Services.AddSingleton<IMyDependency, MyDependency>();` ).</span><span class="sxs-lookup"><span data-stu-id="ead98-117">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="ead98-118">La configurazione può essere fornita tramite il generatore host ( `builder.Configuration` ).</span><span class="sxs-lookup"><span data-stu-id="ead98-118">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="ead98-119">`Startup.cs`( Blazor Server): contiene la logica di avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="ead98-119">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="ead98-120">La `Startup` classe definisce due metodi:</span><span class="sxs-lookup"><span data-stu-id="ead98-120">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="ead98-121">`ConfigureServices`: Configura i servizi [di inserimento delle dipendenze](xref:fundamentals/dependency-injection) dell'app.</span><span class="sxs-lookup"><span data-stu-id="ead98-121">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="ead98-122">Nelle Blazor app Server i servizi vengono aggiunti chiamando <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> e il `WeatherForecastService` viene aggiunto al contenitore del servizio per l'uso da parte del componente di esempio `FetchData` .</span><span class="sxs-lookup"><span data-stu-id="ead98-122">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="ead98-123">`Configure`: Configura la pipeline di gestione delle richieste dell'app:</span><span class="sxs-lookup"><span data-stu-id="ead98-123">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="ead98-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>viene chiamato per configurare un endpoint per la connessione in tempo reale con il browser.</span><span class="sxs-lookup"><span data-stu-id="ead98-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="ead98-125">La connessione viene creata con [SignalR](xref:signalr/introduction) , che è un Framework per l'aggiunta di funzionalità Web in tempo reale alle app.</span><span class="sxs-lookup"><span data-stu-id="ead98-125">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="ead98-126">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*)viene chiamato per impostare la pagina radice dell'app ( `Pages/_Host.cshtml` ) e abilitare la navigazione.</span><span class="sxs-lookup"><span data-stu-id="ead98-126">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

* <span data-ttu-id="ead98-127">`wwwroot/index.html`( Blazor Webassembly): pagina radice dell'app implementata come pagina HTML:</span><span class="sxs-lookup"><span data-stu-id="ead98-127">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="ead98-128">Quando viene inizialmente richiesta una pagina dell'app, il rendering della pagina viene eseguito e restituito nella risposta.</span><span class="sxs-lookup"><span data-stu-id="ead98-128">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="ead98-129">La pagina specifica la posizione in cui `App` viene eseguito il rendering del componente radice.</span><span class="sxs-lookup"><span data-stu-id="ead98-129">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="ead98-130">Il `App` componente ( `App.razor` ) viene specificato come `app` elemento DOM al `AddComponent` metodo in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="ead98-130">The `App` component (`App.razor`) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="ead98-131">`_framework/blazor.webassembly.js`Viene caricato il file JavaScript, che:</span><span class="sxs-lookup"><span data-stu-id="ead98-131">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="ead98-132">Scarica il Runtime .NET, l'app e le dipendenze dell'app.</span><span class="sxs-lookup"><span data-stu-id="ead98-132">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="ead98-133">Inizializza il runtime per l'esecuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="ead98-133">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="ead98-134">`App.razor`: Componente radice dell'app che imposta il routing sul lato client usando il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente.</span><span class="sxs-lookup"><span data-stu-id="ead98-134">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="ead98-135">Il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente intercetta la navigazione del browser ed esegue il rendering della pagina che corrisponde all'indirizzo richiesto.</span><span class="sxs-lookup"><span data-stu-id="ead98-135">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="ead98-136">`Pages`cartella: contiene i componenti e le pagine instradabili ( `.razor` ) che costituiscono l' Blazor app e la Razor pagina radice di un' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="ead98-136">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="ead98-137">La route per ogni pagina viene specificata utilizzando la [`@page`](xref:mvc/views/razor#page) direttiva.</span><span class="sxs-lookup"><span data-stu-id="ead98-137">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="ead98-138">Il modello include quanto segue:</span><span class="sxs-lookup"><span data-stu-id="ead98-138">The template includes the following:</span></span>
  * <span data-ttu-id="ead98-139">`_Host.cshtml`( Blazor Server): pagina radice dell'app implementata come Razor Pagina</span><span class="sxs-lookup"><span data-stu-id="ead98-139">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="ead98-140">Quando viene inizialmente richiesta una pagina dell'app, il rendering della pagina viene eseguito e restituito nella risposta.</span><span class="sxs-lookup"><span data-stu-id="ead98-140">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="ead98-141">`_framework/blazor.server.js`Viene caricato il file JavaScript, che configura la connessione in tempo reale SignalR tra il browser e il server.</span><span class="sxs-lookup"><span data-stu-id="ead98-141">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="ead98-142">Nella pagina host viene specificata la posizione in cui `App` viene eseguito il rendering del componente radice ( `App.razor` ).</span><span class="sxs-lookup"><span data-stu-id="ead98-142">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="ead98-143">`Counter`( `Pages/Counter.razor` ): Implementa la pagina del contatore.</span><span class="sxs-lookup"><span data-stu-id="ead98-143">`Counter` (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="ead98-144">`Error`( `Error.razor` , Blazor Solo app Server): viene eseguito il rendering quando si verifica un'eccezione non gestita nell'app.</span><span class="sxs-lookup"><span data-stu-id="ead98-144">`Error` (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="ead98-145">`FetchData`( `Pages/FetchData.razor` ): Implementa la pagina Recupera dati.</span><span class="sxs-lookup"><span data-stu-id="ead98-145">`FetchData` (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="ead98-146">`Index`( `Pages/Index.razor` ): Implementa la Home page.</span><span class="sxs-lookup"><span data-stu-id="ead98-146">`Index` (`Pages/Index.razor`): Implements the Home page.</span></span>

* <span data-ttu-id="ead98-147">`Shared`cartella: contiene altri componenti dell'interfaccia utente ( `.razor` ) usati dall'app:</span><span class="sxs-lookup"><span data-stu-id="ead98-147">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="ead98-148">`MainLayout`( `MainLayout.razor` ): Componente del layout dell'app.</span><span class="sxs-lookup"><span data-stu-id="ead98-148">`MainLayout` (`MainLayout.razor`): The app's layout component.</span></span>
  * <span data-ttu-id="ead98-149">`NavMenu`( `NavMenu.razor` ): Implementa la navigazione dell'intestazione laterale.</span><span class="sxs-lookup"><span data-stu-id="ead98-149">`NavMenu` (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="ead98-150">Include il [ `NavLink` componente](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ) che esegue il rendering dei collegamenti di navigazione ad altri Razor componenti.</span><span class="sxs-lookup"><span data-stu-id="ead98-150">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="ead98-151">Il <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente indica automaticamente uno stato selezionato quando viene caricato il componente, che consente all'utente di comprendere il componente attualmente visualizzato.</span><span class="sxs-lookup"><span data-stu-id="ead98-151">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="ead98-152">`_Imports.razor`: Include Razor direttive comuni da includere nei componenti dell'app ( `.razor` ), ad esempio le [`@using`](xref:mvc/views/razor#using) direttive per gli spazi dei nomi.</span><span class="sxs-lookup"><span data-stu-id="ead98-152">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="ead98-153">`Data`Folder ( Blazor Server): contiene la `WeatherForecast` classe e l'implementazione di `WeatherForecastService` che forniscono dati meteorologici di esempio al componente dell'app `FetchData` .</span><span class="sxs-lookup"><span data-stu-id="ead98-153">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="ead98-154">`wwwroot`: La cartella [radice Web](xref:fundamentals/index#web-root) per l'app contenente le risorse statiche pubbliche dell'app.</span><span class="sxs-lookup"><span data-stu-id="ead98-154">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="ead98-155">`appsettings.json`( Blazor Server): impostazioni di configurazione per l'app.</span><span class="sxs-lookup"><span data-stu-id="ead98-155">`appsettings.json` (Blazor Server): Configuration settings for the app.</span></span>
