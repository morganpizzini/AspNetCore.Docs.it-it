---
title: BlazorConfigurazione del modello di hosting ASP.NET Core
author: guardrex
description: Informazioni sugli scenari aggiuntivi per la Blazor configurazione del modello di hosting di ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: b28e4e43b88fcf8eab9e8959142cca21223c57ff
ms.sourcegitcommit: e216e8f4afa21215dc38124c28d5ee19f5ed7b1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86239634"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="7ac88-103">BlazorConfigurazione del modello di hosting ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ac88-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="7ac88-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7ac88-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7ac88-105">Questo articolo illustra la configurazione del modello di hosting.</span><span class="sxs-lookup"><span data-stu-id="7ac88-105">This article covers hosting model configuration.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalR<span data-ttu-id="7ac88-106">negoziazione tra le origini per l'autenticazione</span><span class="sxs-lookup"><span data-stu-id="7ac88-106"> cross-origin negotiation for authentication</span></span>

<span data-ttu-id="7ac88-107">*Questa sezione si applica a Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="7ac88-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="7ac88-108">Per configurare SignalR il client sottostante per l'invio di credenziali, ad esempio cookie o intestazioni di autenticazione http:</span><span class="sxs-lookup"><span data-stu-id="7ac88-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="7ac88-109">Usare <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> per impostare <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> le richieste tra origini [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="7ac88-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="7ac88-110">Assegnare <xref:System.Net.Http.HttpMessageHandler> a l' <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opzione:</span><span class="sxs-lookup"><span data-stu-id="7ac88-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="7ac88-111">Per altre informazioni, vedere <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="7ac88-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="7ac88-112">Riflette lo stato di connessione nell'interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="7ac88-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="7ac88-113">*Questa sezione si applica a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="7ac88-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="7ac88-114">Quando il client rileva che la connessione è stata persa, viene visualizzata un'interfaccia utente predefinita quando il client tenta di riconnettersi.</span><span class="sxs-lookup"><span data-stu-id="7ac88-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="7ac88-115">Se la riconnessione non riesce, all'utente viene offerta l'opzione per riprovare.</span><span class="sxs-lookup"><span data-stu-id="7ac88-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="7ac88-116">Per personalizzare l'interfaccia utente, definire un elemento con un valore `id` di `components-reconnect-modal` nell'oggetto `<body>` della `_Host.cshtml` Razor pagina:</span><span class="sxs-lookup"><span data-stu-id="7ac88-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="7ac88-117">Aggiungere quanto segue al foglio di stile dell'app ( `wwwroot/css/app.css` o `wwwroot/css/site.css` ):</span><span class="sxs-lookup"><span data-stu-id="7ac88-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="7ac88-118">Nella tabella seguente vengono descritte le classi CSS applicate all' `components-reconnect-modal` elemento.</span><span class="sxs-lookup"><span data-stu-id="7ac88-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="7ac88-119">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="7ac88-119">CSS class</span></span>                       | <span data-ttu-id="7ac88-120">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="7ac88-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="7ac88-121">Connessione persa.</span><span class="sxs-lookup"><span data-stu-id="7ac88-121">A lost connection.</span></span> <span data-ttu-id="7ac88-122">È in corso un tentativo di riconnessione del client.</span><span class="sxs-lookup"><span data-stu-id="7ac88-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="7ac88-123">Mostra il modale.</span><span class="sxs-lookup"><span data-stu-id="7ac88-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="7ac88-124">Viene ristabilita una connessione attiva al server.</span><span class="sxs-lookup"><span data-stu-id="7ac88-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="7ac88-125">Nascondere il modale.</span><span class="sxs-lookup"><span data-stu-id="7ac88-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="7ac88-126">Riconnessione non riuscita. probabilmente a causa di un errore di rete.</span><span class="sxs-lookup"><span data-stu-id="7ac88-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="7ac88-127">Per tentare la riconnessione, chiamare `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="7ac88-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="7ac88-128">Riconnessione rifiutata.</span><span class="sxs-lookup"><span data-stu-id="7ac88-128">Reconnection rejected.</span></span> <span data-ttu-id="7ac88-129">Il server è stato raggiunto ma ha rifiutato la connessione e lo stato dell'utente nel server è andato perso.</span><span class="sxs-lookup"><span data-stu-id="7ac88-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="7ac88-130">Per ricaricare l'app, chiamare `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="7ac88-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="7ac88-131">Questo stato di connessione può verificarsi nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="7ac88-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="7ac88-132">Si verifica un arresto anomalo del circuito sul lato server.</span><span class="sxs-lookup"><span data-stu-id="7ac88-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="7ac88-133">Il client viene disconnesso abbastanza a lungo da consentire al server di eliminare lo stato dell'utente.</span><span class="sxs-lookup"><span data-stu-id="7ac88-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="7ac88-134">Le istanze dei componenti con cui l'utente interagisce sono state eliminate.</span><span class="sxs-lookup"><span data-stu-id="7ac88-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="7ac88-135">Il server viene riavviato oppure il processo di lavoro dell'app viene riciclato.</span><span class="sxs-lookup"><span data-stu-id="7ac88-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="7ac88-136">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="7ac88-136">Render mode</span></span>

<span data-ttu-id="7ac88-137">*Questa sezione si applica a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="7ac88-137">*This section applies to Blazor Server.*</span></span>

Blazor Server<span data-ttu-id="7ac88-138">per impostazione predefinita, le app sono configurate per eseguire il prerendering dell'interfaccia utente nel server prima che venga stabilita la connessione client al server.</span><span class="sxs-lookup"><span data-stu-id="7ac88-138"> apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="7ac88-139">Questa impostazione è configurata nella `_Host.cshtml` Razor pagina:</span><span class="sxs-lookup"><span data-stu-id="7ac88-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="7ac88-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>Configura se il componente:</span><span class="sxs-lookup"><span data-stu-id="7ac88-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="7ac88-141">Viene preeseguito nella pagina.</span><span class="sxs-lookup"><span data-stu-id="7ac88-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="7ac88-142">Viene sottoposto a rendering come HTML statico nella pagina o se include le informazioni necessarie per il bootstrap di un' Blazor app dall'agente utente.</span><span class="sxs-lookup"><span data-stu-id="7ac88-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="7ac88-143">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="7ac88-143">Render mode</span></span> | <span data-ttu-id="7ac88-144">Descrizione</span><span class="sxs-lookup"><span data-stu-id="7ac88-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="7ac88-145">Esegue il rendering del componente in HTML statico e include un marcatore per un' Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="7ac88-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="7ac88-146">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="7ac88-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="7ac88-147">Esegue il rendering di un marcatore per un' Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="7ac88-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="7ac88-148">L'output del componente non è incluso.</span><span class="sxs-lookup"><span data-stu-id="7ac88-148">Output from the component isn't included.</span></span> <span data-ttu-id="7ac88-149">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="7ac88-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="7ac88-150">Esegue il rendering del componente in HTML statico.</span><span class="sxs-lookup"><span data-stu-id="7ac88-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="7ac88-151">Il rendering dei componenti server da una pagina HTML statica non è supportato.</span><span class="sxs-lookup"><span data-stu-id="7ac88-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="7ac88-152">Configurare il SignalR client per le Blazor Server app</span><span class="sxs-lookup"><span data-stu-id="7ac88-152">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="7ac88-153">*Questa sezione si applica a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="7ac88-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="7ac88-154">Configurare il SignalR client usato dalle Blazor Server app nel `Pages/_Host.cshtml` file.</span><span class="sxs-lookup"><span data-stu-id="7ac88-154">Configure the SignalR client used by Blazor Server apps in the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="7ac88-155">Inserire uno script che chiami `Blazor.start` dopo lo `_framework/blazor.server.js` script e all'interno del `</body>` tag.</span><span class="sxs-lookup"><span data-stu-id="7ac88-155">Place a script that calls `Blazor.start` after the `_framework/blazor.server.js` script and inside the `</body>` tag.</span></span>

### <a name="logging"></a><span data-ttu-id="7ac88-156">Registrazione</span><span class="sxs-lookup"><span data-stu-id="7ac88-156">Logging</span></span>

<span data-ttu-id="7ac88-157">Per configurare la SignalR registrazione del client:</span><span class="sxs-lookup"><span data-stu-id="7ac88-157">To configure SignalR client logging:</span></span>

* <span data-ttu-id="7ac88-158">Aggiungere un `autostart="false"` attributo al `<script>` tag per lo `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="7ac88-158">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="7ac88-159">Passare un oggetto di configurazione ( `configureSignalR` ) che chiama `configureLogging` con il livello di registrazione nel generatore client.</span><span class="sxs-lookup"><span data-stu-id="7ac88-159">Pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder.</span></span>

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

<span data-ttu-id="7ac88-160">Nell'esempio precedente, `information` è equivalente a un livello di log di <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="7ac88-160">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="7ac88-161">Modificare il gestore di riconnessione</span><span class="sxs-lookup"><span data-stu-id="7ac88-161">Modify the reconnection handler</span></span>

<span data-ttu-id="7ac88-162">Gli eventi di connessione del circuito del gestore di riconnessione possono essere modificati per comportamenti personalizzati, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7ac88-162">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="7ac88-163">Per inviare una notifica all'utente se la connessione viene eliminata.</span><span class="sxs-lookup"><span data-stu-id="7ac88-163">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="7ac88-164">Per eseguire la registrazione (dal client) quando si connette un circuito.</span><span class="sxs-lookup"><span data-stu-id="7ac88-164">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="7ac88-165">Per modificare gli eventi di connessione:</span><span class="sxs-lookup"><span data-stu-id="7ac88-165">To modify the connection events:</span></span>

* <span data-ttu-id="7ac88-166">Aggiungere un `autostart="false"` attributo al `<script>` tag per lo `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="7ac88-166">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="7ac88-167">Registra i callback per le modifiche della connessione per le connessioni eliminate ( `onConnectionDown` ) e le connessioni stabilite/ristabilite ( `onConnectionUp` ).</span><span class="sxs-lookup"><span data-stu-id="7ac88-167">Register callbacks for connection changes for dropped connections (`onConnectionDown`) and established/re-established connections (`onConnectionUp`).</span></span> <span data-ttu-id="7ac88-168">**Entrambi** `onConnectionDown` `onConnectionUp`è necessario specificare e.</span><span class="sxs-lookup"><span data-stu-id="7ac88-168">**Both** `onConnectionDown` and `onConnectionUp` must be specified.</span></span>

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="7ac88-169">Modificare il numero di tentativi di riconnessione e l'intervallo</span><span class="sxs-lookup"><span data-stu-id="7ac88-169">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="7ac88-170">Per modificare il numero di tentativi di riconnessione e l'intervallo:</span><span class="sxs-lookup"><span data-stu-id="7ac88-170">To adjust the reconnection retry count and interval:</span></span>

* <span data-ttu-id="7ac88-171">Aggiungere un `autostart="false"` attributo al `<script>` tag per lo `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="7ac88-171">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="7ac88-172">Impostare il numero di tentativi ( `maxRetries` ) e il periodo di tempo in millisecondi consentiti per ogni nuovo tentativo ( `retryIntervalMilliseconds` ).</span><span class="sxs-lookup"><span data-stu-id="7ac88-172">Set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`).</span></span>

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
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

### <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="7ac88-173">Nascondi o Sostituisci la visualizzazione di riconnessione</span><span class="sxs-lookup"><span data-stu-id="7ac88-173">Hide or replace the reconnection display</span></span>

<span data-ttu-id="7ac88-174">Per nascondere la visualizzazione della riconnessione:</span><span class="sxs-lookup"><span data-stu-id="7ac88-174">To hide the reconnection display:</span></span>

* <span data-ttu-id="7ac88-175">Aggiungere un `autostart="false"` attributo al `<script>` tag per lo `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="7ac88-175">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="7ac88-176">Impostare il gestore di riconnessione `_reconnectionDisplay` su un oggetto vuoto ( `{}` o `new Object()` ).</span><span class="sxs-lookup"><span data-stu-id="7ac88-176">Set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`).</span></span>

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });
    </script>
</body>
```

<span data-ttu-id="7ac88-177">Per sostituire la visualizzazione della riconnessione, impostare `_reconnectionDisplay` nell'esempio precedente sull'elemento per la visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="7ac88-177">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="7ac88-178">Il segnaposto `{ELEMENT ID}` è l'ID dell'elemento HTML da visualizzare.</span><span class="sxs-lookup"><span data-stu-id="7ac88-178">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7ac88-179">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="7ac88-179">Additional resources</span></span>

* <xref:fundamentals/logging/index>
