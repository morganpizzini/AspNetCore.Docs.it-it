---
title: ASP.NET Blazor modelli di base
author: guardrex
description: Informazioni sui Blazor modelli di Blazor app ASP.NET Core e sulla struttura del progetto.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 0a4a508beeae3d7bc665372d925989aa4e34ad52
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661724"
---
# <a name="aspnet-core-opno-locblazor-templates"></a><span data-ttu-id="0a45f-103">ASP.NET Blazor modelli di base</span><span class="sxs-lookup"><span data-stu-id="0a45f-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="0a45f-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0a45f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="0a45f-105">Il Blazor framework fornisce modelli per sviluppare Blazor app per ognuno dei modelli di hosting:The framework provides templates to develop apps for each of the hosting models:</span><span class="sxs-lookup"><span data-stu-id="0a45f-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="0a45f-106">WebAssembly`blazorwasm`( )</span><span class="sxs-lookup"><span data-stu-id="0a45f-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="0a45f-107">Server`blazorserver`( )</span><span class="sxs-lookup"><span data-stu-id="0a45f-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="0a45f-108">Per ulteriori Blazorinformazioni sui modelli <xref:blazor/hosting-models>di hosting, vedere .</span><span class="sxs-lookup"><span data-stu-id="0a45f-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="0a45f-109">Per istruzioni dettagliate sulla creazione Blazor di un'app <xref:blazor/get-started>da un modello, vedere .</span><span class="sxs-lookup"><span data-stu-id="0a45f-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

<span data-ttu-id="0a45f-110">Le opzioni del modello `--help` sono disponibili passando l'opzione al [comando dotnet new](/dotnet/core/tools/dotnet-new) CLI:</span><span class="sxs-lookup"><span data-stu-id="0a45f-110">Template options are available by passing the `--help` option to the [dotnet new](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="opno-locblazor-project-structure"></a>Blazor<span data-ttu-id="0a45f-111">struttura del progetto</span><span class="sxs-lookup"><span data-stu-id="0a45f-111"> project structure</span></span>

