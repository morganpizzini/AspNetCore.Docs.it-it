---
title: Integra ASP.NET componenti di base di Razor in pagine Razor e app MVC
author: guardrex
description: Informazioni sugli scenari di associazione Blazor dati per i componenti e gli elementi DOM nelle app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: cf6056e0985d5433bddecac8dd183ca3f4c2af5b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218934"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>Integra ASP.NET componenti di base di Razor in pagine Razor e app MVC

Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

I componenti Razor possono essere integrati nelle pagine Razor e nelle app MVC. Quando viene eseguito il rendering della pagina o della vista, è possibile eseguire il prerendering dei componenti contemporaneamente.

## <a name="prepare-the-app-to-use-components-in-pages-and-views"></a>Preparare l'app per l'uso dei componenti in pagine e visualizzazioni

Un'app Razor Pages o MVC esistente può integrare i componenti Razor nelle pagine e nelle visualizzazioni:An existing Razor Pages or MVC app can integrate Razor components into pages and views:

1. Nel file di layout dell'app (*_Layout.cshtml*):

   * Aggiungere il `<base>` seguente `<head>` tag all'elemento:

     ```html
     <base href="~/" />
     ```

     Il `href` valore *(percorso di base dell'app*) nell'esempio precedente presuppone che`/`l'app si trovi nel percorso dell'URL radice ( ). Se l'app è un'applicazione secondaria, seguire le indicazioni <xref:host-and-deploy/blazor/index#app-base-path> nella sezione Percorso di *base* dell'app dell'articolo.

     Il file *_Layout.cshtml* si trova nella cartella *Pages/Shared* in un'app Razor Pages o nella cartella *Views/Shared* in un'app MVC.

   * Aggiungere `<script>` un tag per lo script *blazor.server.js* immediatamente prima del tag di chiusura: `</body>`

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     Il framework aggiunge lo script *blazor.server.js* all'app. Non è necessario aggiungere manualmente lo script all'app.

1. Aggiungere un file *_Imports.razor* alla cartella radice del progetto con il `MyAppNamespace`contenuto seguente (modificare l'ultimo spazio dei nomi, , , nello spazio dei nomi dell'app):

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. In `Startup.ConfigureServices`, Blazor registrare il servizio Server:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. In `Startup.Configure`, Blazor aggiungere l'endpoint Hub a `app.UseEndpoints`:

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Integrare i componenti in qualsiasi pagina o vista. Per ulteriori informazioni, vedere la sezione [Rendering di componenti da una pagina o vista.](#render-components-from-a-page-or-view)

## <a name="use-routable-components-in-a-razor-pages-app"></a>Usare componenti instradabili in un'app Razor Pages

*Questa sezione riguarda l'aggiunta di componenti instradabili direttamente dalle richieste dell'utente.*

Per supportare i componenti Razor instradabili nelle app Razor Pages:

1. Seguire le istruzioni nella sezione [Preparare l'app per l'uso dei componenti nelle pagine e nelle visualizzazioni.](#prepare-the-app-to-use-components-in-pages-and-views)

1. Aggiungere un file App.razor alla radice del progetto con il contenuto seguente:Add an *App.razor* file to the project root with the following content:

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. Aggiungere un file *_Host.cshtml* alla cartella *Pages* con il contenuto seguente:

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   I componenti utilizzano il file *condiviso _Layout.cshtml* per il layout.

1. Aggiungere una route con priorità bassa per la pagina `Startup.Configure` *_Host.cshtml* alla configurazione dell'endpoint in:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. Aggiungere componenti instradabili all'app. Ad esempio:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   Per altre informazioni sugli spazi dei nomi, vedere la sezione [Spazi dei nomi dei](#component-namespaces) componenti.

## <a name="use-routable-components-in-an-mvc-app"></a>Usare componenti instradabili in un'app MVCUse routable components in an MVC app

*Questa sezione riguarda l'aggiunta di componenti instradabili direttamente dalle richieste dell'utente.*

Per supportare i componenti Razor instradabili nelle app MVC:

1. Seguire le istruzioni nella sezione [Preparare l'app per l'uso dei componenti nelle pagine e nelle visualizzazioni.](#prepare-the-app-to-use-components-in-pages-and-views)

1. Aggiungere un file *App.razor* alla radice del progetto con il contenuto seguente:

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. Aggiungere un file *_Host.cshtml* alla cartella *Views/Home* con il contenuto seguente:

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   I componenti utilizzano il file *condiviso _Layout.cshtml* per il layout.

1. Aggiungere un'azione al controller Home:Add an action to the Home controller:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. Aggiungere una route con priorità bassa per l'azione del controller che `Startup.Configure`restituisce la visualizzazione *_Host.cshtml* alla configurazione dell'endpoint in:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Creare una cartella *Pages* e aggiungere componenti instradabili all'app. Ad esempio:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   Per altre informazioni sugli spazi dei nomi, vedere la sezione [Spazi dei nomi dei](#component-namespaces) componenti.

## <a name="component-namespaces"></a>Spazi dei nomi dei componentiComponent namespaces

Quando usi una cartella personalizzata per contenere i componenti dell'app, aggiungi lo spazio dei nomi che rappresenta la cartella alla pagina/visualizzazione o al file *_ViewImports.cshtml.* Nell'esempio seguente:

* Passare `MyAppNamespace` allo spazio dei nomi dell'app.
* Se una cartella denominata *Componenti* non viene `Components` utilizzata per contenere i componenti, passare alla cartella in cui risiedono i componenti.

```cshtml
@using MyAppNamespace.Components
```

Il file *_ViewImports.cshtml* si trova nella cartella *Pages* di un'app Razor Pages o nella cartella *Views* di un'app MVC.

Per altre informazioni, vedere <xref:blazor/components#import-components>.

## <a name="render-components-from-a-page-or-view"></a>Rendering di componenti da una pagina o una visualizzazione

*Questa sezione riguarda l'aggiunta di componenti a pagine o viste, in cui i componenti non sono instradabili direttamente dalle richieste degli utenti.*

Per eseguire il rendering di un componente da una pagina o da una vista, utilizzate [l'helper tag componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

Per altre informazioni su come viene eseguito `Component` il rendering dei componenti, sullo stato dei componenti e su Tag Helper, vedere gli articoli seguenti:For more information on how components are rendered, component state, and the Tag Helper, see the following articles:

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>
* <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>
