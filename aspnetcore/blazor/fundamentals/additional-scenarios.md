---
title: BlazorConfigurazione del modello di hosting ASP.NET Core
author: guardrex
description: Informazioni sugli scenari aggiuntivi per la Blazor configurazione del modello di hosting di ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2020
no-loc:
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
ms.openlocfilehash: 075bcc68fd2dff0ebf2cfceacec24fde8c818603
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326533"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="fe364-103">BlazorConfigurazione del modello di hosting ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fe364-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="fe364-104">Di [Daniel Roth](https://github.com/danroth27), [Marin Buck](https://github.com/MackinnonBuck)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fe364-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fe364-105">Questo articolo illustra la configurazione del modello di hosting.</span><span class="sxs-lookup"><span data-stu-id="fe364-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="fe364-106">SignalR negoziazione tra le origini per l'autenticazione</span><span class="sxs-lookup"><span data-stu-id="fe364-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="fe364-107">*Questa sezione si applica a Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="fe364-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="fe364-108">Per configurare SignalR il client sottostante per l'invio di credenziali, ad esempio le cookie intestazioni s o http Authentication:</span><span class="sxs-lookup"><span data-stu-id="fe364-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="fe364-109">Usare <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> per impostare <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> le richieste tra origini [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="fe364-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="fe364-110">Assegnare <xref:System.Net.Http.HttpMessageHandler> a l' <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opzione:</span><span class="sxs-lookup"><span data-stu-id="fe364-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="fe364-111">Per altre informazioni, vedere <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="fe364-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="fe364-112">Riflette lo stato di connessione nell'interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="fe364-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="fe364-113">*Questa sezione si applica a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="fe364-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="fe364-114">Quando il client rileva che la connessione è stata persa, viene visualizzata un'interfaccia utente predefinita quando il client tenta di riconnettersi.</span><span class="sxs-lookup"><span data-stu-id="fe364-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="fe364-115">Se la riconnessione non riesce, all'utente viene offerta l'opzione per riprovare.</span><span class="sxs-lookup"><span data-stu-id="fe364-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="fe364-116">Per personalizzare l'interfaccia utente, definire un elemento con un valore `id` di `components-reconnect-modal` nell'oggetto `<body>` della `_Host.cshtml` Razor pagina:</span><span class="sxs-lookup"><span data-stu-id="fe364-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="fe364-117">Aggiungere quanto segue al foglio di stile dell'app ( `wwwroot/css/app.css` o `wwwroot/css/site.css` ):</span><span class="sxs-lookup"><span data-stu-id="fe364-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="fe364-118">Nella tabella seguente vengono descritte le classi CSS applicate all' `components-reconnect-modal` elemento.</span><span class="sxs-lookup"><span data-stu-id="fe364-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="fe364-119">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="fe364-119">CSS class</span></span>                       | <span data-ttu-id="fe364-120">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="fe364-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="fe364-121">Connessione persa.</span><span class="sxs-lookup"><span data-stu-id="fe364-121">A lost connection.</span></span> <span data-ttu-id="fe364-122">È in corso un tentativo di riconnessione del client.</span><span class="sxs-lookup"><span data-stu-id="fe364-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="fe364-123">Mostra il modale.</span><span class="sxs-lookup"><span data-stu-id="fe364-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="fe364-124">Viene ristabilita una connessione attiva al server.</span><span class="sxs-lookup"><span data-stu-id="fe364-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="fe364-125">Nascondere il modale.</span><span class="sxs-lookup"><span data-stu-id="fe364-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="fe364-126">Riconnessione non riuscita. probabilmente a causa di un errore di rete.</span><span class="sxs-lookup"><span data-stu-id="fe364-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="fe364-127">Per tentare la riconnessione, chiamare `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="fe364-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="fe364-128">Riconnessione rifiutata.</span><span class="sxs-lookup"><span data-stu-id="fe364-128">Reconnection rejected.</span></span> <span data-ttu-id="fe364-129">Il server è stato raggiunto ma ha rifiutato la connessione e lo stato dell'utente nel server è andato perso.</span><span class="sxs-lookup"><span data-stu-id="fe364-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="fe364-130">Per ricaricare l'app, chiamare `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="fe364-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="fe364-131">Questo stato di connessione può verificarsi nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="fe364-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="fe364-132">Si verifica un arresto anomalo del circuito sul lato server.</span><span class="sxs-lookup"><span data-stu-id="fe364-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="fe364-133">Il client viene disconnesso abbastanza a lungo da consentire al server di eliminare lo stato dell'utente.</span><span class="sxs-lookup"><span data-stu-id="fe364-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="fe364-134">Le istanze dei componenti con cui l'utente interagisce sono state eliminate.</span><span class="sxs-lookup"><span data-stu-id="fe364-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="fe364-135">Il server viene riavviato oppure il processo di lavoro dell'app viene riciclato.</span><span class="sxs-lookup"><span data-stu-id="fe364-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="fe364-136">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="fe364-136">Render mode</span></span>

<span data-ttu-id="fe364-137">*Questa sezione si applica a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="fe364-137">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="fe364-138">Blazor Server per impostazione predefinita, le app sono configurate per eseguire il prerendering dell'interfaccia utente nel server prima che venga stabilita la connessione client al server.</span><span class="sxs-lookup"><span data-stu-id="fe364-138">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="fe364-139">Questa impostazione è configurata nella `_Host.cshtml` Razor pagina:</span><span class="sxs-lookup"><span data-stu-id="fe364-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="fe364-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> Configura se il componente:</span><span class="sxs-lookup"><span data-stu-id="fe364-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="fe364-141">Viene preeseguito nella pagina.</span><span class="sxs-lookup"><span data-stu-id="fe364-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="fe364-142">Viene sottoposto a rendering come HTML statico nella pagina o se include le informazioni necessarie per il bootstrap di un' Blazor app dall'agente utente.</span><span class="sxs-lookup"><span data-stu-id="fe364-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="fe364-143">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="fe364-143">Render mode</span></span> | <span data-ttu-id="fe364-144">Descrizione</span><span class="sxs-lookup"><span data-stu-id="fe364-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="fe364-145">Esegue il rendering del componente in HTML statico e include un marcatore per un' Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="fe364-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="fe364-146">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="fe364-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="fe364-147">Esegue il rendering di un marcatore per un' Blazor Server app.</span><span class="sxs-lookup"><span data-stu-id="fe364-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="fe364-148">L'output del componente non è incluso.</span><span class="sxs-lookup"><span data-stu-id="fe364-148">Output from the component isn't included.</span></span> <span data-ttu-id="fe364-149">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="fe364-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="fe364-150">Esegue il rendering del componente in HTML statico.</span><span class="sxs-lookup"><span data-stu-id="fe364-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="fe364-151">Il rendering dei componenti server da una pagina HTML statica non è supportato.</span><span class="sxs-lookup"><span data-stu-id="fe364-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="initialize-the-no-locblazor-circuit"></a><span data-ttu-id="fe364-152">Inizializzare il Blazor circuito</span><span class="sxs-lookup"><span data-stu-id="fe364-152">Initialize the Blazor circuit</span></span>

<span data-ttu-id="fe364-153">*Questa sezione si applica a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="fe364-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="fe364-154">Configurare l'avvio manuale del Blazor Server [ SignalR circuito](xref:blazor/hosting-models#circuits) di un'app nel `Pages/_Host.cshtml` file:</span><span class="sxs-lookup"><span data-stu-id="fe364-154">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="fe364-155">Aggiungere un `autostart="false"` attributo al `<script>` tag per lo `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="fe364-155">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="fe364-156">Inserire uno script che chiama `Blazor.start` dopo il `blazor.server.js` tag dello script e all'interno del `</body>` tag di chiusura.</span><span class="sxs-lookup"><span data-stu-id="fe364-156">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="fe364-157">Quando `autostart` è disabilitato, qualsiasi aspetto dell'app che non dipende dal circuito funziona normalmente.</span><span class="sxs-lookup"><span data-stu-id="fe364-157">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="fe364-158">Il routing lato client, ad esempio, è operativo.</span><span class="sxs-lookup"><span data-stu-id="fe364-158">For example, client-side routing is operational.</span></span> <span data-ttu-id="fe364-159">Tuttavia, qualsiasi aspetto che dipende dal circuito non è operativo fino a quando non `Blazor.start` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="fe364-159">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="fe364-160">Il comportamento dell'app è imprevedibile senza un circuito stabilito.</span><span class="sxs-lookup"><span data-stu-id="fe364-160">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="fe364-161">Ad esempio, i metodi dei componenti non vengono eseguiti mentre il circuito è disconnesso.</span><span class="sxs-lookup"><span data-stu-id="fe364-161">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a><span data-ttu-id="fe364-162">Inizializza Blazor quando il documento è pronto</span><span class="sxs-lookup"><span data-stu-id="fe364-162">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="fe364-163">Per inizializzare l' Blazor app quando il documento è pronto:</span><span class="sxs-lookup"><span data-stu-id="fe364-163">To initialize the Blazor app when the document is ready:</span></span>

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="fe364-164">Concatenare a `Promise` che risulta da un avvio manuale</span><span class="sxs-lookup"><span data-stu-id="fe364-164">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="fe364-165">Per eseguire attività aggiuntive, ad esempio l'inizializzazione dell'interoperabilità JS, usare `then` per concatenare a `Promise` che risulta da un'app manuale di Blazor avvio:</span><span class="sxs-lookup"><span data-stu-id="fe364-165">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

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

### <a name="configure-the-no-locsignalr-client"></a><span data-ttu-id="fe364-166">Configurare il SignalR client</span><span class="sxs-lookup"><span data-stu-id="fe364-166">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="fe364-167">Registrazione</span><span class="sxs-lookup"><span data-stu-id="fe364-167">Logging</span></span>

<span data-ttu-id="fe364-168">Per configurare la SignalR registrazione client, passare un oggetto di configurazione ( `configureSignalR` ) che chiama `configureLogging` con il livello di registrazione nel generatore client:</span><span class="sxs-lookup"><span data-stu-id="fe364-168">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

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

<span data-ttu-id="fe364-169">Nell'esempio precedente, `information` è equivalente a un livello di log di <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="fe364-169">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="fe364-170">Modificare il gestore di riconnessione</span><span class="sxs-lookup"><span data-stu-id="fe364-170">Modify the reconnection handler</span></span>

<span data-ttu-id="fe364-171">Gli eventi di connessione del circuito del gestore di riconnessione possono essere modificati per comportamenti personalizzati, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="fe364-171">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="fe364-172">Per inviare una notifica all'utente se la connessione viene eliminata.</span><span class="sxs-lookup"><span data-stu-id="fe364-172">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="fe364-173">Per eseguire la registrazione (dal client) quando si connette un circuito.</span><span class="sxs-lookup"><span data-stu-id="fe364-173">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="fe364-174">Per modificare gli eventi di connessione, registrare i callback per le seguenti modifiche della connessione:</span><span class="sxs-lookup"><span data-stu-id="fe364-174">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="fe364-175">Connessioni eliminate utilizzare `onConnectionDown` .</span><span class="sxs-lookup"><span data-stu-id="fe364-175">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="fe364-176">Le connessioni stabilite/ristabilite usano `onConnectionUp` .</span><span class="sxs-lookup"><span data-stu-id="fe364-176">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="fe364-177">**Entrambi** `onConnectionDown` `onConnectionUp` è necessario specificare e:</span><span class="sxs-lookup"><span data-stu-id="fe364-177">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

```cshtml
<body>

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="fe364-178">Modificare il numero di tentativi di riconnessione e l'intervallo</span><span class="sxs-lookup"><span data-stu-id="fe364-178">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="fe364-179">Per modificare il numero di tentativi di riconnessione e l'intervallo, impostare il numero di tentativi ( `maxRetries` ) e il periodo di tempo in millisecondi consentiti per ogni nuovo tentativo ( `retryIntervalMilliseconds` ):</span><span class="sxs-lookup"><span data-stu-id="fe364-179">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

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

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="fe364-180">Nascondi o Sostituisci la visualizzazione di riconnessione</span><span class="sxs-lookup"><span data-stu-id="fe364-180">Hide or replace the reconnection display</span></span>

<span data-ttu-id="fe364-181">Per nascondere la visualizzazione della riconnessione, impostare il gestore di riconnessione `_reconnectionDisplay` su un oggetto vuoto ( `{}` o `new Object()` ):</span><span class="sxs-lookup"><span data-stu-id="fe364-181">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

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

<span data-ttu-id="fe364-182">Per sostituire la visualizzazione della riconnessione, impostare `_reconnectionDisplay` nell'esempio precedente sull'elemento per la visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="fe364-182">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="fe364-183">Il segnaposto `{ELEMENT ID}` è l'ID dell'elemento HTML da visualizzare.</span><span class="sxs-lookup"><span data-stu-id="fe364-183">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="fe364-184">Personalizzare il ritardo prima che la visualizzazione della riconnessione venga visualizzata impostando la `transition-delay` proprietà nel CSS ( `wwwroot/css/site.css` ) dell'app per l'elemento modale.</span><span class="sxs-lookup"><span data-stu-id="fe364-184">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="fe364-185">Nell'esempio seguente viene impostato il ritardo di transizione da 500 ms (impostazione predefinita) a 1.000 ms (1 secondo):</span><span class="sxs-lookup"><span data-stu-id="fe364-185">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a><span data-ttu-id="fe364-186">Disconnettere il Blazor circuito dal client</span><span class="sxs-lookup"><span data-stu-id="fe364-186">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="fe364-187">Per impostazione predefinita, un Blazor circuito viene disconnesso quando viene attivato l' [ `unload` evento della pagina](https://developer.mozilla.org/docs/Web/API/Window/unload_event) .</span><span class="sxs-lookup"><span data-stu-id="fe364-187">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="fe364-188">Per disconnettere il circuito per altri scenari nel client, richiamare `Blazor.disconnect` nel gestore eventi appropriato.</span><span class="sxs-lookup"><span data-stu-id="fe364-188">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="fe364-189">Nell'esempio seguente il circuito viene disconnesso quando la pagina è nascosta ([ `pagehide` evento](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span><span class="sxs-lookup"><span data-stu-id="fe364-189">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

## <a name="influence-html-head-tag-elements"></a><span data-ttu-id="fe364-190">Influenza `<head>` elementi tag HTML</span><span class="sxs-lookup"><span data-stu-id="fe364-190">Influence HTML `<head>` tag elements</span></span>

<span data-ttu-id="fe364-191">*Questa sezione si applica alla prossima versione di ASP.NET Core 5,0 di Blazor WebAssembly e Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="fe364-191">*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="fe364-192">Quando viene eseguito il rendering, i `Title` `Link` componenti, e `Meta` aggiungono o aggiornano i dati negli `<head>` elementi tag HTML:</span><span class="sxs-lookup"><span data-stu-id="fe364-192">When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

<span data-ttu-id="fe364-193">Nell'esempio precedente, i segnaposto per `{TITLE}` , `{URL}` e `{DESCRIPTION}` sono valori stringa, Razor variabili o Razor espressioni.</span><span class="sxs-lookup"><span data-stu-id="fe364-193">In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.</span></span>

<span data-ttu-id="fe364-194">Si applicano le seguenti caratteristiche:</span><span class="sxs-lookup"><span data-stu-id="fe364-194">The following characteristics apply:</span></span>

* <span data-ttu-id="fe364-195">Il prerendering lato server è supportato.</span><span class="sxs-lookup"><span data-stu-id="fe364-195">Server-side prerendering is supported.</span></span>
* <span data-ttu-id="fe364-196">Il `Value` parametro è l'unico parametro valido per il `Title` componente.</span><span class="sxs-lookup"><span data-stu-id="fe364-196">The `Value` parameter is the only valid parameter for the `Title` component.</span></span>
* <span data-ttu-id="fe364-197">Gli attributi HTML forniti ai `Meta` `Link` componenti e vengono acquisiti in [attributi aggiuntivi](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) e passati al tag HTML sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="fe364-197">HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.</span></span>
* <span data-ttu-id="fe364-198">Per più `Title` componenti, il titolo della pagina riflette l'oggetto `Value` dell'ultimo componente di cui è stato `Title` eseguito il rendering.</span><span class="sxs-lookup"><span data-stu-id="fe364-198">For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.</span></span>
* <span data-ttu-id="fe364-199">Se più `Meta` `Link` componenti o sono inclusi con attributi identici, esiste esattamente un tag HTML sottoposto a rendering per ogni `Meta` `Link` componente o.</span><span class="sxs-lookup"><span data-stu-id="fe364-199">If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component.</span></span> <span data-ttu-id="fe364-200">Due `Meta` `Link` componenti o non possono fare riferimento allo stesso tag HTML sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="fe364-200">Two `Meta` or `Link` components can't refer to the same rendered HTML tag.</span></span>
* <span data-ttu-id="fe364-201">Le modifiche apportate ai parametri dei `Meta` componenti o esistenti `Link` vengono riflesse nei tag HTML sottoposti a rendering.</span><span class="sxs-lookup"><span data-stu-id="fe364-201">Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.</span></span>
* <span data-ttu-id="fe364-202">Quando i `Link` `Meta` componenti o non vengono più sottoposti a rendering e quindi eliminati dal Framework, i tag HTML sottoposti a rendering vengono rimossi.</span><span class="sxs-lookup"><span data-stu-id="fe364-202">When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.</span></span>

<span data-ttu-id="fe364-203">Quando uno dei componenti del Framework viene utilizzato in un componente figlio, il tag HTML sottoposto a rendering influisce su qualsiasi altro componente figlio del componente padre, purché venga eseguito il rendering del componente figlio contenente il componente del Framework.</span><span class="sxs-lookup"><span data-stu-id="fe364-203">When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered.</span></span> <span data-ttu-id="fe364-204">La distinzione tra l'uso di uno di questi componenti del Framework in un componente figlio e l'inserimento di un tag HTML in `wwwroot/index.html` o è il tag HTML sottoposto a `Pages/_Host.cshtml` rendering di un componente del Framework:</span><span class="sxs-lookup"><span data-stu-id="fe364-204">The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="fe364-205">Può essere modificato in base allo stato dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="fe364-205">Can be modified by application state.</span></span> <span data-ttu-id="fe364-206">Un tag HTML hardcoded non può essere modificato in base allo stato dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="fe364-206">A hard-coded HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="fe364-207">Viene rimosso dal codice HTML `<head>` quando il componente padre non viene più sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="fe364-207">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

## <a name="static-files"></a><span data-ttu-id="fe364-208">File statici</span><span class="sxs-lookup"><span data-stu-id="fe364-208">Static files</span></span>

<span data-ttu-id="fe364-209">*Questa sezione si applica a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="fe364-209">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="fe364-210">Per creare altri mapping di file con <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> o configurare altri <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> , usare **uno** degli approcci seguenti.</span><span class="sxs-lookup"><span data-stu-id="fe364-210">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="fe364-211">Negli esempi seguenti, il `{EXTENSION}` segnaposto è l'estensione di file e il `{CONTENT TYPE}` segnaposto è il tipo di contenuto.</span><span class="sxs-lookup"><span data-stu-id="fe364-211">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="fe364-212">Configurare le opzioni tramite l' [inserimento di dipendenze](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` ( `Startup.cs` ) utilizzando <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> :</span><span class="sxs-lookup"><span data-stu-id="fe364-212">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

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

  <span data-ttu-id="fe364-213">Poiché questo approccio configura lo stesso provider di file usato per gestire `blazor.server.js` , assicurarsi che la configurazione personalizzata non interferisca con il servizio `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="fe364-213">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="fe364-214">Ad esempio, non rimuovere il mapping per i file JavaScript configurando il provider con `provider.Mappings.Remove(".js")` .</span><span class="sxs-lookup"><span data-stu-id="fe364-214">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="fe364-215">Usare due chiamate a <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="fe364-215">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="fe364-216">Configurare il provider di file personalizzato nella prima chiamata con <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> .</span><span class="sxs-lookup"><span data-stu-id="fe364-216">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="fe364-217">Il secondo middleware serve `blazor.server.js` , che usa la configurazione predefinita dei file statici fornita dal Blazor Framework.</span><span class="sxs-lookup"><span data-stu-id="fe364-217">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the Blazor framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* <span data-ttu-id="fe364-218">È possibile evitare di interferire con `_framework/blazor.server.js` il servizio utilizzando <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> per eseguire un middleware di file statico personalizzato:</span><span class="sxs-lookup"><span data-stu-id="fe364-218">You can avoid interfering with serving `_framework/blazor.server.js` by using <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> to execute a custom Static File Middleware:</span></span>

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```

## <a name="additional-resources"></a><span data-ttu-id="fe364-219">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="fe364-219">Additional resources</span></span>

* <xref:fundamentals/logging/index>
