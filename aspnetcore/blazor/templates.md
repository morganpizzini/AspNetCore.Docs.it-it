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
ms.openlocfilehash: 3a491e43aec8291fcf03696b53240bd33e0deda1
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102489"
---
# <a name="aspnet-core-blazor-templates"></a>Modelli di ASP.NET Core Blazor

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Il Blazor Framework fornisce modelli per lo sviluppo di app per ognuno dei Blazor modelli di hosting:

* BlazorWebassembly ( `blazorwasm` )
* BlazorServer ( `blazorserver` )

Per ulteriori informazioni sui Blazor modelli di hosting di, vedere <xref:blazor/hosting-models> .

Per istruzioni dettagliate sulla creazione di un' Blazor app da un modello, vedere <xref:blazor/get-started> .

Le opzioni del modello sono disponibili passando l' `--help` opzione al comando [DotNet New](/dotnet/core/tools/dotnet-new) CLI:

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazorstruttura del progetto

I file e le cartelle seguenti costituiscono un' Blazor app generata da un Blazor modello:

* *Program.cs*: il punto di ingresso dell'app che configura:

  * [Host](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor Server)
  * Host webassembly (webassembly Blazor ): il codice in questo file è univoco per le app create dal Blazor modello webassembly ( `blazorwasm` ).
    * Il `App` componente, che è il componente radice dell'app, viene specificato come `app` elemento DOM per il `Add` metodo.
    * I servizi possono essere configurati con il `ConfigureServices` metodo nel generatore host (ad esempio, `builder.Services.AddSingleton<IMyDependency, MyDependency>();` ).
    * La configurazione può essere fornita tramite il generatore host ( `builder.Configuration` ).

* *Startup.cs* ( Blazor Server): contiene la logica di avvio dell'app. La `Startup` classe definisce due metodi:

  * `ConfigureServices`: Configura i servizi [di inserimento delle dipendenze](xref:fundamentals/dependency-injection) dell'app. Nelle Blazor app Server i servizi vengono aggiunti chiamando <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> e il `WeatherForecastService` viene aggiunto al contenitore del servizio per l'uso da parte del componente di esempio `FetchData` .
  * `Configure`: Configura la pipeline di gestione delle richieste dell'app:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>viene chiamato per configurare un endpoint per la connessione in tempo reale con il browser. La connessione viene creata con [SignalR](xref:signalr/introduction) , che è un Framework per l'aggiunta di funzionalità Web in tempo reale alle app.
    * [MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) viene chiamato per configurare la pagina radice dell'app (*pages/_Host. cshtml*) e abilitare la navigazione.

* *wwwroot/index.html* ( Blazor webassembly): pagina radice dell'app implementata come pagina HTML:
  * Quando viene inizialmente richiesta una pagina dell'app, il rendering della pagina viene eseguito e restituito nella risposta.
  * La pagina specifica la posizione in cui `App` viene eseguito il rendering del componente radice. Il `App` componente (*app. Razor*) viene specificato come `app` elemento DOM al `AddComponent` metodo in `Startup.Configure` .
  * `_framework/blazor.webassembly.js`Viene caricato il file JavaScript, che:
    * Scarica il Runtime .NET, l'app e le dipendenze dell'app.
    * Inizializza il runtime per l'esecuzione dell'app.

* *App. Razor*: componente radice dell'app che configura il routing sul lato client usando il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente. Il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente intercetta la navigazione del browser ed esegue il rendering della pagina che corrisponde all'indirizzo richiesto.

* Cartella *pages* : contiene i componenti e le pagine instradabili (*Razor*) che costituiscono l' Blazor app e la Razor pagina radice di un' Blazor app Server. La route per ogni pagina viene specificata utilizzando la [`@page`](xref:mvc/views/razor#page) direttiva. Il modello include quanto segue:
  * *_Host. cshtml* ( Blazor Server): pagina radice dell'app implementata come Razor pagina:
    * Quando viene inizialmente richiesta una pagina dell'app, il rendering della pagina viene eseguito e restituito nella risposta.
    * `_framework/blazor.server.js`Viene caricato il file JavaScript, che configura la connessione in tempo reale SignalR tra il browser e il server.
    * Nella pagina host viene specificata la posizione in cui `App` viene eseguito il rendering del componente radice (*app. Razor*).
  * `Counter`(*Counter. Razor*): implementa la pagina del contatore.
  * `Error`(*Error. Razor*, Blazor Solo app Server): il rendering viene eseguito quando si verifica un'eccezione non gestita nell'app.
  * `FetchData`(*FetchData. Razor*): implementa la pagina Recupera dati.
  * `Index`(*Index. Razor*): implementa la Home page.

* Cartella *condivisa* : contiene altri componenti dell'interfaccia utente (*Razor*) usati dall'app:
  * `MainLayout`(*MainLayout. Razor*): componente del layout dell'app.
  * `NavMenu`(*NavMenu. Razor*): implementa la navigazione nell'intestazione laterale. Include il [componente NavLink](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), che esegue il rendering dei collegamenti di navigazione ad altri Razor componenti. Il <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente indica automaticamente uno stato selezionato quando viene caricato il componente, che consente all'utente di comprendere il componente attualmente visualizzato.

* *_Imports. Razor*: include Razor direttive comuni da includere nei componenti dell'app (*Razor*), ad esempio le [`@using`](xref:mvc/views/razor#using) direttive per gli spazi dei nomi.

* *Data* Folder ( Blazor Server): contiene la `WeatherForecast` classe e l'implementazione di `WeatherForecastService` che forniscono dati meteorologici di esempio al componente dell'app `FetchData` .

* *wwwroot*: la cartella [radice Web](xref:fundamentals/index#web-root) per l'app contenente le risorse statiche pubbliche dell'app.

* *appsettings.json* ( Blazor Server): impostazioni di configurazione per l'app.
