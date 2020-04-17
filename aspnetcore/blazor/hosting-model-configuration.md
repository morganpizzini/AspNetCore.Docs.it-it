---
title: Configurazione Blazor del modello di hosting ASP.NET Core
author: guardrex
description: Informazioni Blazor sulla configurazione del modello di hosting, incluso come integrare i componenti Razor nelle pagine Razor e nelle app MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 1b0f5f4071be7134d7de08615ec016ca6567385d
ms.sourcegitcommit: 49c91ad4b69f4f8032394cbf2d5ae1b19a7f863b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81544843"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="8d9ea-103">ASP.NET configurazione del modello di hosting Core Blazor</span><span class="sxs-lookup"><span data-stu-id="8d9ea-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="8d9ea-104">Di [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="8d9ea-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="8d9ea-105">Questo articolo illustra la configurazione del modello di hosting.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="8d9ea-106">WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="8d9ea-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="8d9ea-107">Environment</span><span class="sxs-lookup"><span data-stu-id="8d9ea-107">Environment</span></span>

<span data-ttu-id="8d9ea-108">Quando si esegue un'app in locale, l'ambiente predefinito è Sviluppo.When running an app locally, the environment defaults to Development.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="8d9ea-109">Quando l'app viene pubblicata, l'ambiente viene impostato su Produzione per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="8d9ea-110">Un'app Blazor WebAssembly ospitata preleva l'ambiente dal server tramite un middleware che comunica l'ambiente al browser aggiungendo l'intestazione. `blazor-environment`</span><span class="sxs-lookup"><span data-stu-id="8d9ea-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="8d9ea-111">Il valore dell'intestazione è l'ambiente.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-111">The value of the header is the environment.</span></span> <span data-ttu-id="8d9ea-112">L'app Blazor ospitata e l'app server condividono lo stesso ambiente.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="8d9ea-113">Per ulteriori informazioni, incluso come <xref:fundamentals/environments>configurare l'ambiente, vedere .</span><span class="sxs-lookup"><span data-stu-id="8d9ea-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="8d9ea-114">Per un'app autonoma in esecuzione `blazor-environment` in locale, il server di sviluppo aggiunge l'intestazione per specificare l'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="8d9ea-115">Per specificare l'ambiente per `blazor-environment` altri ambienti di hosting, aggiungere l'intestazione.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="8d9ea-116">Nell'esempio seguente per IIS aggiungere l'intestazione personalizzata al file *web.config* pubblicato.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="8d9ea-117">Il file *web.config* si trova nella cartella *bin/Release/'TARGET FRAMEWORK/publish:*</span><span class="sxs-lookup"><span data-stu-id="8d9ea-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> <span data-ttu-id="8d9ea-118">Per utilizzare un file *web.config* personalizzato per IIS che non viene sovrascritto <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>quando l'app viene pubblicata nella cartella di *pubblicazione,* vedere .</span><span class="sxs-lookup"><span data-stu-id="8d9ea-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="8d9ea-119">Ottenere l'ambiente dell'app in `IWebAssemblyHostEnvironment` un componente `Environment` inserendo e leggendo la proprietà:</span><span class="sxs-lookup"><span data-stu-id="8d9ea-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="8d9ea-120">Durante l'avvio, l'espone `WebAssemblyHostBuilder` la `IWebAssemblyHostEnvironment` tramite la proprietà , che consente agli sviluppatori di disporre di logica specifica dell'ambiente nel codice:During startup, the exposes the through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span><span class="sxs-lookup"><span data-stu-id="8d9ea-120">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="8d9ea-121">I seguenti metodi di estensione pratico consentono di controllare l'ambiente corrente per i nomi di ambiente personalizzato, produzione, gestione temporanea e personalizzati:</span><span class="sxs-lookup"><span data-stu-id="8d9ea-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="8d9ea-122">'IsEnvironment("'ENVIRONMENT NAME'")</span><span class="sxs-lookup"><span data-stu-id="8d9ea-122">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

<span data-ttu-id="8d9ea-123">La `IWebAssemblyHostEnvironment.BaseAddress` proprietà può essere utilizzata `NavigationManager` durante l'avvio quando il servizio non è disponibile.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-123">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="8d9ea-124">Configurazione</span><span class="sxs-lookup"><span data-stu-id="8d9ea-124">Configuration</span></span>

<span data-ttu-id="8d9ea-125">A partire dalla versione ASP.NET Core 3.2 Preview 3[(la versione corrente è 3.2 Preview 4](xref:blazor/get-started)), Blazor WebAssembly supporta la configurazione da:</span><span class="sxs-lookup"><span data-stu-id="8d9ea-125">As of the ASP.NET Core 3.2 Preview 3 release ([current release is 3.2 Preview 4](xref:blazor/get-started)), Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="8d9ea-126">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="8d9ea-126">*wwwroot/appsettings.json*</span></span>
* <span data-ttu-id="8d9ea-127">*wwwroot/appsettings. AMBIENTE.*</span><span class="sxs-lookup"><span data-stu-id="8d9ea-127">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>

<span data-ttu-id="8d9ea-128">Aggiungere un file *appsettings.json* nella cartella *wwwroot:*</span><span class="sxs-lookup"><span data-stu-id="8d9ea-128">Add an *appsettings.json* file in the *wwwroot* folder:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="8d9ea-129">Inserire <xref:Microsoft.Extensions.Configuration.IConfiguration> un'istanza in un componente per accedere ai dati di configurazione:Inject an instance into a component to access the configuration data:</span><span class="sxs-lookup"><span data-stu-id="8d9ea-129">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

> [!WARNING]
> <span data-ttu-id="8d9ea-130">La configurazione in un'app Blazor WebAssembly è visibile agli utenti.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-130">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="8d9ea-131">**Non archiviare segreti o credenziali dell'app nella configurazione.**</span><span class="sxs-lookup"><span data-stu-id="8d9ea-131">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="8d9ea-132">I file di configurazione vengono memorizzati nella cache per l'utilizzo offline.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-132">Configuration files are cached for offline use.</span></span> <span data-ttu-id="8d9ea-133">Con [Progressive Web Applications (PWAs),](xref:blazor/progressive-web-app)è possibile aggiornare i file di configurazione solo quando si crea una nuova distribuzione.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-133">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="8d9ea-134">La modifica dei file di configurazione tra le distribuzioni non ha alcun effetto perché:Editing configuration files between deployments has no effect because:</span><span class="sxs-lookup"><span data-stu-id="8d9ea-134">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="8d9ea-135">Gli utenti hanno memorizzato nella cache le versioni dei file che continuano a utilizzare.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-135">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="8d9ea-136">I file *service-worker.js* e *service-worker-assets.js* di PWA devono essere ricompilati durante la compilazione, che segnalano all'app la prossima visita online dell'utente che l'app è stata ridistribuita.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-136">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="8d9ea-137">Per ulteriori informazioni sulla modalità di gestione degli <xref:blazor/progressive-web-app#background-updates>aggiornamenti in background da parte dei PWA, vedere .</span><span class="sxs-lookup"><span data-stu-id="8d9ea-137">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="8d9ea-138">Server Blazor</span><span class="sxs-lookup"><span data-stu-id="8d9ea-138">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="8d9ea-139">Riflettere lo stato della connessione nell'interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="8d9ea-139">Reflect the connection state in the UI</span></span>

<span data-ttu-id="8d9ea-140">Quando il client rileva che la connessione è stata persa, un'interfaccia utente predefinita viene visualizzata all'utente mentre il client tenta di riconnettersi.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-140">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="8d9ea-141">Se la riconnessione non riesce, all'utente viene fornita l'opzione per riprovare.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-141">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="8d9ea-142">Per personalizzare l'interfaccia utente, `id` `components-reconnect-modal` definire `<body>` un elemento con un di nella pagina *Razor _Host.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="8d9ea-142">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="8d9ea-143">Nella tabella seguente vengono descritte `components-reconnect-modal` le classi CSS applicate all'elemento.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-143">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="8d9ea-144">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="8d9ea-144">CSS class</span></span>                       | <span data-ttu-id="8d9ea-145">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="8d9ea-145">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="8d9ea-146">Una connessione persa.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-146">A lost connection.</span></span> <span data-ttu-id="8d9ea-147">Il client sta tentando di riconnettersi.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-147">The client is attempting to reconnect.</span></span> <span data-ttu-id="8d9ea-148">Mostra il modale.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-148">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="8d9ea-149">Viene ristabilita una connessione attiva al server.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-149">An active connection is re-established to the server.</span></span> <span data-ttu-id="8d9ea-150">Nascondi il modale.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-150">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="8d9ea-151">Riconnessione non riuscita, probabilmente a causa di un errore di rete.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-151">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="8d9ea-152">Per tentare la `window.Blazor.reconnect()`riconnessione, chiamare .</span><span class="sxs-lookup"><span data-stu-id="8d9ea-152">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="8d9ea-153">Riconnessione rifiutata.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-153">Reconnection rejected.</span></span> <span data-ttu-id="8d9ea-154">Il server è stato raggiunto ma ha rifiutato la connessione e lo stato dell'utente sul server viene perso.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-154">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="8d9ea-155">Per ricaricare l'app, chiamare `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-155">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="8d9ea-156">Questo stato di connessione può verificarsi quando:This connection state may result when:</span><span class="sxs-lookup"><span data-stu-id="8d9ea-156">This connection state may result when:</span></span><ul><li><span data-ttu-id="8d9ea-157">Si verifica un arresto anomalo nel circuito lato server.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-157">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="8d9ea-158">Il client viene disconnesso abbastanza a lungo da richiedere al server di eliminare lo stato dell'utente.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-158">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="8d9ea-159">Le istanze dei componenti con cui l'utente interagisce vengono eliminate.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-159">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="8d9ea-160">Il server viene riavviato o il processo di lavoro dell'app viene riciclato.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-160">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="8d9ea-161">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="8d9ea-161">Render mode</span></span>

<span data-ttu-id="8d9ea-162">Le app del server Blazor sono configurate per impostazione predefinita per eseguire il prerendering dell'interfaccia utente sul server prima che venga stabilita la connessione client al server.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-162">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="8d9ea-163">Questo è impostato nella pagina *_Host.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="8d9ea-163">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="8d9ea-164">`RenderMode`configura se il componente:</span><span class="sxs-lookup"><span data-stu-id="8d9ea-164">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="8d9ea-165">Viene eseguito il prerendering nella pagina.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-165">Is prerendered into the page.</span></span>
* <span data-ttu-id="8d9ea-166">Viene eseguito il rendering come HTML statico nella pagina o se include le informazioni necessarie per eseguire il bootstrap di un'app Blazor dall'agente utente.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-166">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="8d9ea-167">Descrizione</span><span class="sxs-lookup"><span data-stu-id="8d9ea-167">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="8d9ea-168">Esegue il rendering del componente in Blazor codice HTML statico e include un marcatore per un'app Server.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-168">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="8d9ea-169">Quando l'agente utente viene avviato, Blazor questo marcatore viene usato per eseguire il bootstrap di un'app.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-169">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="8d9ea-170">Esegue il rendering Blazor di un marcatore per un'app Server.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-170">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="8d9ea-171">L'output del componente non è incluso.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-171">Output from the component isn't included.</span></span> <span data-ttu-id="8d9ea-172">Quando l'agente utente viene avviato, Blazor questo marcatore viene usato per eseguire il bootstrap di un'app.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-172">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="8d9ea-173">Esegue il rendering del componente in codice HTML statico.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-173">Renders the component into static HTML.</span></span> |

<span data-ttu-id="8d9ea-174">Il rendering dei componenti server da una pagina HTML statica non è supportato.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-174">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="8d9ea-175">Rendering di componenti interattivi con stato da pagine e viste RazorRender stateful interactive components from Razor pages and views</span><span class="sxs-lookup"><span data-stu-id="8d9ea-175">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="8d9ea-176">I componenti interattivi con stato possono essere aggiunti a una pagina razor o a una visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-176">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="8d9ea-177">Quando viene eseguito il rendering della pagina o della visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="8d9ea-177">When the page or view renders:</span></span>

* <span data-ttu-id="8d9ea-178">Il rendering del componente viene eseguito con il prerendering della pagina o della vista.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-178">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="8d9ea-179">Lo stato iniziale del componente utilizzato per il prerendering viene perso.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-179">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="8d9ea-180">Il nuovo stato del SignalR componente viene creato quando viene stabilita la connessione.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-180">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="8d9ea-181">La pagina Razor `Counter` seguente esegue il rendering di un componente:</span><span class="sxs-lookup"><span data-stu-id="8d9ea-181">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="8d9ea-182">Rendering di componenti non interattivi da pagine e viste Razor</span><span class="sxs-lookup"><span data-stu-id="8d9ea-182">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="8d9ea-183">Nella pagina Razor seguente, il rendering del `Counter` componente viene eseguito in modo statico con un valore iniziale specificato utilizzando un form:</span><span class="sxs-lookup"><span data-stu-id="8d9ea-183">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="8d9ea-184">Poiché `MyComponent` viene eseguito il rendering statico, il componente non può essere interattivo.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-184">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="8d9ea-185">Configurare SignalR il Blazor client per le app server</span><span class="sxs-lookup"><span data-stu-id="8d9ea-185">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="8d9ea-186">A volte, è SignalR necessario configurare il client utilizzato dalle Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-186">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="8d9ea-187">Ad esempio, è possibile configurare SignalR la registrazione sul client per diagnosticare un problema di connessione.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-187">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="8d9ea-188">Per configurare il SignalR client nel file *Pages/_Host.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="8d9ea-188">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="8d9ea-189">Aggiungere `autostart="false"` un attributo `<script>` al `blazor.server.js` tag per lo script.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-189">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="8d9ea-190">Chiamare `Blazor.start` e passare un oggetto di SignalR configurazione che specifica il generatore.</span><span class="sxs-lookup"><span data-stu-id="8d9ea-190">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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