<span data-ttu-id="0a45f-112">I file e le Blazor cartelle seguenti Blazor costituiscono un'app generata da un modello:</span><span class="sxs-lookup"><span data-stu-id="0a45f-112">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="0a45f-113">*Program.cs* &ndash; punto di ingresso dell'app che configura:</span><span class="sxs-lookup"><span data-stu-id="0a45f-113">*Program.cs* &ndash; The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="0a45f-114">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="0a45f-114">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="0a45f-115">Host WebAssemblyBlazor ( &ndash; WebAssembly) Il codice in questo Blazor file è`blazorwasm`univoco per le app create dal modello WebAssembly ( ).</span><span class="sxs-lookup"><span data-stu-id="0a45f-115">WebAssembly host (Blazor WebAssembly) &ndash; The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="0a45f-116">Il `App` componente, che è il componente radice dell'app, viene specificato come elemento `app` DOM per il `Add` metodo.</span><span class="sxs-lookup"><span data-stu-id="0a45f-116">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="0a45f-117">I servizi possono `ConfigureServices` essere configurati con il metodo `builder.Services.AddSingleton<IMyDependency, MyDependency>();`nel generatore host, ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="0a45f-117">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="0a45f-118">La configurazione può essere fornita`builder.Configuration`tramite il generatore host ( ).</span><span class="sxs-lookup"><span data-stu-id="0a45f-118">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="0a45f-119">*Startup.cs* Blazor ( &ndash; Server) contiene la logica di avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="0a45f-119">*Startup.cs* (Blazor Server) &ndash; Contains the app's startup logic.</span></span> <span data-ttu-id="0a45f-120">La `Startup` classe definisce due metodi:</span><span class="sxs-lookup"><span data-stu-id="0a45f-120">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="0a45f-121">`ConfigureServices`&ndash; Configura i servizi [di inserimento delle dipendenze (DI)](xref:fundamentals/dependency-injection) dell'app.</span><span class="sxs-lookup"><span data-stu-id="0a45f-121">`ConfigureServices` &ndash; Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="0a45f-122">Nelle Blazor app server i servizi <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>vengono `WeatherForecastService` aggiunti chiamando e quelli aggiunti al `FetchData` contenitore dei servizi per l'utilizzo da parte del componente di esempio.</span><span class="sxs-lookup"><span data-stu-id="0a45f-122">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="0a45f-123">`Configure`&ndash; Configura la pipeline di gestione delle richieste dell'app:</span><span class="sxs-lookup"><span data-stu-id="0a45f-123">`Configure` &ndash; Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="0a45f-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>viene chiamato per configurare un endpoint per la connessione in tempo reale con il browser.</span><span class="sxs-lookup"><span data-stu-id="0a45f-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="0a45f-125">La connessione viene [SignalR](xref:signalr/introduction)creata con , che è un framework per l'aggiunta di funzionalità Web in tempo reale alle app.</span><span class="sxs-lookup"><span data-stu-id="0a45f-125">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="0a45f-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) viene chiamato per configurare la pagina radice dell'app (*Pages/_Host.cshtml*) e abilitare la navigazione.</span><span class="sxs-lookup"><span data-stu-id="0a45f-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="0a45f-127">*wwwroot/index.html* Blazor ( WebAssembly) &ndash; La pagina radice dell'app implementata come pagina HTML:</span><span class="sxs-lookup"><span data-stu-id="0a45f-127">*wwwroot/index.html* (Blazor WebAssembly) &ndash; The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="0a45f-128">Quando una pagina dell'app viene inizialmente richiesta, il rendering di questa pagina viene eseguito e restituito nella risposta.</span><span class="sxs-lookup"><span data-stu-id="0a45f-128">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="0a45f-129">La pagina specifica dove `App` viene eseguito il rendering del componente radice.</span><span class="sxs-lookup"><span data-stu-id="0a45f-129">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="0a45f-130">Il `App` componente (*App.razor*) `app` viene specificato `AddComponent` come `Startup.Configure`elemento DOM per il metodo in .</span><span class="sxs-lookup"><span data-stu-id="0a45f-130">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="0a45f-131">Il `_framework/blazor.webassembly.js` file JavaScript viene caricato, che:</span><span class="sxs-lookup"><span data-stu-id="0a45f-131">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="0a45f-132">Scarica il runtime di .NET, l'app e le dipendenze dell'app.</span><span class="sxs-lookup"><span data-stu-id="0a45f-132">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="0a45f-133">Inizializza il runtime per eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="0a45f-133">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="0a45f-134">*App.razor* &ndash; Il componente radice dell'app che configura <xref:Microsoft.AspNetCore.Components.Routing.Router> il routing sul lato client tramite il componente.</span><span class="sxs-lookup"><span data-stu-id="0a45f-134">*App.razor* &ndash; The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="0a45f-135">Il `Router` componente intercetta la navigazione del browser ed esegue il rendering della pagina che corrisponde all'indirizzo richiesto.</span><span class="sxs-lookup"><span data-stu-id="0a45f-135">The `Router` component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="0a45f-136">*Cartella* &ndash; Pagine Contiene i componenti/pagine instradabili ( Blazor *.razor*) che costituiscono l'app e la pagina Razor radice di un'app Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="0a45f-136">*Pages* folder &ndash; Contains the routable components/pages (*.razor*) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="0a45f-137">La route per ogni pagina [`@page`](xref:mvc/views/razor#page) viene specificata utilizzando la direttiva.</span><span class="sxs-lookup"><span data-stu-id="0a45f-137">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="0a45f-138">Il modello include quanto segue:</span><span class="sxs-lookup"><span data-stu-id="0a45f-138">The template includes the following:</span></span>
  * <span data-ttu-id="0a45f-139">*_Host.cshtml* Blazor ( &ndash; Server) La pagina radice dell'app implementata come pagina Razor:</span><span class="sxs-lookup"><span data-stu-id="0a45f-139">*_Host.cshtml* (Blazor Server) &ndash; The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="0a45f-140">Quando una pagina dell'app viene inizialmente richiesta, il rendering di questa pagina viene eseguito e restituito nella risposta.</span><span class="sxs-lookup"><span data-stu-id="0a45f-140">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="0a45f-141">Il `_framework/blazor.server.js` file JavaScript viene caricato, che SignalR imposta la connessione in tempo reale tra il browser e il server.</span><span class="sxs-lookup"><span data-stu-id="0a45f-141">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="0a45f-142">La pagina Host specifica `App` dove viene eseguito il rendering del componente radice (*App.razor*).</span><span class="sxs-lookup"><span data-stu-id="0a45f-142">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>
  * <span data-ttu-id="0a45f-143">`Counter`(*Counter.razor*) &ndash; Implementa la pagina Contatore.</span><span class="sxs-lookup"><span data-stu-id="0a45f-143">`Counter` (*Counter.razor*) &ndash; Implements the Counter page.</span></span>
  * <span data-ttu-id="0a45f-144">`Error`(Solo*app Error.razor*, Blazor app server) &ndash; Viene eseguito il rendering quando si verifica un'eccezione non gestita nell'app.</span><span class="sxs-lookup"><span data-stu-id="0a45f-144">`Error` (*Error.razor*, Blazor Server app only) &ndash; Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="0a45f-145">`FetchData`(*FetchData.razor*) &ndash; Implementa la pagina dei dati Fetch.</span><span class="sxs-lookup"><span data-stu-id="0a45f-145">`FetchData` (*FetchData.razor*) &ndash; Implements the Fetch data page.</span></span>
  * <span data-ttu-id="0a45f-146">`Index`(*Index.razor*) &ndash; Implementa la home page.</span><span class="sxs-lookup"><span data-stu-id="0a45f-146">`Index` (*Index.razor*) &ndash; Implements the Home page.</span></span>

* <span data-ttu-id="0a45f-147">*Cartella* &ndash; condivisa Contiene altri componenti dell'interfaccia utente (*.razor*) utilizzati dall'app:</span><span class="sxs-lookup"><span data-stu-id="0a45f-147">*Shared* folder &ndash; Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="0a45f-148">`MainLayout`(*MainLayout.razor*) &ndash; Componente di layout dell'app.</span><span class="sxs-lookup"><span data-stu-id="0a45f-148">`MainLayout` (*MainLayout.razor*) &ndash; The app's layout component.</span></span>
  * <span data-ttu-id="0a45f-149">`NavMenu`(*NavMenu.razor*) &ndash; Implementa la navigazione nella barra laterale.</span><span class="sxs-lookup"><span data-stu-id="0a45f-149">`NavMenu` (*NavMenu.razor*) &ndash; Implements sidebar navigation.</span></span> <span data-ttu-id="0a45f-150">Include il componente<xref:Microsoft.AspNetCore.Components.Routing.NavLink> [NavLink](xref:blazor/routing#navlink-component) ( ), che esegue il rendering dei collegamenti di navigazione ad altri componenti Razor.</span><span class="sxs-lookup"><span data-stu-id="0a45f-150">Includes the [NavLink component](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="0a45f-151">Il `NavLink` componente indica automaticamente uno stato selezionato quando viene caricato il componente, che consente all'utente di comprendere quale componente è attualmente visualizzato.</span><span class="sxs-lookup"><span data-stu-id="0a45f-151">The `NavLink` component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="0a45f-152">*_Imports.razor* &ndash; Include direttive Razor comuni da includere nei componenti dell'app (*.razor*), ad [`@using`](xref:mvc/views/razor#using) esempio le direttive per gli spazi dei nomi.</span><span class="sxs-lookup"><span data-stu-id="0a45f-152">*_Imports.razor* &ndash; Includes common Razor directives to include in the app's components (*.razor*), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="0a45f-153">*Cartella* datiBlazor (Server) &ndash; Contiene la `WeatherForecast` `WeatherForecastService` classe e l'implementazione di `FetchData` che forniscono dati meteo di esempio al componente dell'app.</span><span class="sxs-lookup"><span data-stu-id="0a45f-153">*Data* folder (Blazor Server) &ndash; Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="0a45f-154">*wwwroot* &ndash; La cartella [Radice Web](xref:fundamentals/index#web-root) per l'app contenente le risorse statiche pubbliche dell'app.</span><span class="sxs-lookup"><span data-stu-id="0a45f-154">*wwwroot* &ndash; The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="0a45f-155">*appsettings.json* Blazor ( &ndash; Server) Impostazioni di configurazione per l'app.</span><span class="sxs-lookup"><span data-stu-id="0a45f-155">*appsettings.json* (Blazor Server) &ndash; Configuration settings for the app.</span></span>
