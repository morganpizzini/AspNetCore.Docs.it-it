---
title: 'Modelli di ASP.NET Core Blazor'
author: guardrex
description: 'Informazioni su ASP.NET Core Blazor modelli di app e la Blazor struttura del progetto.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/04/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/templates
ms.openlocfilehash: fc2e81cf130732d515fb871227031493e297cf9f
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507772"
---
# <a name="aspnet-core-no-locblazor-templates"></a><span data-ttu-id="7e6a8-103">Modelli di ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="7e6a8-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="7e6a8-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7e6a8-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7e6a8-105">Il Blazor Framework fornisce modelli per lo sviluppo di app per ognuno dei Blazor modelli di hosting:</span><span class="sxs-lookup"><span data-stu-id="7e6a8-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* <span data-ttu-id="7e6a8-106">Blazor WebAssembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="7e6a8-106">Blazor WebAssembly (`blazorwasm`)</span></span>
* <span data-ttu-id="7e6a8-107">Blazor Server (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="7e6a8-107">Blazor Server (`blazorserver`)</span></span>

<span data-ttu-id="7e6a8-108">Per ulteriori informazioni sui Blazor modelli di hosting di, vedere <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="7e6a8-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="7e6a8-109">Le opzioni del modello sono disponibili passando l' `--help` opzione al [`dotnet new`](/dotnet/core/tools/dotnet-new) comando CLI:</span><span class="sxs-lookup"><span data-stu-id="7e6a8-109">Template options are available by passing the `--help` option to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a><span data-ttu-id="7e6a8-110">Blazor struttura del progetto</span><span class="sxs-lookup"><span data-stu-id="7e6a8-110">Blazor project structure</span></span>

<span data-ttu-id="7e6a8-111">I file e le cartelle seguenti costituiscono un' Blazor app generata da un Blazor modello di progetto:</span><span class="sxs-lookup"><span data-stu-id="7e6a8-111">The following files and folders make up a Blazor app generated from a Blazor project template:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="7e6a8-112">`Program.cs`: Il punto di ingresso dell'app che configura:</span><span class="sxs-lookup"><span data-stu-id="7e6a8-112">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="7e6a8-113">[Host](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="7e6a8-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="7e6a8-114">Host webassembly ( Blazor WebAssembly ): il codice in questo file è univoco per le app create dal Blazor WebAssembly modello ( `blazorwasm` ).</span><span class="sxs-lookup"><span data-stu-id="7e6a8-114">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="7e6a8-115">Il `App` componente è il componente radice dell'app.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-115">The `App` component is the root component of the app.</span></span> <span data-ttu-id="7e6a8-116">Il `App` componente viene specificato come `app` elemento DOM ( `<div id="app">Loading...</div>` in `wwwroot/index.html` ) per la raccolta di componenti radice ( `builder.RootComponents.Add<App>("#app")` ).</span><span class="sxs-lookup"><span data-stu-id="7e6a8-116">The `App` component is specified as the `app` DOM element (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
    * <span data-ttu-id="7e6a8-117">I [Servizi](xref:blazor/fundamentals/dependency-injection) vengono aggiunti e configurati, ad esempio `builder.Services.AddSingleton<IMyDependency, MyDependency>()` .</span><span class="sxs-lookup"><span data-stu-id="7e6a8-117">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="7e6a8-118">`Program.cs`: Il punto di ingresso dell'app che configura:</span><span class="sxs-lookup"><span data-stu-id="7e6a8-118">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="7e6a8-119">[Host](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="7e6a8-119">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="7e6a8-120">Host webassembly ( Blazor WebAssembly ): il codice in questo file è univoco per le app create dal Blazor WebAssembly modello ( `blazorwasm` ).</span><span class="sxs-lookup"><span data-stu-id="7e6a8-120">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="7e6a8-121">Il `App` componente è il componente radice dell'app.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-121">The `App` component is the root component of the app.</span></span> <span data-ttu-id="7e6a8-122">Il `App` componente viene specificato come `app` elemento DOM ( `<app>Loading...</app>` in `wwwroot/index.html` ) per la raccolta di componenti radice ( `builder.RootComponents.Add<App>("app")` ).</span><span class="sxs-lookup"><span data-stu-id="7e6a8-122">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
    * <span data-ttu-id="7e6a8-123">I [Servizi](xref:blazor/fundamentals/dependency-injection) vengono aggiunti e configurati, ad esempio `builder.Services.AddSingleton<IMyDependency, MyDependency>()` .</span><span class="sxs-lookup"><span data-stu-id="7e6a8-123">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

* <span data-ttu-id="7e6a8-124">`Startup.cs` ( Blazor Server ): Contiene la logica di avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-124">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="7e6a8-125">La `Startup` classe definisce due metodi:</span><span class="sxs-lookup"><span data-stu-id="7e6a8-125">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="7e6a8-126">`ConfigureServices`: Configura i servizi [di inserimento delle dipendenze](xref:fundamentals/dependency-injection) dell'app.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-126">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="7e6a8-127">Nelle Blazor Server app, i servizi vengono aggiunti chiamando <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> e il `WeatherForecastService` viene aggiunto al contenitore del servizio per l'uso da parte del `FetchData` componente di esempio.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-127">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="7e6a8-128">`Configure`: Configura la pipeline di gestione delle richieste dell'app:</span><span class="sxs-lookup"><span data-stu-id="7e6a8-128">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="7e6a8-129"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> viene chiamato per configurare un endpoint per la connessione in tempo reale con il browser.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-129"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="7e6a8-130">La connessione viene creata con [SignalR](xref:signalr/introduction) , che è un Framework per l'aggiunta di funzionalità Web in tempo reale alle app.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-130">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="7e6a8-131">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) viene chiamato per impostare la pagina radice dell'app ( `Pages/_Host.cshtml` ) e abilitare la navigazione.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-131">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

* <span data-ttu-id="7e6a8-132">`wwwroot/index.html` ( Blazor WebAssembly ): La pagina radice dell'app è stata implementata come pagina HTML:</span><span class="sxs-lookup"><span data-stu-id="7e6a8-132">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="7e6a8-133">Quando viene inizialmente richiesta una pagina dell'app, il rendering della pagina viene eseguito e restituito nella risposta.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-133">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="7e6a8-134">La pagina specifica la posizione in cui `App` viene eseguito il rendering del componente radice.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-134">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="7e6a8-135">Il rendering del componente viene eseguito in corrispondenza della posizione dell' `app` elemento DOM ( `<app>...</app>` ).</span><span class="sxs-lookup"><span data-stu-id="7e6a8-135">The component is rendered at the location of the `app` DOM element (`<app>...</app>`).</span></span>
  * <span data-ttu-id="7e6a8-136">`_framework/blazor.webassembly.js`Viene caricato il file JavaScript, che:</span><span class="sxs-lookup"><span data-stu-id="7e6a8-136">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="7e6a8-137">Scarica il Runtime .NET, l'app e le dipendenze dell'app.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-137">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="7e6a8-138">Inizializza il runtime per l'esecuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-138">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="7e6a8-139">`App.razor`: Componente radice dell'app che imposta il routing sul lato client usando il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-139">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="7e6a8-140">Il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente intercetta la navigazione del browser ed esegue il rendering della pagina che corrisponde all'indirizzo richiesto.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-140">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="7e6a8-141">`Pages` cartella: contiene i componenti e le pagine instradabili ( `.razor` ) che costituiscono l' Blazor app e la Razor pagina radice di un' Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-141">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="7e6a8-142">La route per ogni pagina viene specificata utilizzando la [`@page`](xref:mvc/views/razor#page) direttiva.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-142">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="7e6a8-143">Il modello include quanto segue:</span><span class="sxs-lookup"><span data-stu-id="7e6a8-143">The template includes the following:</span></span>
  * <span data-ttu-id="7e6a8-144">`_Host.cshtml` ( Blazor Server ): La pagina radice dell'app è stata implementata come Razor Pagina</span><span class="sxs-lookup"><span data-stu-id="7e6a8-144">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="7e6a8-145">Quando viene inizialmente richiesta una pagina dell'app, il rendering della pagina viene eseguito e restituito nella risposta.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-145">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="7e6a8-146">`_framework/blazor.server.js`Viene caricato il file JavaScript, che configura la connessione in tempo reale SignalR tra il browser e il server.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-146">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="7e6a8-147">Nella pagina host viene specificata la posizione in cui `App` viene eseguito il rendering del componente radice ( `App.razor` ).</span><span class="sxs-lookup"><span data-stu-id="7e6a8-147">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="7e6a8-148">`Counter` ( `Pages/Counter.razor` ): Implementa la pagina del contatore.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-148">`Counter` (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="7e6a8-149">`Error` ( `Error.razor` , Blazor Server solo app): viene eseguito il rendering quando si verifica un'eccezione non gestita nell'app.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-149">`Error` (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="7e6a8-150">`FetchData` ( `Pages/FetchData.razor` ): Implementa la pagina Recupera dati.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-150">`FetchData` (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="7e6a8-151">`Index` ( `Pages/Index.razor` ): Implementa la Home page.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-151">`Index` (`Pages/Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="7e6a8-152">`Properties/launchSettings.json`: Include la [configurazione dell'ambiente di sviluppo](xref:fundamentals/environments#development-and-launchsettingsjson).</span><span class="sxs-lookup"><span data-stu-id="7e6a8-152">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

* <span data-ttu-id="7e6a8-153">`Shared` cartella: contiene altri componenti dell'interfaccia utente ( `.razor` ) usati dall'app:</span><span class="sxs-lookup"><span data-stu-id="7e6a8-153">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="7e6a8-154">`MainLayout` ( `MainLayout.razor` ): [Componente del layout](xref:blazor/layouts)dell'app.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-154">`MainLayout` (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="7e6a8-155">`NavMenu` ( `NavMenu.razor` ): Implementa la navigazione dell'intestazione laterale.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-155">`NavMenu` (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="7e6a8-156">Include il [ `NavLink` componente](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ) che esegue il rendering dei collegamenti di navigazione ad altri Razor componenti.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-156">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="7e6a8-157">Il <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente indica automaticamente uno stato selezionato quando viene caricato il componente, che consente all'utente di comprendere il componente attualmente visualizzato.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-157">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="7e6a8-158">`_Imports.razor`: Include Razor direttive comuni da includere nei componenti dell'app ( `.razor` ), ad esempio le [`@using`](xref:mvc/views/razor#using) direttive per gli spazi dei nomi.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-158">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="7e6a8-159">`Data` Folder ( Blazor Server ): contiene la `WeatherForecast` classe e l'implementazione di `WeatherForecastService` che forniscono dati meteorologici di esempio al componente dell'app `FetchData` .</span><span class="sxs-lookup"><span data-stu-id="7e6a8-159">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="7e6a8-160">`wwwroot`: La cartella [radice Web](xref:fundamentals/index#web-root) per l'app contenente le risorse statiche pubbliche dell'app.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-160">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="7e6a8-161">`appsettings.json`: Include [le impostazioni di configurazione](xref:blazor/fundamentals/configuration) per l'app.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-161">`appsettings.json`: Holds [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span> <span data-ttu-id="7e6a8-162">In un' Blazor WebAssembly app, il file di impostazioni dell'app si trova nella `wwwroot` cartella.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-162">In a Blazor WebAssembly app, the app settings file is located in the `wwwroot` folder.</span></span> <span data-ttu-id="7e6a8-163">In un' Blazor Server app, il file di impostazioni dell'app si trova nella radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="7e6a8-163">In a Blazor Server app, the app settings file is located at the project root.</span></span>
