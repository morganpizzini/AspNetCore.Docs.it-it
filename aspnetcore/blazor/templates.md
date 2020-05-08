---
title: Modelli Blazor di ASP.NET Core
author: guardrex
description: Informazioni su ASP.NET Core Blazor modelli di app Blazor e la struttura del progetto.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 705fa32ee72221b3c18653e9f3495b9cd61e9ad1
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967428"
---
# <a name="aspnet-core-blazor-templates"></a>Modelli Blazor di ASP.NET Core

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Il Blazor Framework fornisce modelli per lo sviluppo di app per ognuno Blazor dei modelli di hosting:

* BlazorWebassembly (`blazorwasm`)
* BlazorServer (`blazorserver`)

Per ulteriori informazioni sui Blazormodelli di hosting di, <xref:blazor/hosting-models>vedere.

Per istruzioni dettagliate sulla creazione di un' Blazor app da un modello, vedere. <xref:blazor/get-started>

Le opzioni del modello sono disponibili passando `--help` l'opzione al comando [DotNet New](/dotnet/core/tools/dotnet-new) CLI:

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazorstruttura del progetto

I file e le cartelle seguenti costituiscono un' Blazor app generata da un Blazor modello:

* *Program.cs* &ndash; il punto di ingresso dell'app che configura:

  * [Host](xref:fundamentals/host/generic-host) ASP.NET Core (Blazor Server)
  * Host webassembly (Blazor webassembly) &ndash; il codice in questo file è univoco per le app create dal Blazor modello webassembly (`blazorwasm`).
    * Il `App` componente, che è il componente radice dell'app, viene specificato come elemento `app` Dom per il `Add` metodo.
    * I servizi possono essere configurati con il `ConfigureServices` metodo nel generatore host (ad `builder.Services.AddSingleton<IMyDependency, MyDependency>();`esempio,).
    * La configurazione può essere fornita tramite il generatore host`builder.Configuration`().

* *Startup.cs* (Blazor Server) &ndash; contiene la logica di avvio dell'app. La `Startup` classe definisce due metodi:

  * `ConfigureServices`&ndash; Configura i servizi [di inserimento delle dipendenze](xref:fundamentals/dependency-injection) dell'app. Nelle Blazor app Server i servizi vengono aggiunti <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>chiamando e il `WeatherForecastService` viene aggiunto al contenitore del servizio per l'uso da parte del componente `FetchData` di esempio.
  * `Configure`&ndash; Configura la pipeline di gestione delle richieste dell'app:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>viene chiamato per configurare un endpoint per la connessione in tempo reale con il browser. La connessione viene creata con [SignalR](xref:signalr/introduction), che è un Framework per l'aggiunta di funzionalità Web in tempo reale alle app.
    * [MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) viene chiamato per configurare la pagina radice dell'app (*pages/_Host. cshtml*) e abilitare la navigazione.

* *wwwroot/index.html* (Blazor webassembly) &ndash; la pagina radice dell'app implementata come pagina HTML:
  * Quando viene inizialmente richiesta una pagina dell'app, il rendering della pagina viene eseguito e restituito nella risposta.
  * La pagina specifica la posizione in `App` cui viene eseguito il rendering del componente radice. Il `App` componente (*app. Razor*) viene specificato come elemento `app` Dom al `AddComponent` metodo in `Startup.Configure`.
  * Viene `_framework/blazor.webassembly.js` caricato il file JavaScript, che:
    * Scarica il Runtime .NET, l'app e le dipendenze dell'app.
    * Inizializza il runtime per l'esecuzione dell'app.

* *App. Razor* &ndash; il componente radice dell'app che imposta il routing lato client usando il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente. Il `Router` componente intercetta la navigazione del browser ed esegue il rendering della pagina che corrisponde all'indirizzo richiesto.

* La cartella &ndash; *pages* contiene i componenti e le pagine instradabili (*Razor*) che Blazor costituiscono l'app e Razor la pagina radice Blazor di un'app Server. La route per ogni pagina viene specificata utilizzando la [`@page`](xref:mvc/views/razor#page) direttiva. Il modello include quanto segue:
  * *_Host. cshtml* (Blazor Server) &ndash; pagina radice dell'app implementata come Razor pagina:
    * Quando viene inizialmente richiesta una pagina dell'app, il rendering della pagina viene eseguito e restituito nella risposta.
    * Viene `_framework/blazor.server.js` caricato il file JavaScript, che configura la connessione in tempo SignalR reale tra il browser e il server.
    * Nella pagina host viene specificata la posizione `App` in cui viene eseguito il rendering del componente radice (*app. Razor*).
  * `Counter`(*Counter. Razor*) &ndash; implementa la pagina del contatore.
  * `Error`(*Error. Razor*, Blazor solo app Server) &ndash; Sottoposto a rendering quando si verifica un'eccezione non gestita nell'app.
  * `FetchData`(*FetchData. Razor*) &ndash; implementa la pagina Recupera dati.
  * `Index`(*Index. Razor*) &ndash; implementa la Home page.

* *Shared* La cartella &ndash; condivisa contiene altri componenti dell'interfaccia utente (*Razor*) usati dall'app:
  * `MainLayout`(*MainLayout. Razor*) &ndash; il componente del layout dell'app.
  * `NavMenu`(*NavMenu. Razor*) &ndash; implementa la navigazione nell'intestazione laterale. Include il [componente NavLink](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), che esegue il rendering dei collegamenti di Razor navigazione ad altri componenti. Il `NavLink` componente indica automaticamente uno stato selezionato quando viene caricato il componente, che consente all'utente di comprendere il componente attualmente visualizzato.

* *_Imports. Razor* &ndash; include direttive comuni Razor da includere nei componenti dell'app (*Razor*), ad esempio le [`@using`](xref:mvc/views/razor#using) direttive per gli spazi dei nomi.

* *Data* La cartella dataBlazor (server &ndash; ) contiene `WeatherForecast` la classe e l'implementazione `WeatherForecastService` di che forniscono dati meteorologici di esempio al `FetchData` componente dell'app.

* *wwwroot* &ndash; la cartella [radice Web](xref:fundamentals/index#web-root) per l'app contenente le risorse statiche pubbliche dell'app.

* impostazioni di configurazione di *appSettings. JSON* (Blazor Server) &ndash; per l'app.
