---
title: Configurazione Blazor del modello di hosting ASP.NET Core
author: guardrex
description: Informazioni sulla Blazor configurazione del modello di hosting, incluse informazioni su come integrare i componenti Razor nelle app Razor Pages e MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: cf5776109368dc7353d7e21bcad1e947561e7eb4
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111058"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="2ed35-103">Configurazione del modello di hosting di ASP.NET Core Blazer</span><span class="sxs-lookup"><span data-stu-id="2ed35-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="2ed35-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2ed35-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="2ed35-105">Questo articolo illustra la configurazione del modello di hosting.</span><span class="sxs-lookup"><span data-stu-id="2ed35-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="2ed35-106">WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="2ed35-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="2ed35-107">Environment</span><span class="sxs-lookup"><span data-stu-id="2ed35-107">Environment</span></span>

<span data-ttu-id="2ed35-108">Quando si esegue un'app localmente, per impostazione predefinita viene impostato lo sviluppo per l'ambiente.</span><span class="sxs-lookup"><span data-stu-id="2ed35-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="2ed35-109">Quando l'app viene pubblicata, per impostazione predefinita viene impostato l'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="2ed35-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="2ed35-110">Un'app webassembly di Blazer ospitata preleva l'ambiente dal server tramite un middleware che comunica l'ambiente al browser aggiungendo l' `blazor-environment` intestazione.</span><span class="sxs-lookup"><span data-stu-id="2ed35-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="2ed35-111">Il valore dell'intestazione è l'ambiente.</span><span class="sxs-lookup"><span data-stu-id="2ed35-111">The value of the header is the environment.</span></span> <span data-ttu-id="2ed35-112">L'app di Blazer ospitata e l'app Server condividono lo stesso ambiente.</span><span class="sxs-lookup"><span data-stu-id="2ed35-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="2ed35-113">Per ulteriori informazioni, tra cui come configurare l'ambiente, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="2ed35-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="2ed35-114">Per un'app autonoma in esecuzione in locale, il server `blazor-environment` di sviluppo aggiunge l'intestazione per specificare l'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="2ed35-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="2ed35-115">Per specificare l'ambiente per altri ambienti host, aggiungere l' `blazor-environment` intestazione.</span><span class="sxs-lookup"><span data-stu-id="2ed35-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="2ed35-116">Nell'esempio seguente per IIS aggiungere l'intestazione personalizzata al file *Web. config* pubblicato.</span><span class="sxs-lookup"><span data-stu-id="2ed35-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="2ed35-117">Il file *Web. config* si trova nella cartella *bin/Release/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="2ed35-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="2ed35-118">Per usare un file *Web. config* personalizzato per IIS che non viene sovrascritto quando l'app viene pubblicata nella cartella di *pubblicazione* , <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>vedere.</span><span class="sxs-lookup"><span data-stu-id="2ed35-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="2ed35-119">Ottenere l'ambiente dell'app in un componente inserendo `IWebAssemblyHostEnvironment` e leggendo la `Environment` proprietà:</span><span class="sxs-lookup"><span data-stu-id="2ed35-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="2ed35-120">Durante l'avvio, `WebAssemblyHostBuilder` espone l' `IWebAssemblyHostEnvironment` oggetto tramite `HostEnvironment` la proprietà, che consente agli sviluppatori di disporre di logica specifica dell'ambiente nel codice:</span><span class="sxs-lookup"><span data-stu-id="2ed35-120">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="2ed35-121">I metodi di estensione pratici seguenti consentono di controllare l'ambiente corrente per i nomi di sviluppo, produzione, gestione temporanea e ambiente personalizzato:</span><span class="sxs-lookup"><span data-stu-id="2ed35-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="2ed35-122">' Outenvironment ("{nome ambiente}")</span><span class="sxs-lookup"><span data-stu-id="2ed35-122">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

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

<span data-ttu-id="2ed35-123">La `IWebAssemblyHostEnvironment.BaseAddress` proprietà può essere utilizzata durante l'avvio quando `NavigationManager` il servizio non è disponibile.</span><span class="sxs-lookup"><span data-stu-id="2ed35-123">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="2ed35-124">Configurazione</span><span class="sxs-lookup"><span data-stu-id="2ed35-124">Configuration</span></span>

