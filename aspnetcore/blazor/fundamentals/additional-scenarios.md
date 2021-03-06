---
title: BlazorConfigurazione del modello di hosting ASP.NET Core
author: guardrex
description: Informazioni sugli scenari aggiuntivi per la Blazor configurazione del modello di hosting di ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: b7fc3710fe5ad1efba907edf98f590a42e2a83ae
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "97485875"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a>BlazorConfigurazione del modello di hosting ASP.NET Core

Di [Daniel Roth](https://github.com/danroth27), [Marin Buck](https://github.com/MackinnonBuck)e [Luke Latham](https://github.com/guardrex)

Questo articolo illustra la configurazione del modello di hosting.

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a>SignalR negoziazione tra le origini per l'autenticazione

*Questa sezione si applica a Blazor WebAssembly .*

Per configurare SignalR il client sottostante per l'invio di credenziali, ad esempio le cookie intestazioni s o http Authentication:

* Usare <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> per impostare <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> le richieste tra origini [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :

  ```csharp
  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* Assegnare <xref:System.Net.Http.HttpMessageHandler> a l' <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opzione:

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

Per altre informazioni, vedere <xref:signalr/configuration#configure-additional-options>.

## <a name="reflect-the-connection-state-in-the-ui"></a>Riflette lo stato di connessione nell'interfaccia utente

*Questa sezione si applica a Blazor Server .*

Quando il client rileva che la connessione è stata persa, viene visualizzata un'interfaccia utente predefinita quando il client tenta di riconnettersi. Se la riconnessione non riesce, all'utente viene offerta l'opzione per riprovare.

Per personalizzare l'interfaccia utente, definire un elemento con un valore `id` di `components-reconnect-modal` nell'oggetto `<body>` della `_Host.cshtml` Razor pagina:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Aggiungere quanto segue al foglio di stile dell'app ( `wwwroot/css/app.css` o `wwwroot/css/site.css` ):

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

Nella tabella seguente vengono descritte le classi CSS applicate all' `components-reconnect-modal` elemento.

| Classe CSS                       | Indica&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Connessione persa. È in corso un tentativo di riconnessione del client. Mostra il modale. |
| `components-reconnect-hide`     | Viene ristabilita una connessione attiva al server. Nascondere il modale. |
| `components-reconnect-failed`   | Riconnessione non riuscita. probabilmente a causa di un errore di rete. Per tentare la riconnessione, chiamare `window.Blazor.reconnect()` . |
| `components-reconnect-rejected` | Riconnessione rifiutata. Il server è stato raggiunto ma ha rifiutato la connessione e lo stato dell'utente nel server è andato perso. Per ricaricare l'app, chiamare `location.reload()` . Questo stato di connessione può verificarsi nei casi seguenti:<ul><li>Si verifica un arresto anomalo del circuito sul lato server.</li><li>Il client viene disconnesso abbastanza a lungo da consentire al server di eliminare lo stato dell'utente. Le istanze dei componenti con cui l'utente interagisce sono state eliminate.</li><li>Il server viene riavviato oppure il processo di lavoro dell'app viene riciclato.</li></ul> |

## <a name="render-mode"></a>Modalità di rendering

::: moniker range=">= aspnetcore-5.0"

*Questa sezione si applica a Hosted Blazor WebAssembly e Blazor Server .*

Blazor per impostazione predefinita, le app sono configurate per eseguire il prerendering dell'interfaccia utente nel server. Per altre informazioni, vedere <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

*Questa sezione si applica a Blazor Server .*

Blazor Server per impostazione predefinita, le app sono configurate per eseguire il prerendering dell'interfaccia utente nel server prima che venga stabilita la connessione client al server. Per altre informazioni, vedere <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

::: moniker-end

## <a name="initialize-the-no-locblazor-circuit"></a>Inizializzare il Blazor circuito

*Questa sezione si applica a Blazor Server .*

Configurare l'avvio manuale del Blazor Server [ SignalR circuito](xref:blazor/hosting-models#circuits) di un'app nel `Pages/_Host.cshtml` file:

* Aggiungere un `autostart="false"` attributo al `<script>` tag per lo `blazor.server.js` script.
* Inserire uno script che chiama `Blazor.start` dopo il `blazor.server.js` tag dello script e all'interno del `</body>` tag di chiusura.

Quando `autostart` è disabilitato, qualsiasi aspetto dell'app che non dipende dal circuito funziona normalmente. Il routing lato client, ad esempio, è operativo. Tuttavia, qualsiasi aspetto che dipende dal circuito non è operativo fino a quando non `Blazor.start` viene chiamato il metodo. Il comportamento dell'app è imprevedibile senza un circuito stabilito. Ad esempio, i metodi dei componenti non vengono eseguiti mentre il circuito è disconnesso.

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a>Inizializza Blazor quando il documento è pronto

Per inizializzare l' Blazor app quando il documento è pronto:

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        Blazor.start();
      });
    </script>
</body>
```

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a>Concatenare a `Promise` che risulta da un avvio manuale

Per eseguire attività aggiuntive, ad esempio l'inizializzazione dell'interoperabilità JS, usare `then` per concatenare a `Promise` che risulta da un'app manuale di Blazor avvio:

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start().then(function () {
        ...
      });
    </script>
</body>
```

### <a name="configure-the-no-locsignalr-client"></a>Configurare il SignalR client

#### <a name="logging"></a>Registrazione

Per configurare la SignalR registrazione client, passare un oggetto di configurazione ( `configureSignalR` ) che chiama `configureLogging` con il livello di registrazione nel generatore client:

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

Nell'esempio precedente, `information` è equivalente a un livello di log di <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .

### <a name="modify-the-reconnection-handler"></a>Modificare il gestore di riconnessione

Gli eventi di connessione del circuito del gestore di riconnessione possono essere modificati per comportamenti personalizzati, ad esempio:

* Per inviare una notifica all'utente se la connessione viene eliminata.
* Per eseguire la registrazione (dal client) quando si connette un circuito.

Per modificare gli eventi di connessione, registrare i callback per le seguenti modifiche della connessione:

* Connessioni eliminate utilizzare `onConnectionDown` .
* Le connessioni stabilite/ristabilite usano `onConnectionUp` .

**Entrambi** `onConnectionDown` `onConnectionUp` è necessario specificare e:

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error),
          onConnectionUp: () => console.log("Up, up, and away!")
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a>Modificare il numero di tentativi di riconnessione e l'intervallo

Per modificare il numero di tentativi di riconnessione e l'intervallo, impostare il numero di tentativi ( `maxRetries` ) e il periodo di tempo in millisecondi consentiti per ogni nuovo tentativo ( `retryIntervalMilliseconds` ):

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

## <a name="hide-or-replace-the-reconnection-display"></a>Nascondi o Sostituisci la visualizzazione di riconnessione

Per nascondere la visualizzazione della riconnessione, impostare il gestore di riconnessione `_reconnectionDisplay` su un oggetto vuoto ( `{}` o `new Object()` ):

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });

      Blazor.start();
    </script>
</body>
```

Per sostituire la visualizzazione della riconnessione, impostare `_reconnectionDisplay` nell'esempio precedente sull'elemento per la visualizzazione:

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

Il segnaposto `{ELEMENT ID}` è l'ID dell'elemento HTML da visualizzare.

::: moniker range=">= aspnetcore-5.0"

Personalizzare il ritardo prima che la visualizzazione della riconnessione venga visualizzata impostando la `transition-delay` proprietà nel CSS ( `wwwroot/css/site.css` ) dell'app per l'elemento modale. Nell'esempio seguente viene impostato il ritardo di transizione da 500 ms (impostazione predefinita) a 1.000 ms (1 secondo):

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a>Disconnettere il Blazor circuito dal client

Per impostazione predefinita, un Blazor circuito viene disconnesso quando viene attivato l' [ `unload` evento della pagina](https://developer.mozilla.org/docs/Web/API/Window/unload_event) . Per disconnettere il circuito per altri scenari nel client, richiamare `Blazor.disconnect` nel gestore eventi appropriato. Nell'esempio seguente il circuito viene disconnesso quando la pagina è nascosta ([ `pagehide` evento](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

<!-- HOLD for reactivation at 5x

## Influence HTML `<head>` tag elements

*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*

When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.

The following characteristics apply:

* Server-side prerendering is supported.
* The `Value` parameter is the only valid parameter for the `Title` component.
* HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.
* For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.
* If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component. Two `Meta` or `Link` components can't refer to the same rendered HTML tag.
* Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.
* When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.

When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered. The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

## <a name="static-files"></a>File statici

*Questa sezione si applica a Blazor Server .*

Per creare altri mapping di file con <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> o configurare altri <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> , usare **uno** degli approcci seguenti. Negli esempi seguenti, il `{EXTENSION}` segnaposto è l'estensione di file e il `{CONTENT TYPE}` segnaposto è il tipo di contenuto.

* Configurare le opzioni tramite l' [inserimento di dipendenze](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` ( `Startup.cs` ) utilizzando <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> :

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  services.Configure<StaticFileOptions>(options =>
  {
      options.ContentTypeProvider = provider;
  });
  ```

  Poiché questo approccio configura lo stesso provider di file usato per gestire `blazor.server.js` , assicurarsi che la configurazione personalizzata non interferisca con il servizio `blazor.server.js` . Ad esempio, non rimuovere il mapping per i file JavaScript configurando il provider con `provider.Mappings.Remove(".js")` .

* Usare due chiamate a <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` ( `Startup.cs` ):
  * Configurare il provider di file personalizzato nella prima chiamata con <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> .
  * Il secondo middleware serve `blazor.server.js` , che usa la configurazione predefinita dei file statici fornita dal Blazor Framework.

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* È possibile evitare di interferire con `_framework/blazor.server.js` il servizio utilizzando <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> per eseguire un middleware di file statico personalizzato:

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:fundamentals/logging/index>
* [Blazor Server eventi di riconnessione e eventi del ciclo di vita del componente](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
