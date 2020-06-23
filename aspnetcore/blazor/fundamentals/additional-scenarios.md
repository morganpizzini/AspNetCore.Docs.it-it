---
title: BlazorConfigurazione del modello di hosting ASP.NET Core
author: guardrex
description: Informazioni sugli scenari aggiuntivi per la Blazor configurazione del modello di hosting di ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 2efc13d5d4ab91ffdf6c4c7021072a2b3f83153f
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242654"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="c9bf2-103">BlazorConfigurazione del modello di hosting ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c9bf2-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="c9bf2-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c9bf2-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c9bf2-105">Questo articolo illustra la configurazione del modello di hosting.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-105">This article covers hosting model configuration.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalR<span data-ttu-id="c9bf2-106">negoziazione tra le origini per l'autenticazione</span><span class="sxs-lookup"><span data-stu-id="c9bf2-106"> cross-origin negotiation for authentication</span></span>

<span data-ttu-id="c9bf2-107">*Questa sezione si applica a Blazor webassembly.*</span><span class="sxs-lookup"><span data-stu-id="c9bf2-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="c9bf2-108">Per configurare SignalR il client sottostante per l'invio di credenziali, ad esempio cookie o intestazioni di autenticazione http:</span><span class="sxs-lookup"><span data-stu-id="c9bf2-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="c9bf2-109">Usare <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> per impostare <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> le richieste tra origini [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="c9bf2-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

  ```csharp
  public class IncludeRequestCredentialsMessagHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="c9bf2-110">Assegnare <xref:System.Net.Http.HttpMessageHandler> a l' <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opzione:</span><span class="sxs-lookup"><span data-stu-id="c9bf2-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessagHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="c9bf2-111">Per altre informazioni, vedere <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="c9bf2-112">Riflette lo stato di connessione nell'interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="c9bf2-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="c9bf2-113">*Questa sezione si applica al Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="c9bf2-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="c9bf2-114">Quando il client rileva che la connessione è stata persa, viene visualizzata un'interfaccia utente predefinita quando il client tenta di riconnettersi.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="c9bf2-115">Se la riconnessione non riesce, all'utente viene offerta l'opzione per riprovare.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="c9bf2-116">Per personalizzare l'interfaccia utente, definire un elemento con un valore `id` di `components-reconnect-modal` nell'oggetto `<body>` della `_Host.cshtml` Razor pagina:</span><span class="sxs-lookup"><span data-stu-id="c9bf2-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="c9bf2-117">Nella tabella seguente vengono descritte le classi CSS applicate all' `components-reconnect-modal` elemento.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-117">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="c9bf2-118">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="c9bf2-118">CSS class</span></span>                       | <span data-ttu-id="c9bf2-119">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="c9bf2-119">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="c9bf2-120">Connessione persa.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-120">A lost connection.</span></span> <span data-ttu-id="c9bf2-121">È in corso un tentativo di riconnessione del client.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-121">The client is attempting to reconnect.</span></span> <span data-ttu-id="c9bf2-122">Mostra il modale.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-122">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="c9bf2-123">Viene ristabilita una connessione attiva al server.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-123">An active connection is re-established to the server.</span></span> <span data-ttu-id="c9bf2-124">Nascondere il modale.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-124">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="c9bf2-125">Riconnessione non riuscita. probabilmente a causa di un errore di rete.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-125">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="c9bf2-126">Per tentare la riconnessione, chiamare `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="c9bf2-126">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="c9bf2-127">Riconnessione rifiutata.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-127">Reconnection rejected.</span></span> <span data-ttu-id="c9bf2-128">Il server è stato raggiunto ma ha rifiutato la connessione e lo stato dell'utente nel server è andato perso.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-128">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="c9bf2-129">Per ricaricare l'app, chiamare `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="c9bf2-129">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="c9bf2-130">Questo stato di connessione può verificarsi nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="c9bf2-130">This connection state may result when:</span></span><ul><li><span data-ttu-id="c9bf2-131">Si verifica un arresto anomalo del circuito sul lato server.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-131">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="c9bf2-132">Il client viene disconnesso abbastanza a lungo da consentire al server di eliminare lo stato dell'utente.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-132">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="c9bf2-133">Le istanze dei componenti con cui l'utente interagisce sono state eliminate.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-133">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="c9bf2-134">Il server viene riavviato oppure il processo di lavoro dell'app viene riciclato.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-134">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="c9bf2-135">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="c9bf2-135">Render mode</span></span>

<span data-ttu-id="c9bf2-136">*Questa sezione si applica al Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="c9bf2-136">*This section applies to Blazor Server.*</span></span>

Blazor<span data-ttu-id="c9bf2-137">Per impostazione predefinita, le app Server sono impostate per eseguire il prerendering dell'interfaccia utente nel server prima che venga stabilita la connessione client al server.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-137"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="c9bf2-138">Questa impostazione è configurata nella `_Host.cshtml` Razor pagina:</span><span class="sxs-lookup"><span data-stu-id="c9bf2-138">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="c9bf2-139"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>Configura se il componente:</span><span class="sxs-lookup"><span data-stu-id="c9bf2-139"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="c9bf2-140">Viene preeseguito nella pagina.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-140">Is prerendered into the page.</span></span>
* <span data-ttu-id="c9bf2-141">Viene sottoposto a rendering come HTML statico nella pagina o se include le informazioni necessarie per il bootstrap di un' Blazor app dall'agente utente.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-141">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="c9bf2-142">Descrizione</span><span class="sxs-lookup"><span data-stu-id="c9bf2-142">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="c9bf2-143">Esegue il rendering del componente in HTML statico e include un marcatore per un' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-143">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="c9bf2-144">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-144">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="c9bf2-145">Esegue il rendering di un marcatore per un' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-145">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="c9bf2-146">L'output del componente non è incluso.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-146">Output from the component isn't included.</span></span> <span data-ttu-id="c9bf2-147">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-147">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="c9bf2-148">Esegue il rendering del componente in HTML statico.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-148">Renders the component into static HTML.</span></span> |

<span data-ttu-id="c9bf2-149">Il rendering dei componenti server da una pagina HTML statica non è supportato.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-149">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="c9bf2-150">Configurare il SignalR client per le Blazor app Server</span><span class="sxs-lookup"><span data-stu-id="c9bf2-150">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="c9bf2-151">*Questa sezione si applica al Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="c9bf2-151">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="c9bf2-152">In alcuni casi, è necessario configurare il SignalR client usato dalle Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-152">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="c9bf2-153">È ad esempio possibile configurare la registrazione sul SignalR client per diagnosticare un problema di connessione.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-153">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="c9bf2-154">Per configurare il SignalR client nel `Pages/_Host.cshtml` file:</span><span class="sxs-lookup"><span data-stu-id="c9bf2-154">To configure the SignalR client in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="c9bf2-155">Aggiungere un `autostart="false"` attributo al `<script>` tag per lo `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-155">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="c9bf2-156">Chiamare `Blazor.start` e passare un oggetto di configurazione che specifichi il SignalR generatore.</span><span class="sxs-lookup"><span data-stu-id="c9bf2-156">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a><span data-ttu-id="c9bf2-157">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="c9bf2-157">Additional resources</span></span>

* <xref:fundamentals/logging/index>
