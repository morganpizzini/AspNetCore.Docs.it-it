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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 6359a02b23803f26c4a40772c68d39e804396403
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401896"
---
# <a name="aspnet-core-blazor-templates"></a>Modelli di ASP.NET Core Blazor

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Il Blazor Framework fornisce modelli per lo sviluppo di app per ognuno dei Blazor modelli di hosting:

* Blazor WebAssembly (`blazorwasm`)
* Blazor Server (`blazorserver`)

Per ulteriori informazioni sui Blazor modelli di hosting di, vedere <xref:blazor/hosting-models> .

Per istruzioni dettagliate sulla creazione di un' Blazor app da un modello, vedere <xref:blazor/get-started> .

Le opzioni del modello sono disponibili passando l' `--help` opzione al [`dotnet new`](/dotnet/core/tools/dotnet-new) comando CLI:

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazorstruttura del progetto

I file e le cartelle seguenti costituiscono un' Blazor app generata da un Blazor modello:

* `Program.cs`: Il punto di ingresso dell'app che configura:

  * [Host](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor Server )
  * Host webassembly ( Blazor WebAssembly ): il codice in questo file è univoco per le app create dal Blazor WebAssembly modello ( `blazorwasm` ).
    * Il `App` componente, che è il componente radice dell'app, viene specificato come `app` elemento DOM per il `Add` metodo.
    * I servizi possono essere configurati con il `ConfigureServices` metodo nel generatore host (ad esempio, `builder.Services.AddSingleton<IMyDependency, MyDependency>();` ).
    * La configurazione può essere fornita tramite il generatore host ( `builder.Configuration` ).

* `Startup.cs`( Blazor Server ): Contiene la logica di avvio dell'app. La `Startup` classe definisce due metodi:

  * `ConfigureServices`: Configura i servizi [di inserimento delle dipendenze](xref:fundamentals/dependency-injection) dell'app. Nelle Blazor Server app, i servizi vengono aggiunti chiamando <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> e il `WeatherForecastService` viene aggiunto al contenitore del servizio per l'uso da parte del `FetchData` componente di esempio.
  * `Configure`: Configura la pipeline di gestione delle richieste dell'app:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>viene chiamato per configurare un endpoint per la connessione in tempo reale con il browser. La connessione viene creata con [SignalR](xref:signalr/introduction) , che è un Framework per l'aggiunta di funzionalità Web in tempo reale alle app.
    * [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*)viene chiamato per impostare la pagina radice dell'app ( `Pages/_Host.cshtml` ) e abilitare la navigazione.

* `wwwroot/index.html`( Blazor WebAssembly ): La pagina radice dell'app è stata implementata come pagina HTML:
  * Quando viene inizialmente richiesta una pagina dell'app, il rendering della pagina viene eseguito e restituito nella risposta.
  * La pagina specifica la posizione in cui `App` viene eseguito il rendering del componente radice. Il `App` componente ( `App.razor` ) viene specificato come `app` elemento DOM al `AddComponent` metodo in `Startup.Configure` .
  * `_framework/blazor.webassembly.js`Viene caricato il file JavaScript, che:
    * Scarica il Runtime .NET, l'app e le dipendenze dell'app.
    * Inizializza il runtime per l'esecuzione dell'app.

* `App.razor`: Componente radice dell'app che imposta il routing sul lato client usando il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente. Il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente intercetta la navigazione del browser ed esegue il rendering della pagina che corrisponde all'indirizzo richiesto.

* `Pages`cartella: contiene i componenti e le pagine instradabili ( `.razor` ) che costituiscono l' Blazor app e la Razor pagina radice di un' Blazor Server app. La route per ogni pagina viene specificata utilizzando la [`@page`](xref:mvc/views/razor#page) direttiva. Il modello include quanto segue:
  * `_Host.cshtml`( Blazor Server ): La pagina radice dell'app è stata implementata come Razor Pagina
    * Quando viene inizialmente richiesta una pagina dell'app, il rendering della pagina viene eseguito e restituito nella risposta.
    * `_framework/blazor.server.js`Viene caricato il file JavaScript, che configura la connessione in tempo reale SignalR tra il browser e il server.
    * Nella pagina host viene specificata la posizione in cui `App` viene eseguito il rendering del componente radice ( `App.razor` ).
  * `Counter`( `Pages/Counter.razor` ): Implementa la pagina del contatore.
  * `Error`( `Error.razor` , Blazor Server solo app): viene eseguito il rendering quando si verifica un'eccezione non gestita nell'app.
  * `FetchData`( `Pages/FetchData.razor` ): Implementa la pagina Recupera dati.
  * `Index`( `Pages/Index.razor` ): Implementa la Home page.

* `Shared`cartella: contiene altri componenti dell'interfaccia utente ( `.razor` ) usati dall'app:
  * `MainLayout`( `MainLayout.razor` ): Componente del layout dell'app.
  * `NavMenu`( `NavMenu.razor` ): Implementa la navigazione dell'intestazione laterale. Include il [ `NavLink` componente](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ) che esegue il rendering dei collegamenti di navigazione ad altri Razor componenti. Il <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente indica automaticamente uno stato selezionato quando viene caricato il componente, che consente all'utente di comprendere il componente attualmente visualizzato.

* `_Imports.razor`: Include Razor direttive comuni da includere nei componenti dell'app ( `.razor` ), ad esempio le [`@using`](xref:mvc/views/razor#using) direttive per gli spazi dei nomi.

* `Data`Folder ( Blazor Server ): contiene la `WeatherForecast` classe e l'implementazione di `WeatherForecastService` che forniscono dati meteorologici di esempio al componente dell'app `FetchData` .

* `wwwroot`: La cartella [radice Web](xref:fundamentals/index#web-root) per l'app contenente le risorse statiche pubbliche dell'app.

* `appsettings.json`( Blazor Server ): Impostazioni di configurazione per l'app.
