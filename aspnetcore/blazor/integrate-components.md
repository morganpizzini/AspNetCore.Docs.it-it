---
title: Integrare ASP.NET Core componenti Razor in app Razor Pages e MVC
author: guardrex
description: Informazioni sugli scenari di data binding per i componenti e gli Blazor elementi DOM nelle app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: 4e2103b7e8b65478808093d7a31e8cfe29b04984
ms.sourcegitcommit: f9a5069577e8f7c53f8bcec9e13e117950f4f033
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82558911"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>Integrare ASP.NET Core componenti Razor in app Razor Pages e MVC

Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

I componenti Razor possono essere integrati nelle app Razor Pages e MVC. Quando viene eseguito il rendering della pagina o della vista, è possibile eseguire il rendering dei componenti contemporaneamente.

Dopo aver [preparato l'app](#prepare-the-app), usare le istruzioni riportate nelle sezioni seguenti a seconda dei requisiti dell'app:

* Componenti &ndash; instradabili per i componenti che sono direttamente instradabili dalle richieste degli utenti. Seguire queste linee guida quando i visitatori devono essere in grado di effettuare una richiesta HTTP nel browser per un componente [`@page`](xref:mvc/views/razor#page) con una direttiva.
  * [Usare componenti instradabili in un'app Razor Pages](#use-routable-components-in-a-razor-pages-app)
  * [Usare componenti instradabili in un'app MVC](#use-routable-components-in-an-mvc-app)
* [Esegue il rendering dei componenti da una pagina o da una vista](#render-components-from-a-page-or-view) &ndash; per i componenti che non sono direttamente instradabili dalle richieste degli utenti. Seguire queste linee guida quando l'app incorpora i componenti in pagine e visualizzazioni esistenti con l' [Helper tag dei componenti](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

## <a name="prepare-the-app"></a>Preparare l'app

Un'app Razor Pages o MVC esistente può integrare i componenti Razor in pagine e visualizzazioni:

1. Nel file di layout dell'app (*_Layout. cshtml*):

   * Aggiungere il tag `<base>` seguente all' `<head>` elemento:

     ```html
     <base href="~/" />
     ```

     Il `href` valore (il *percorso di base dell'app*) nell'esempio precedente presuppone che l'app si trovi nel percorso URL radice (`/`). Se l'app è un'applicazione secondaria, seguire le istruzioni nella sezione *percorso di base* dell'applicazione dell' <xref:host-and-deploy/blazor/index#app-base-path> articolo.

     Il file *_Layout. cshtml* si trova nella cartella *pages/Shared* in un'app Razor Pages o in una cartella *Views/Shared* in un'app MVC.

   * Aggiungere un `<script>` tag per lo script *blazer. Server. js* immediatamente prima del tag di `</body>` chiusura:

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     Il Framework aggiunge lo script *blazer. Server. js* all'app. Non è necessario aggiungere manualmente lo script all'app.

1. Aggiungere un file *_Imports. Razor* alla cartella radice del progetto con il contenuto seguente (modificare l'ultimo spazio dei nomi, `MyAppNamespace`, nello spazio dei nomi dell'app):

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

1. In `Startup.ConfigureServices`registrare il servizio Server Blazer:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. In `Startup.Configure`aggiungere l'endpoint dell'hub blazer a `app.UseEndpoints`:

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Integrare i componenti in qualsiasi pagina o visualizzazione. Per ulteriori informazioni, vedere la sezione [rendering dei componenti da una pagina o vista](#render-components-from-a-page-or-view) .

## <a name="use-routable-components-in-a-razor-pages-app"></a>Usare componenti instradabili in un'app Razor Pages

*Questa sezione riguarda l'aggiunta di componenti che sono direttamente instradabili dalle richieste degli utenti.*

Per supportare componenti Razor instradabili nelle app Razor Pages:

1. Seguire le istruzioni nella sezione [preparare l'app](#prepare-the-app) .

1. Aggiungere un file *app. Razor* alla radice del progetto con il contenuto seguente:

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

1. Aggiungere un file *_Host. cshtml* alla cartella *pages* con il contenuto seguente:

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   I componenti usano il file Shared *_Layout. cshtml* per il layout.

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Configura se il `App` componente:

   * Viene preeseguito nella pagina.
   * Viene visualizzato come HTML statico nella pagina o se include le informazioni necessarie per eseguire il bootstrap di un'app Blazer dall'agente utente.

   | Modalità di rendering | Descrizione |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Esegue il rendering `App` del componente in HTML statico e include un marcatore per un'app del server blazer. Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un'app blazer. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Esegue il rendering di un marcatore per un'app del server blazer. L' `App` output del componente non è incluso. Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un'app blazer. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Esegue il rendering `App` del componente in HTML statico. |

   Per ulteriori informazioni sull'helper tag dei componenti, vedere <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

1. Aggiungere una route con priorità bassa per la pagina *_Host. cshtml* alla configurazione dell'endpoint `Startup.Configure`in:

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

Per ulteriori informazioni sugli spazi dei nomi, vedere la sezione relativa agli [spazi dei nomi dei componenti](#component-namespaces) .

## <a name="use-routable-components-in-an-mvc-app"></a>Usare componenti instradabili in un'app MVC

*Questa sezione riguarda l'aggiunta di componenti che sono direttamente instradabili dalle richieste degli utenti.*

Per supportare i componenti Razor instradabili nelle app MVC:

1. Seguire le istruzioni nella sezione [preparare l'app](#prepare-the-app) .

1. Aggiungere un file *app. Razor* alla radice del progetto con il contenuto seguente:

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

1. Aggiungere un file *_Host. cshtml* alla cartella *views/Home* con il contenuto seguente:

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   I componenti usano il file Shared *_Layout. cshtml* per il layout.
   
   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>Configura se il `App` componente:

   * Viene preeseguito nella pagina.
   * Viene visualizzato come HTML statico nella pagina o se include le informazioni necessarie per eseguire il bootstrap di un'app Blazer dall'agente utente.

   | Modalità di rendering | Descrizione |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Esegue il rendering `App` del componente in HTML statico e include un marcatore Blazor per un'app Server. Quando l'agente utente viene avviato, questo marcatore viene usato per il Blazor bootstrap di un'app. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Esegue il rendering di un marcatore per un' Blazor app Server. L' `App` output del componente non è incluso. Quando l'agente utente viene avviato, questo marcatore viene usato per il Blazor bootstrap di un'app. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Esegue il rendering `App` del componente in HTML statico. |

   Per ulteriori informazioni sull'helper tag dei componenti, vedere <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

1. Aggiungere un'azione al controller Home:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. Aggiungere una route con priorità bassa per l'azione del controller che restituisce la vista *_Host. cshtml* alla configurazione dell'endpoint `Startup.Configure`in:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Creare una cartella *pages* e aggiungere componenti instradabili all'app. Ad esempio:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Per ulteriori informazioni sugli spazi dei nomi, vedere la sezione relativa agli [spazi dei nomi dei componenti](#component-namespaces) .

## <a name="render-components-from-a-page-or-view"></a>Eseguire il rendering dei componenti da una pagina o da una vista

*Questa sezione riguarda l'aggiunta di componenti a pagine o viste, in cui i componenti non sono direttamente instradabili dalle richieste degli utenti.*

Per eseguire il rendering di un componente da una pagina o da una vista, usare l' [Helper Tag Component](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

### <a name="render-stateful-interactive-components"></a>Eseguire il rendering di componenti interattivi con stato

I componenti interattivi con stato possono essere aggiunti a una pagina o a una visualizzazione Razor.

Quando viene eseguito il rendering della pagina o della visualizzazione:

* Il componente viene preeseguito con la pagina o la visualizzazione.
* Lo stato iniziale del componente utilizzato per il prerendering viene perso.
* Quando viene stabilita la connessione, SignalR viene creato il nuovo stato del componente.

La pagina Razor seguente esegue il rendering `Counter` di un componente:

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Per altre informazioni, vedere <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

### <a name="render-noninteractive-components"></a>Eseguire il rendering di componenti non interattivi

Nella pagina Razor seguente il `Counter` componente viene sottoposto a rendering statico con un valore iniziale specificato usando un modulo. Poiché il componente viene sottoposto a rendering statico, il componente non è interattivo:

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Per altre informazioni, vedere <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

## <a name="component-namespaces"></a>Spazi dei nomi dei componenti

Quando si usa una cartella personalizzata per archiviare i componenti dell'app, aggiungere lo spazio dei nomi che rappresenta la cartella alla pagina/visualizzazione o al file *_ViewImports. cshtml* . Nell'esempio seguente:

* Passare `MyAppNamespace` allo spazio dei nomi dell'app.
* Se una cartella denominata *Components* non viene utilizzata per conservare i componenti `Components` , passare alla cartella in cui si trovano i componenti.

```cshtml
@using MyAppNamespace.Components
```

Il file *_ViewImports. cshtml* si trova nella cartella *pagine* di un'app Razor Pages o nella cartella *views* di un'app MVC.

Per altre informazioni, vedere <xref:blazor/components#import-components>.
