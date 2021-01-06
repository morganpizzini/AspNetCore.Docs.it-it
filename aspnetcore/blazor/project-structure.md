---
title: BlazorStruttura del progetto ASP.NET Core
author: guardrex
description: Informazioni sulla Blazor struttura del progetto di app ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/07/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/project-structure
ms.openlocfilehash: 6df84366dc3fc99d31a8a0e614ca860a50df7f5c
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "97513549"
---
# <a name="aspnet-core-no-locblazor-project-structure"></a>BlazorStruttura del progetto ASP.NET Core

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

I file e le cartelle seguenti costituiscono un' Blazor app generata da un Blazor modello di progetto:

::: moniker range=">= aspnetcore-5.0"

* `Program.cs`: Il punto di ingresso dell'app che configura:

  * [Host](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor Server )
  * Host webassembly ( Blazor WebAssembly ): il codice in questo file è univoco per le app create dal Blazor WebAssembly modello ( `blazorwasm` ).
    * Il `App` componente è il componente radice dell'app. Il `App` componente viene specificato come `div` elemento DOM con un valore `id` di `app` ( `<div id="app">Loading...</div>` in `wwwroot/index.html` ) per la raccolta di componenti radice ( `builder.RootComponents.Add<App>("#app")` ).
    * I [Servizi](xref:blazor/fundamentals/dependency-injection) vengono aggiunti e configurati, ad esempio `builder.Services.AddSingleton<IMyDependency, MyDependency>()` .

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Program.cs`: Il punto di ingresso dell'app che configura:

  * [Host](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor Server )
  * Host webassembly ( Blazor WebAssembly ): il codice in questo file è univoco per le app create dal Blazor WebAssembly modello ( `blazorwasm` ).
    * Il `App` componente è il componente radice dell'app. Il `App` componente viene specificato come `app` elemento DOM ( `<app>Loading...</app>` in `wwwroot/index.html` ) per la raccolta di componenti radice ( `builder.RootComponents.Add<App>("app")` ).
    * I [Servizi](xref:blazor/fundamentals/dependency-injection) vengono aggiunti e configurati, ad esempio `builder.Services.AddSingleton<IMyDependency, MyDependency>()` .

::: moniker-end

* `Startup.cs` ( Blazor Server ): Contiene la logica di avvio dell'app. La `Startup` classe definisce due metodi:

  * `ConfigureServices`: Configura i servizi [di inserimento delle dipendenze](xref:fundamentals/dependency-injection) dell'app. Nelle Blazor Server app, i servizi vengono aggiunti chiamando <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> e il `WeatherForecastService` viene aggiunto al contenitore del servizio per l'uso da parte del `FetchData` componente di esempio.
  * `Configure`: Configura la pipeline di gestione delle richieste dell'app:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> viene chiamato per configurare un endpoint per la connessione in tempo reale con il browser. La connessione viene creata con [SignalR](xref:signalr/introduction) , che è un Framework per l'aggiunta di funzionalità Web in tempo reale alle app.
    * [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) viene chiamato per impostare la pagina radice dell'app ( `Pages/_Host.cshtml` ) e abilitare la navigazione.

::: moniker range=">= aspnetcore-5.0"

* `wwwroot/index.html` ( Blazor WebAssembly ): La pagina radice dell'app è stata implementata come pagina HTML:
  * Quando viene inizialmente richiesta una pagina dell'app, il rendering della pagina viene eseguito e restituito nella risposta.
  * La pagina specifica la posizione in cui `App` viene eseguito il rendering del componente radice. Il rendering del componente viene eseguito in corrispondenza della posizione dell' `div` elemento DOM con un valore `id` di `app` ( `<div id="app">Loading...</div>` ).
  * `_framework/blazor.webassembly.js`Viene caricato il file JavaScript, che:
    * Scarica il Runtime .NET, l'app e le dipendenze dell'app.
    * Inizializza il runtime per l'esecuzione dell'app.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `wwwroot/index.html` ( Blazor WebAssembly ): La pagina radice dell'app è stata implementata come pagina HTML:
  * Quando viene inizialmente richiesta una pagina dell'app, il rendering della pagina viene eseguito e restituito nella risposta.
  * La pagina specifica la posizione in cui `App` viene eseguito il rendering del componente radice. Il rendering del componente viene eseguito in corrispondenza della posizione dell' `app` elemento DOM ( `<app>Loading...</app>` ).
  * `_framework/blazor.webassembly.js`Viene caricato il file JavaScript, che:
    * Scarica il Runtime .NET, l'app e le dipendenze dell'app.
    * Inizializza il runtime per l'esecuzione dell'app.

::: moniker-end

* `App.razor`: Componente radice dell'app che imposta il routing sul lato client usando il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente. Il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente intercetta la navigazione del browser ed esegue il rendering della pagina che corrisponde all'indirizzo richiesto.

* `Pages` cartella: contiene i componenti e le pagine instradabili ( `.razor` ) che costituiscono l' Blazor app e la Razor pagina radice di un' Blazor Server app. La route per ogni pagina viene specificata utilizzando la [`@page`](xref:mvc/views/razor#page) direttiva. Il modello include quanto segue:
  * `_Host.cshtml` ( Blazor Server ): La pagina radice dell'app è stata implementata come Razor Pagina
    * Quando viene inizialmente richiesta una pagina dell'app, il rendering della pagina viene eseguito e restituito nella risposta.
    * `_framework/blazor.server.js`Viene caricato il file JavaScript, che configura la connessione in tempo reale SignalR tra il browser e il server.
    * Nella pagina host viene specificata la posizione in cui `App` viene eseguito il rendering del componente radice ( `App.razor` ).
  * `Counter` Component ( `Pages/Counter.razor` ): implementa la pagina del contatore.
  * `Error` componente ( `Error.razor` , Blazor Server solo app): viene eseguito il rendering quando si verifica un'eccezione non gestita nell'app.
  * `FetchData` Component ( `Pages/FetchData.razor` ): implementa la pagina Recupera dati.
  * `Index` Component ( `Pages/Index.razor` ): implementa la Home page.
  
* `Properties/launchSettings.json`: Include la [configurazione dell'ambiente di sviluppo](xref:fundamentals/environments#development-and-launchsettingsjson).

::: moniker range=">= aspnetcore-5.0"

* `Shared` cartella: contiene altri componenti dell'interfaccia utente ( `.razor` ) usati dall'app:
  * `MainLayout` Component ( `MainLayout.razor` ): [componente del layout](xref:blazor/layouts)dell'app.
  * `MainLayout.razor.css`: Foglio di stile per il layout principale dell'app.
  * `NavMenu` Component ( `NavMenu.razor` ): implementa la navigazione nell'intestazione laterale. Include il [ `NavLink` componente](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ) che esegue il rendering dei collegamenti di navigazione ad altri Razor componenti. Il <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente indica automaticamente uno stato selezionato quando viene caricato il componente, che consente all'utente di comprendere il componente attualmente visualizzato.
  * `NavMenu.razor.css`: Foglio di stile per il menu di navigazione dell'app.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Shared` cartella: contiene altri componenti dell'interfaccia utente ( `.razor` ) usati dall'app:
  * `MainLayout` Component ( `MainLayout.razor` ): [componente del layout](xref:blazor/layouts)dell'app.
  * `NavMenu` Component ( `NavMenu.razor` ): implementa la navigazione nell'intestazione laterale. Include il [ `NavLink` componente](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ) che esegue il rendering dei collegamenti di navigazione ad altri Razor componenti. Il <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente indica automaticamente uno stato selezionato quando viene caricato il componente, che consente all'utente di comprendere il componente attualmente visualizzato.
  
::: moniker-end

* `_Imports.razor`: Include Razor direttive comuni da includere nei componenti dell'app ( `.razor` ), ad esempio le [`@using`](xref:mvc/views/razor#using) direttive per gli spazi dei nomi.

* `Data` Folder ( Blazor Server ): contiene la `WeatherForecast` classe e l'implementazione di `WeatherForecastService` che forniscono dati meteorologici di esempio al componente dell'app `FetchData` .

* `wwwroot`: La cartella [radice Web](xref:fundamentals/index#web-root) per l'app contenente le risorse statiche pubbliche dell'app.

* `appsettings.json`: Include [le impostazioni di configurazione](xref:blazor/fundamentals/configuration) per l'app. In un' Blazor WebAssembly app, il file di impostazioni dell'app si trova nella `wwwroot` cartella. In un' Blazor Server app, il file di impostazioni dell'app si trova nella radice del progetto.
