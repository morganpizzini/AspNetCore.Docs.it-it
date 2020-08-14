---
title: BlazorConfigurazione del modello di hosting ASP.NET Core
author: guardrex
description: Informazioni sugli scenari aggiuntivi per la Blazor configurazione del modello di hosting di ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2020
no-loc:
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
ms.openlocfilehash: 618e451f5cb8a4e8eaf355d398fdeb80190cf559
ms.sourcegitcommit: ec41ab354952b75557240923756a8c2ac79b49f8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88202716"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="91850-103">BlazorConfigurazione del modello di hosting ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="91850-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="91850-104">Di [Daniel Roth](https://github.com/danroth27), [Marin Buck](https://github.com/MackinnonBuck)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="91850-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="91850-105">Questo articolo illustra la configurazione del modello di hosting.</span><span class="sxs-lookup"><span data-stu-id="91850-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="91850-106">SignalR negoziazione tra le origini per l'autenticazione</span><span class="sxs-lookup"><span data-stu-id="91850-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="91850-107">*Questa sezione si applica a Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="91850-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="91850-108">Per configurare SignalR il client sottostante per l'invio di credenziali, ad esempio le cookie intestazioni s o http Authentication:</span><span class="sxs-lookup"><span data-stu-id="91850-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="91850-109">Usare <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> per impostare <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> le richieste tra origini [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="91850-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="91850-110">Assegnare <xref:System.Net.Http.HttpMessageHandler> a l' <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opzione:</span><span class="sxs-lookup"><span data-stu-id="91850-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="91850-111">Per altre informazioni, vedere <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="91850-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="91850-112">Riflette lo stato di connessione nell'interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="91850-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="91850-113">*Questa sezione si applica a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="91850-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="91850-114">Quando il client rileva che la connessione è stata persa, viene visualizzata un'interfaccia utente predefinita quando il client tenta di riconnettersi.</span><span class="sxs-lookup"><span data-stu-id="91850-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="91850-115">Se la riconnessione non riesce, all'utente viene offerta l'opzione per riprovare.</span><span class="sxs-lookup"><span data-stu-id="91850-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="91850-116">Per personalizzare l'interfaccia utente, definire un elemento con un valore `id` di `components-reconnect-modal` nell'oggetto `<body>` della `_Host.cshtml` Razor pagina:</span><span class="sxs-lookup"><span data-stu-id="91850-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="91850-117">Aggiungere quanto segue al foglio di stile dell'app ( `wwwroot/css/app.css` o `wwwroot/css/site.css` ):</span><span class="sxs-lookup"><span data-stu-id="91850-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="91850-118">Nella tabella seguente vengono descritte le classi CSS applicate all' `components-reconnect-modal` elemento.</span><span class="sxs-lookup"><span data-stu-id="91850-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="91850-119">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="91850-119">CSS class</span></span>                       | <span data-ttu-id="91850-120">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="91850-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="91850-121">Connessione persa.</span><span class="sxs-lookup"><span data-stu-id="91850-121">A lost connection.</span></span> <span data-ttu-id="91850-122">È in corso un tentativo di riconnessione del client.</span><span class="sxs-lookup"><span data-stu-id="91850-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="91850-123">Mostra il modale.</span><span class="sxs-lookup"><span data-stu-id="91850-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="91850-124">Viene ristabilita una connessione attiva al server.</span><span class="sxs-lookup"><span data-stu-id="91850-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="91850-125">Nascondere il modale.</span><span class="sxs-lookup"><span data-stu-id="91850-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="91850-126">Riconnessione non riuscita. probabilmente a causa di un errore di rete.</span><span class="sxs-lookup"><span data-stu-id="91850-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="91850-127">Per tentare la riconnessione, chiamare `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="91850-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="91850-128">Riconnessione rifiutata.</span><span class="sxs-lookup"><span data-stu-id="91850-128">Reconnection rejected.</span></span> <span data-ttu-id="91850-129">Il server è stato raggiunto ma ha rifiutato la connessione e lo stato dell'utente nel server è andato perso.</span><span class="sxs-lookup"><span data-stu-id="91850-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="91850-130">Per ricaricare l'app, chiamare `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="91850-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="91850-131">Questo stato di connessione può verificarsi nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="91850-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="91850-132">Si verifica un arresto anomalo del circuito sul lato server.</span><span class="sxs-lookup"><span data-stu-id="91850-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="91850-133">Il client viene disconnesso abbastanza a lungo da consentire al server di eliminare lo stato dell'utente.</span><span class="sxs-lookup"><span data-stu-id="91850-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="91850-134">Le istanze dei componenti con cui l'utente interagisce sono state eliminate.</span><span class="sxs-lookup"><span data-stu-id="91850-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="91850-135">Il server viene riavviato oppure il processo di lavoro dell'app viene riciclato.</span><span class="sxs-lookup"><span data-stu-id="91850-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="91850-136">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="91850-136">Render mode</span></span>

<span data-ttu-id="91850-137">*Questa sezione si applica a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="91850-137">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="91850-138">Blazor Server per impostazione predefinita, le app sono configurate per eseguire il prerendering dell'interfaccia utente nel server prima che venga stabilita la connessione client al server.</span><span class="sxs-lookup"><span data-stu-id="91850-138">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="91850-139">Questa impostazione è configurata nella `_Host.cshtml` Razor pagina:</span><span class="sxs-lookup"><span data-stu-id="91850-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="91850-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> Configura se il componente:</span><span class="sxs-lookup"><span data-stu-id="91850-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="91850-141">Viene preeseguito nella pagina.</span><span class="sxs-lookup"><span data-stu-id="91850-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="91850-142">Viene sottoposto a rendering come HTML statico nella pagina o se include le informazioni necessarie per il bootstrap di un' Blazor app dall'agente utente.</span><span class="sxs-lookup"><span data-stu-id="91850-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="91850-143">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="91850-143">Render mode</span></span> | <span data-ttu-id="91850-144">Descrizione</span><span class="sxs-lookup"><span data-stu-id="91850-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="91850-145">Esegue il rendering del componente in HTML statico e include un marcatore per un' Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="91850-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="91850-146">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="91850-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="91850-147">Esegue il rendering di un marcatore per un' Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="91850-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="91850-148">L'output del componente non è incluso.</span><span class="sxs-lookup"><span data-stu-id="91850-148">Output from the component isn't included.</span></span> <span data-ttu-id="91850-149">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="91850-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="91850-150">Esegue il rendering del componente in HTML statico.</span><span class="sxs-lookup"><span data-stu-id="91850-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="91850-151">Il rendering dei componenti server da una pagina HTML statica non è supportato.</span><span class="sxs-lookup"><span data-stu-id="91850-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-no-locsignalr-client-for-no-locblazor-server-apps"></a><span data-ttu-id="91850-152">Configurare il SignalR client per le Blazor Server app</span><span class="sxs-lookup"><span data-stu-id="91850-152">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="91850-153">*Questa sezione si applica a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="91850-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="91850-154">Configurare il SignalR client usato dalle Blazor Server app nel `Pages/_Host.cshtml` file.</span><span class="sxs-lookup"><span data-stu-id="91850-154">Configure the SignalR client used by Blazor Server apps in the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="91850-155">Inserire uno script che chiami `Blazor.start` dopo lo `_framework/blazor.server.js` script e all'interno del `</body>` tag.</span><span class="sxs-lookup"><span data-stu-id="91850-155">Place a script that calls `Blazor.start` after the `_framework/blazor.server.js` script and inside the `</body>` tag.</span></span>

### <a name="logging"></a><span data-ttu-id="91850-156">Registrazione</span><span class="sxs-lookup"><span data-stu-id="91850-156">Logging</span></span>

<span data-ttu-id="91850-157">Per configurare la SignalR registrazione del client:</span><span class="sxs-lookup"><span data-stu-id="91850-157">To configure SignalR client logging:</span></span>

* <span data-ttu-id="91850-158">Aggiungere un `autostart="false"` attributo al `<script>` tag per lo `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="91850-158">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="91850-159">Passare un oggetto di configurazione ( `configureSignalR` ) che chiama `configureLogging` con il livello di registrazione nel generatore client.</span><span class="sxs-lookup"><span data-stu-id="91850-159">Pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder.</span></span>

```cshtml
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

<span data-ttu-id="91850-160">Nell'esempio precedente, `information` è equivalente a un livello di log di <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="91850-160">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="91850-161">Modificare il gestore di riconnessione</span><span class="sxs-lookup"><span data-stu-id="91850-161">Modify the reconnection handler</span></span>

<span data-ttu-id="91850-162">Gli eventi di connessione del circuito del gestore di riconnessione possono essere modificati per comportamenti personalizzati, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="91850-162">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="91850-163">Per inviare una notifica all'utente se la connessione viene eliminata.</span><span class="sxs-lookup"><span data-stu-id="91850-163">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="91850-164">Per eseguire la registrazione (dal client) quando si connette un circuito.</span><span class="sxs-lookup"><span data-stu-id="91850-164">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="91850-165">Per modificare gli eventi di connessione:</span><span class="sxs-lookup"><span data-stu-id="91850-165">To modify the connection events:</span></span>

* <span data-ttu-id="91850-166">Aggiungere un `autostart="false"` attributo al `<script>` tag per lo `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="91850-166">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="91850-167">Registra i callback per le modifiche della connessione per le connessioni eliminate ( `onConnectionDown` ) e le connessioni stabilite/ristabilite ( `onConnectionUp` ).</span><span class="sxs-lookup"><span data-stu-id="91850-167">Register callbacks for connection changes for dropped connections (`onConnectionDown`) and established/re-established connections (`onConnectionUp`).</span></span> <span data-ttu-id="91850-168">**Entrambi** `onConnectionDown` `onConnectionUp` è necessario specificare e.</span><span class="sxs-lookup"><span data-stu-id="91850-168">**Both** `onConnectionDown` and `onConnectionUp` must be specified.</span></span>

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="91850-169">Modificare il numero di tentativi di riconnessione e l'intervallo</span><span class="sxs-lookup"><span data-stu-id="91850-169">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="91850-170">Per modificare il numero di tentativi di riconnessione e l'intervallo:</span><span class="sxs-lookup"><span data-stu-id="91850-170">To adjust the reconnection retry count and interval:</span></span>

* <span data-ttu-id="91850-171">Aggiungere un `autostart="false"` attributo al `<script>` tag per lo `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="91850-171">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="91850-172">Impostare il numero di tentativi ( `maxRetries` ) e il periodo di tempo in millisecondi consentiti per ogni nuovo tentativo ( `retryIntervalMilliseconds` ).</span><span class="sxs-lookup"><span data-stu-id="91850-172">Set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`).</span></span>

```cshtml
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

### <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="91850-173">Nascondi o Sostituisci la visualizzazione di riconnessione</span><span class="sxs-lookup"><span data-stu-id="91850-173">Hide or replace the reconnection display</span></span>

<span data-ttu-id="91850-174">Per nascondere la visualizzazione della riconnessione:</span><span class="sxs-lookup"><span data-stu-id="91850-174">To hide the reconnection display:</span></span>

* <span data-ttu-id="91850-175">Aggiungere un `autostart="false"` attributo al `<script>` tag per lo `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="91850-175">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="91850-176">Impostare il gestore di riconnessione `_reconnectionDisplay` su un oggetto vuoto ( `{}` o `new Object()` ).</span><span class="sxs-lookup"><span data-stu-id="91850-176">Set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`).</span></span>

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });
    </script>
