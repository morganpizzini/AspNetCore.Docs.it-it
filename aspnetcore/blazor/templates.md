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
# <a name="aspnet-core-opno-locblazor-templates"></a>ASP.NET Blazor modelli di base

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Il Blazor framework fornisce modelli per sviluppare Blazor app per ognuno dei modelli di hosting:The framework provides templates to develop apps for each of the hosting models:

* BlazorWebAssembly`blazorwasm`( )
* BlazorServer`blazorserver`( )

Per ulteriori Blazorinformazioni sui modelli <xref:blazor/hosting-models>di hosting, vedere .

Per istruzioni dettagliate sulla creazione Blazor di un'app <xref:blazor/get-started>da un modello, vedere .

Le opzioni del modello `--help` sono disponibili passando l'opzione al [comando dotnet new](/dotnet/core/tools/dotnet-new) CLI:

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="opno-locblazor-project-structure"></a>Blazorstruttura del progetto

I file e le Blazor cartelle seguenti Blazor costituiscono un'app generata da un modello:

* *Program.cs* &ndash; punto di ingresso dell'app che configura:

  * ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)
  * Host WebAssemblyBlazor ( &ndash; WebAssembly) Il codice in questo Blazor file è`blazorwasm`univoco per le app create dal modello WebAssembly ( ).
    * Il `App` componente, che è il componente radice dell'app, viene specificato come elemento `app` DOM per il `Add` metodo.
    * I servizi possono `ConfigureServices` essere configurati con il metodo `builder.Services.AddSingleton<IMyDependency, MyDependency>();`nel generatore host, ad esempio ).
    * La configurazione può essere fornita`builder.Configuration`tramite il generatore host ( ).

* *Startup.cs* Blazor ( &ndash; Server) contiene la logica di avvio dell'app. La `Startup` classe definisce due metodi:

  * `ConfigureServices`&ndash; Configura i servizi [di inserimento delle dipendenze (DI)](xref:fundamentals/dependency-injection) dell'app. Nelle Blazor app server i servizi <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>vengono `WeatherForecastService` aggiunti chiamando e quelli aggiunti al `FetchData` contenitore dei servizi per l'utilizzo da parte del componente di esempio.
  * `Configure`&ndash; Configura la pipeline di gestione delle richieste dell'app:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>viene chiamato per configurare un endpoint per la connessione in tempo reale con il browser. La connessione viene [SignalR](xref:signalr/introduction)creata con , che è un framework per l'aggiunta di funzionalità Web in tempo reale alle app.
    * [MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) viene chiamato per configurare la pagina radice dell'app (*Pages/_Host.cshtml*) e abilitare la navigazione.

* *wwwroot/index.html* Blazor ( WebAssembly) &ndash; La pagina radice dell'app implementata come pagina HTML:
  * Quando una pagina dell'app viene inizialmente richiesta, il rendering di questa pagina viene eseguito e restituito nella risposta.
  * La pagina specifica dove `App` viene eseguito il rendering del componente radice. Il `App` componente (*App.razor*) `app` viene specificato `AddComponent` come `Startup.Configure`elemento DOM per il metodo in .
  * Il `_framework/blazor.webassembly.js` file JavaScript viene caricato, che:
    * Scarica il runtime di .NET, l'app e le dipendenze dell'app.
    * Inizializza il runtime per eseguire l'app.

* *App.razor* &ndash; Il componente radice dell'app che configura <xref:Microsoft.AspNetCore.Components.Routing.Router> il routing sul lato client tramite il componente. Il `Router` componente intercetta la navigazione del browser ed esegue il rendering della pagina che corrisponde all'indirizzo richiesto.

* *Cartella* &ndash; Pagine Contiene i componenti/pagine instradabili ( Blazor *.razor*) che costituiscono l'app e la pagina Razor radice di un'app Blazor Server. La route per ogni pagina [`@page`](xref:mvc/views/razor#page) viene specificata utilizzando la direttiva. Il modello include quanto segue:
  * *_Host.cshtml* Blazor ( &ndash; Server) La pagina radice dell'app implementata come pagina Razor:
    * Quando una pagina dell'app viene inizialmente richiesta, il rendering di questa pagina viene eseguito e restituito nella risposta.
    * Il `_framework/blazor.server.js` file JavaScript viene caricato, che SignalR imposta la connessione in tempo reale tra il browser e il server.
    * La pagina Host specifica `App` dove viene eseguito il rendering del componente radice (*App.razor*).
  * `Counter`(*Counter.razor*) &ndash; Implementa la pagina Contatore.
  * `Error`(Solo*app Error.razor*, Blazor app server) &ndash; Viene eseguito il rendering quando si verifica un'eccezione non gestita nell'app.
  * `FetchData`(*FetchData.razor*) &ndash; Implementa la pagina dei dati Fetch.
  * `Index`(*Index.razor*) &ndash; Implementa la home page.

* *Cartella* &ndash; condivisa Contiene altri componenti dell'interfaccia utente (*.razor*) utilizzati dall'app:
  * `MainLayout`(*MainLayout.razor*) &ndash; Componente di layout dell'app.
  * `NavMenu`(*NavMenu.razor*) &ndash; Implementa la navigazione nella barra laterale. Include il componente<xref:Microsoft.AspNetCore.Components.Routing.NavLink> [NavLink](xref:blazor/routing#navlink-component) ( ), che esegue il rendering dei collegamenti di navigazione ad altri componenti Razor. Il `NavLink` componente indica automaticamente uno stato selezionato quando viene caricato il componente, che consente all'utente di comprendere quale componente è attualmente visualizzato.

* *_Imports.razor* &ndash; Include direttive Razor comuni da includere nei componenti dell'app (*.razor*), ad [`@using`](xref:mvc/views/razor#using) esempio le direttive per gli spazi dei nomi.

* *Cartella* datiBlazor (Server) &ndash; Contiene la `WeatherForecast` `WeatherForecastService` classe e l'implementazione di `FetchData` che forniscono dati meteo di esempio al componente dell'app.

* *wwwroot* &ndash; La cartella [Radice Web](xref:fundamentals/index#web-root) per l'app contenente le risorse statiche pubbliche dell'app.

* *appsettings.json* Blazor ( &ndash; Server) Impostazioni di configurazione per l'app.