<span data-ttu-id="2ed35-125">Blazer webassembly supporta la configurazione da:</span><span class="sxs-lookup"><span data-stu-id="2ed35-125">Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="2ed35-126">Il [provider di configurazione file](xref:fundamentals/configuration/index#file-configuration-provider) per i file di impostazioni dell'app per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="2ed35-126">The [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) for app settings files by default:</span></span>
  * <span data-ttu-id="2ed35-127">*Wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="2ed35-127">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="2ed35-128">*Wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="2ed35-128">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="2ed35-129">Altri [provider di configurazione](xref:fundamentals/configuration/index) registrati dall'app.</span><span class="sxs-lookup"><span data-stu-id="2ed35-129">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span>

> [!WARNING]
> <span data-ttu-id="2ed35-130">La configurazione in un'app webassembly blazer è visibile agli utenti.</span><span class="sxs-lookup"><span data-stu-id="2ed35-130">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="2ed35-131">**Non archiviare i segreti dell'app o le credenziali nella configurazione.**</span><span class="sxs-lookup"><span data-stu-id="2ed35-131">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="2ed35-132">Per ulteriori informazioni sui provider di configurazione, <xref:fundamentals/configuration/index>vedere.</span><span class="sxs-lookup"><span data-stu-id="2ed35-132">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="2ed35-133">Configurazione impostazioni app</span><span class="sxs-lookup"><span data-stu-id="2ed35-133">App settings configuration</span></span>

<span data-ttu-id="2ed35-134">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="2ed35-134">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="2ed35-135">Inserire un' <xref:Microsoft.Extensions.Configuration.IConfiguration> istanza in un componente per accedere ai dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="2ed35-135">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="2ed35-136">Configurazione provider</span><span class="sxs-lookup"><span data-stu-id="2ed35-136">Provider configuration</span></span>

<span data-ttu-id="2ed35-137">Nell'esempio seguente vengono utilizzati <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> un e il [provider di configurazione file](xref:fundamentals/configuration/index#file-configuration-provider) per fornire una configurazione aggiuntiva:</span><span class="sxs-lookup"><span data-stu-id="2ed35-137">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> and the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) to supply additional configuration:</span></span>

<span data-ttu-id="2ed35-138">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="2ed35-138">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

...

builder.Configuration
    .Add(memoryConfig)
    .AddJsonFile("cars.json", optional: false, reloadOnChange: true);
```

<span data-ttu-id="2ed35-139">Inserire un' <xref:Microsoft.Extensions.Configuration.IConfiguration> istanza in un componente per accedere ai dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="2ed35-139">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</p>
    <li>Brand: @Configuration["wheels:brand"]</p>
    <li>Type: @Configuration["wheels:brand:type"]</p>
    <li>Year: @Configuration["wheels:year"]</p>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

#### <a name="authentication-configuration"></a><span data-ttu-id="2ed35-140">Configurazione dell'autenticazione</span><span class="sxs-lookup"><span data-stu-id="2ed35-140">Authentication configuration</span></span>

<span data-ttu-id="2ed35-141">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="2ed35-141">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="2ed35-142">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="2ed35-142">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="2ed35-143">Configurazione della registrazione</span><span class="sxs-lookup"><span data-stu-id="2ed35-143">Logging configuration</span></span>

<span data-ttu-id="2ed35-144">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="2ed35-144">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

<span data-ttu-id="2ed35-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="2ed35-145">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="2ed35-146">Configurazione del generatore host</span><span class="sxs-lookup"><span data-stu-id="2ed35-146">Host builder configuration</span></span>

<span data-ttu-id="2ed35-147">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="2ed35-147">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="2ed35-148">Configurazione memorizzata nella cache</span><span class="sxs-lookup"><span data-stu-id="2ed35-148">Cached configuration</span></span>

<span data-ttu-id="2ed35-149">I file di configurazione vengono memorizzati nella cache per l'uso offline.</span><span class="sxs-lookup"><span data-stu-id="2ed35-149">Configuration files are cached for offline use.</span></span> <span data-ttu-id="2ed35-150">Con [le applicazioni Web progressive (PWA)](xref:blazor/progressive-web-app)è possibile aggiornare solo i file di configurazione quando si crea una nuova distribuzione.</span><span class="sxs-lookup"><span data-stu-id="2ed35-150">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="2ed35-151">La modifica dei file di configurazione tra le distribuzioni non ha effetto perché:</span><span class="sxs-lookup"><span data-stu-id="2ed35-151">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="2ed35-152">Gli utenti dispongono di versioni memorizzate nella cache dei file che continuano a usare.</span><span class="sxs-lookup"><span data-stu-id="2ed35-152">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="2ed35-153">I file *service-worker. js* e *service-worker-assets. js* di PWA devono essere ricompilati durante la compilazione, che segnalano all'app la prossima visita online dell'utente che l'app è stata ridistribuita.</span><span class="sxs-lookup"><span data-stu-id="2ed35-153">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="2ed35-154">Per ulteriori informazioni sul modo in cui gli aggiornamenti in background vengono gestiti <xref:blazor/progressive-web-app#background-updates>da PWA, vedere.</span><span class="sxs-lookup"><span data-stu-id="2ed35-154">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="2ed35-155">Registrazione</span><span class="sxs-lookup"><span data-stu-id="2ed35-155">Logging</span></span>

<span data-ttu-id="2ed35-156">Per informazioni sul supporto per la registrazione di webassembly blazer, vedere <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="2ed35-156">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="2ed35-157">Server Blazor</span><span class="sxs-lookup"><span data-stu-id="2ed35-157">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="2ed35-158">Riflette lo stato di connessione nell'interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="2ed35-158">Reflect the connection state in the UI</span></span>

<span data-ttu-id="2ed35-159">Quando il client rileva che la connessione è stata persa, viene visualizzata un'interfaccia utente predefinita quando il client tenta di riconnettersi.</span><span class="sxs-lookup"><span data-stu-id="2ed35-159">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="2ed35-160">Se la riconnessione non riesce, all'utente viene offerta l'opzione per riprovare.</span><span class="sxs-lookup"><span data-stu-id="2ed35-160">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="2ed35-161">Per personalizzare l'interfaccia utente, definire un elemento con `id` un `components-reconnect-modal` valore di `<body>` nell'oggetto della pagina Razor *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="2ed35-161">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="2ed35-162">Nella tabella seguente vengono descritte le classi CSS applicate all' `components-reconnect-modal` elemento.</span><span class="sxs-lookup"><span data-stu-id="2ed35-162">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="2ed35-163">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="2ed35-163">CSS class</span></span>                       | <span data-ttu-id="2ed35-164">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="2ed35-164">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="2ed35-165">Connessione persa.</span><span class="sxs-lookup"><span data-stu-id="2ed35-165">A lost connection.</span></span> <span data-ttu-id="2ed35-166">È in corso un tentativo di riconnessione del client.</span><span class="sxs-lookup"><span data-stu-id="2ed35-166">The client is attempting to reconnect.</span></span> <span data-ttu-id="2ed35-167">Mostra il modale.</span><span class="sxs-lookup"><span data-stu-id="2ed35-167">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="2ed35-168">Viene ristabilita una connessione attiva al server.</span><span class="sxs-lookup"><span data-stu-id="2ed35-168">An active connection is re-established to the server.</span></span> <span data-ttu-id="2ed35-169">Nascondere il modale.</span><span class="sxs-lookup"><span data-stu-id="2ed35-169">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="2ed35-170">Riconnessione non riuscita. probabilmente a causa di un errore di rete.</span><span class="sxs-lookup"><span data-stu-id="2ed35-170">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="2ed35-171">Per tentare la riconnessione, `window.Blazor.reconnect()`chiamare.</span><span class="sxs-lookup"><span data-stu-id="2ed35-171">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="2ed35-172">Riconnessione rifiutata.</span><span class="sxs-lookup"><span data-stu-id="2ed35-172">Reconnection rejected.</span></span> <span data-ttu-id="2ed35-173">Il server è stato raggiunto ma ha rifiutato la connessione e lo stato dell'utente nel server è andato perso.</span><span class="sxs-lookup"><span data-stu-id="2ed35-173">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="2ed35-174">Per ricaricare l'app, `location.reload()`chiamare.</span><span class="sxs-lookup"><span data-stu-id="2ed35-174">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="2ed35-175">Questo stato di connessione può verificarsi nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="2ed35-175">This connection state may result when:</span></span><ul><li><span data-ttu-id="2ed35-176">Si verifica un arresto anomalo del circuito sul lato server.</span><span class="sxs-lookup"><span data-stu-id="2ed35-176">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="2ed35-177">Il client viene disconnesso abbastanza a lungo da consentire al server di eliminare lo stato dell'utente.</span><span class="sxs-lookup"><span data-stu-id="2ed35-177">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="2ed35-178">Le istanze dei componenti con cui l'utente interagisce sono state eliminate.</span><span class="sxs-lookup"><span data-stu-id="2ed35-178">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="2ed35-179">Il server viene riavviato oppure il processo di lavoro dell'app viene riciclato.</span><span class="sxs-lookup"><span data-stu-id="2ed35-179">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="2ed35-180">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="2ed35-180">Render mode</span></span>

<span data-ttu-id="2ed35-181">Per impostazione predefinita, le app del server Blaze sono configurate per eseguire il prerendering dell'interfaccia utente nel server prima che venga stabilita la connessione client al server.</span><span class="sxs-lookup"><span data-stu-id="2ed35-181">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="2ed35-182">Questa impostazione è configurata nella pagina Razor *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="2ed35-182">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="2ed35-183">`RenderMode`Configura se il componente:</span><span class="sxs-lookup"><span data-stu-id="2ed35-183">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="2ed35-184">Viene preeseguito nella pagina.</span><span class="sxs-lookup"><span data-stu-id="2ed35-184">Is prerendered into the page.</span></span>
* <span data-ttu-id="2ed35-185">Viene visualizzato come HTML statico nella pagina o se include le informazioni necessarie per eseguire il bootstrap di un'app Blazer dall'agente utente.</span><span class="sxs-lookup"><span data-stu-id="2ed35-185">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="2ed35-186">Description</span><span class="sxs-lookup"><span data-stu-id="2ed35-186">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="2ed35-187">Esegue il rendering del componente in HTML statico e include un marcatore Blazor per un'app Server.</span><span class="sxs-lookup"><span data-stu-id="2ed35-187">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="2ed35-188">Quando l'agente utente viene avviato, questo marcatore viene usato per il Blazor bootstrap di un'app.</span><span class="sxs-lookup"><span data-stu-id="2ed35-188">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="2ed35-189">Esegue il rendering di un marcatore per un' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="2ed35-189">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="2ed35-190">L'output del componente non è incluso.</span><span class="sxs-lookup"><span data-stu-id="2ed35-190">Output from the component isn't included.</span></span> <span data-ttu-id="2ed35-191">Quando l'agente utente viene avviato, questo marcatore viene usato per il Blazor bootstrap di un'app.</span><span class="sxs-lookup"><span data-stu-id="2ed35-191">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="2ed35-192">Esegue il rendering del componente in HTML statico.</span><span class="sxs-lookup"><span data-stu-id="2ed35-192">Renders the component into static HTML.</span></span> |

<span data-ttu-id="2ed35-193">Il rendering dei componenti server da una pagina HTML statica non è supportato.</span><span class="sxs-lookup"><span data-stu-id="2ed35-193">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="2ed35-194">Eseguire il rendering di componenti interattivi con stato da pagine e visualizzazioni Razor</span><span class="sxs-lookup"><span data-stu-id="2ed35-194">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="2ed35-195">I componenti interattivi con stato possono essere aggiunti a una pagina o a una visualizzazione Razor.</span><span class="sxs-lookup"><span data-stu-id="2ed35-195">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="2ed35-196">Quando viene eseguito il rendering della pagina o della visualizzazione:</span><span class="sxs-lookup"><span data-stu-id="2ed35-196">When the page or view renders:</span></span>

* <span data-ttu-id="2ed35-197">Il componente viene preeseguito con la pagina o la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="2ed35-197">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="2ed35-198">Lo stato iniziale del componente utilizzato per il prerendering viene perso.</span><span class="sxs-lookup"><span data-stu-id="2ed35-198">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="2ed35-199">Quando viene stabilita la connessione, SignalR viene creato il nuovo stato del componente.</span><span class="sxs-lookup"><span data-stu-id="2ed35-199">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="2ed35-200">La pagina Razor seguente esegue il rendering `Counter` di un componente:</span><span class="sxs-lookup"><span data-stu-id="2ed35-200">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="2ed35-201">Eseguire il rendering di componenti non interattivi da pagine e visualizzazioni Razor</span><span class="sxs-lookup"><span data-stu-id="2ed35-201">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="2ed35-202">Nella pagina Razor seguente il `Counter` componente viene sottoposto a rendering statico con un valore iniziale specificato usando un form:</span><span class="sxs-lookup"><span data-stu-id="2ed35-202">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

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

<span data-ttu-id="2ed35-203">Poiché `MyComponent` viene sottoposto a rendering statico, il componente non può essere interattivo.</span><span class="sxs-lookup"><span data-stu-id="2ed35-203">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="2ed35-204">Configurare il SignalR client per Blazor le app Server</span><span class="sxs-lookup"><span data-stu-id="2ed35-204">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="2ed35-205">In alcuni casi, è necessario configurare SignalR il client usato Blazor dalle app Server.</span><span class="sxs-lookup"><span data-stu-id="2ed35-205">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="2ed35-206">È ad esempio possibile configurare la SignalR registrazione sul client per diagnosticare un problema di connessione.</span><span class="sxs-lookup"><span data-stu-id="2ed35-206">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="2ed35-207">Per configurare il SignalR client nel file *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="2ed35-207">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="2ed35-208">Aggiungere un `autostart="false"` attributo al `<script>` tag per lo `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="2ed35-208">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="2ed35-209">Chiamare `Blazor.start` e passare un oggetto di configurazione che specifichi il SignalR generatore.</span><span class="sxs-lookup"><span data-stu-id="2ed35-209">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="2ed35-210">Registrazione</span><span class="sxs-lookup"><span data-stu-id="2ed35-210">Logging</span></span>

<span data-ttu-id="2ed35-211">Per informazioni sul Blazor supporto per la registrazione del <xref:fundamentals/logging/index#create-logs-in-blazor>server, vedere.</span><span class="sxs-lookup"><span data-stu-id="2ed35-211">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