</body>
```

<span data-ttu-id="91850-177">Per sostituire la visualizzazione della riconnessione, impostare `_reconnectionDisplay` nell'esempio precedente sull'elemento per la visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="91850-177">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="91850-178">Il segnaposto `{ELEMENT ID}` è l'ID dell'elemento HTML da visualizzare.</span><span class="sxs-lookup"><span data-stu-id="91850-178">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

## <a name="influence-html-head-tag-elements"></a><span data-ttu-id="91850-179">Influenza `<head>` elementi tag HTML</span><span class="sxs-lookup"><span data-stu-id="91850-179">Influence HTML `<head>` tag elements</span></span>

<span data-ttu-id="91850-180">*Questa sezione si applica alla prossima versione di ASP.NET Core 5,0 di Blazor WebAssembly e Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="91850-180">*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="91850-181">Quando viene eseguito il rendering, i `Title` `Link` componenti, e `Meta` aggiungono o aggiornano i dati negli `<head>` elementi tag HTML:</span><span class="sxs-lookup"><span data-stu-id="91850-181">When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

<span data-ttu-id="91850-182">Nell'esempio precedente, i segnaposto per `{TITLE}` , `{URL}` e `{DESCRIPTION}` sono valori stringa, Razor variabili o Razor espressioni.</span><span class="sxs-lookup"><span data-stu-id="91850-182">In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.</span></span>

<span data-ttu-id="91850-183">Si applicano le seguenti caratteristiche:</span><span class="sxs-lookup"><span data-stu-id="91850-183">The following characteristics apply:</span></span>

* <span data-ttu-id="91850-184">Il prerendering lato server è supportato.</span><span class="sxs-lookup"><span data-stu-id="91850-184">Server-side prerendering is supported.</span></span>
* <span data-ttu-id="91850-185">Il `Value` parametro è l'unico parametro valido per il `Title` componente.</span><span class="sxs-lookup"><span data-stu-id="91850-185">The `Value` parameter is the only valid parameter for the `Title` component.</span></span>
* <span data-ttu-id="91850-186">Gli attributi HTML forniti ai `Meta` `Link` componenti e vengono acquisiti in [attributi aggiuntivi](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) e passati al tag HTML sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="91850-186">HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.</span></span>
* <span data-ttu-id="91850-187">Per più `Title` componenti, il titolo della pagina riflette l'oggetto `Value` dell'ultimo componente di cui è stato `Title` eseguito il rendering.</span><span class="sxs-lookup"><span data-stu-id="91850-187">For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.</span></span>
* <span data-ttu-id="91850-188">Se più `Meta` `Link` componenti o sono inclusi con attributi identici, esiste esattamente un tag HTML sottoposto a rendering per ogni `Meta` `Link` componente o.</span><span class="sxs-lookup"><span data-stu-id="91850-188">If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component.</span></span> <span data-ttu-id="91850-189">Due `Meta` `Link` componenti o non possono fare riferimento allo stesso tag HTML sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="91850-189">Two `Meta` or `Link` components can't refer to the same rendered HTML tag.</span></span>
* <span data-ttu-id="91850-190">Le modifiche apportate ai parametri dei `Meta` componenti o esistenti `Link` vengono riflesse nei tag HTML sottoposti a rendering.</span><span class="sxs-lookup"><span data-stu-id="91850-190">Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.</span></span>
* <span data-ttu-id="91850-191">Quando i `Link` `Meta` componenti o non vengono più sottoposti a rendering e quindi eliminati dal Framework, i tag HTML sottoposti a rendering vengono rimossi.</span><span class="sxs-lookup"><span data-stu-id="91850-191">When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.</span></span>

<span data-ttu-id="91850-192">Quando uno dei componenti del Framework viene utilizzato in un componente figlio, il tag HTML sottoposto a rendering influisce su qualsiasi altro componente figlio del componente padre, purché venga eseguito il rendering del componente figlio contenente il componente del Framework.</span><span class="sxs-lookup"><span data-stu-id="91850-192">When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered.</span></span> <span data-ttu-id="91850-193">La distinzione tra l'uso di uno di questi componenti del Framework in un componente figlio e l'inserimento di un tag HTML in `wwwroot/index.html` o è il tag HTML sottoposto a `Pages/_Host.cshtml` rendering di un componente del Framework:</span><span class="sxs-lookup"><span data-stu-id="91850-193">The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="91850-194">Può essere modificato in base allo stato dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="91850-194">Can be modified by application state.</span></span> <span data-ttu-id="91850-195">Un tag HTML hardcoded non può essere modificato in base allo stato dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="91850-195">A hard-coded HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="91850-196">Viene rimosso dal codice HTML `<head>` quando il componente padre non viene più sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="91850-196">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

## <a name="static-files"></a><span data-ttu-id="91850-197">File statici</span><span class="sxs-lookup"><span data-stu-id="91850-197">Static files</span></span>

<span data-ttu-id="91850-198">*Questa sezione si applica a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="91850-198">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="91850-199">Per creare altri mapping di file con <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> o configurare altri <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> , usare **uno** degli approcci seguenti.</span><span class="sxs-lookup"><span data-stu-id="91850-199">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="91850-200">Negli esempi seguenti, il `{EXTENSION}` segnaposto è l'estensione di file e il `{CONTENT TYPE}` segnaposto è il tipo di contenuto.</span><span class="sxs-lookup"><span data-stu-id="91850-200">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="91850-201">Configurare le opzioni tramite l' [inserimento di dipendenze](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` ( `Startup.cs` ) utilizzando <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> :</span><span class="sxs-lookup"><span data-stu-id="91850-201">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

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

  <span data-ttu-id="91850-202">Poiché questo approccio configura lo stesso provider di file usato per gestire `blazor.server.js` , assicurarsi che la configurazione personalizzata non interferisca con il servizio `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="91850-202">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="91850-203">Ad esempio, non rimuovere il mapping per i file JavaScript configurando il provider con `provider.Mappings.Remove(".js")` .</span><span class="sxs-lookup"><span data-stu-id="91850-203">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="91850-204">Usare due chiamate a <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="91850-204">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="91850-205">Configurare il provider di file personalizzato nella prima chiamata con <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> .</span><span class="sxs-lookup"><span data-stu-id="91850-205">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="91850-206">Il secondo middleware serve `blazor.server.js` , che usa la configurazione predefinita dei file statici fornita dal Blazor Framework.</span><span class="sxs-lookup"><span data-stu-id="91850-206">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the Blazor framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

## <a name="additional-resources"></a><span data-ttu-id="91850-207">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="91850-207">Additional resources</span></span>

* <xref:fundamentals/logging/index>
