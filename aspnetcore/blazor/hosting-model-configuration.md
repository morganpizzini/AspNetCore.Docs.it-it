---
title: Configurazione Blazor del modello di hosting ASP.NET Core
author: guardrex
description: Informazioni sulla Blazor configurazione del modello di hosting, incluse informazioni Razor su come Razor integrare i componenti in pagine e app MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 17ed43a12643f067da73658bec72400acbe1be43
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772074"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="3e3f7-103">Configurazione del modello di hosting di ASP.NET Core Blazer</span><span class="sxs-lookup"><span data-stu-id="3e3f7-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="3e3f7-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3e3f7-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="3e3f7-105">Questo articolo illustra la configurazione del modello di hosting.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="3e3f7-106">WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="3e3f7-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="3e3f7-107">Ambiente</span><span class="sxs-lookup"><span data-stu-id="3e3f7-107">Environment</span></span>

<span data-ttu-id="3e3f7-108">Quando si esegue un'app localmente, per impostazione predefinita viene impostato lo sviluppo per l'ambiente.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="3e3f7-109">Quando l'app viene pubblicata, per impostazione predefinita viene impostato l'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="3e3f7-110">Un'app webassembly di Blazer ospitata preleva l'ambiente dal server tramite un middleware che comunica l'ambiente al browser aggiungendo l' `blazor-environment` intestazione.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="3e3f7-111">Il valore dell'intestazione è l'ambiente.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-111">The value of the header is the environment.</span></span> <span data-ttu-id="3e3f7-112">L'app di Blazer ospitata e l'app Server condividono lo stesso ambiente.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="3e3f7-113">Per ulteriori informazioni, tra cui come configurare l'ambiente, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="3e3f7-114">Per un'app autonoma in esecuzione in locale, il server `blazor-environment` di sviluppo aggiunge l'intestazione per specificare l'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="3e3f7-115">Per specificare l'ambiente per altri ambienti host, aggiungere l' `blazor-environment` intestazione.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="3e3f7-116">Nell'esempio seguente per IIS aggiungere l'intestazione personalizzata al file *Web. config* pubblicato.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="3e3f7-117">Il file *Web. config* si trova nella cartella *bin/Release/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="3e3f7-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="3e3f7-118">Per usare un file *Web. config* personalizzato per IIS che non viene sovrascritto quando l'app viene pubblicata nella cartella di *pubblicazione* , <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>vedere.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="3e3f7-119">Ottenere l'ambiente dell'app in un componente inserendo `IWebAssemblyHostEnvironment` e leggendo la `Environment` proprietà:</span><span class="sxs-lookup"><span data-stu-id="3e3f7-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="3e3f7-120">Durante l'avvio, `WebAssemblyHostBuilder` espone l' `IWebAssemblyHostEnvironment` oggetto tramite `HostEnvironment` la proprietà, che consente agli sviluppatori di disporre di logica specifica dell'ambiente nel codice:</span><span class="sxs-lookup"><span data-stu-id="3e3f7-120">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="3e3f7-121">I metodi di estensione pratici seguenti consentono di controllare l'ambiente corrente per i nomi di sviluppo, produzione, gestione temporanea e ambiente personalizzato:</span><span class="sxs-lookup"><span data-stu-id="3e3f7-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="3e3f7-122">' Outenvironment ("{nome ambiente}")</span><span class="sxs-lookup"><span data-stu-id="3e3f7-122">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

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

<span data-ttu-id="3e3f7-123">La `IWebAssemblyHostEnvironment.BaseAddress` proprietà può essere utilizzata durante l'avvio quando `NavigationManager` il servizio non è disponibile.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-123">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="3e3f7-124">Configurazione</span><span class="sxs-lookup"><span data-stu-id="3e3f7-124">Configuration</span></span>

<span data-ttu-id="3e3f7-125">La configurazione viene caricata dal webassembly blazer da:</span><span class="sxs-lookup"><span data-stu-id="3e3f7-125">Blazor WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="3e3f7-126">File di impostazioni dell'app per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="3e3f7-126">App settings files by default:</span></span>
  * <span data-ttu-id="3e3f7-127">*Wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="3e3f7-127">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="3e3f7-128">*Wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="3e3f7-128">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="3e3f7-129">Altri [provider di configurazione](xref:fundamentals/configuration/index) registrati dall'app.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-129">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="3e3f7-130">Non tutti i provider sono appropriati per le app webassembly blazer.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-130">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="3e3f7-131">Per verificare quali provider sono supportati per l'assembly WebBlazer, è necessario [chiarire i provider di configurazione per blazer WASM (DotNet/AspNetCore. Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="3e3f7-131">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="3e3f7-132">La configurazione in un'app webassembly blazer è visibile agli utenti.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-132">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="3e3f7-133">**Non archiviare i segreti dell'app o le credenziali nella configurazione.**</span><span class="sxs-lookup"><span data-stu-id="3e3f7-133">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="3e3f7-134">Per ulteriori informazioni sui provider di configurazione, <xref:fundamentals/configuration/index>vedere.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-134">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="3e3f7-135">Configurazione impostazioni app</span><span class="sxs-lookup"><span data-stu-id="3e3f7-135">App settings configuration</span></span>

<span data-ttu-id="3e3f7-136">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="3e3f7-136">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="3e3f7-137">Inserire un' <xref:Microsoft.Extensions.Configuration.IConfiguration> istanza in un componente per accedere ai dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="3e3f7-137">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="3e3f7-138">Configurazione provider</span><span class="sxs-lookup"><span data-stu-id="3e3f7-138">Provider configuration</span></span>

<span data-ttu-id="3e3f7-139">Nell'esempio seguente viene usato <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> un oggetto per fornire la configurazione aggiuntiva:</span><span class="sxs-lookup"><span data-stu-id="3e3f7-139">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="3e3f7-140">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="3e3f7-140">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;

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

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="3e3f7-141">Inserire un' <xref:Microsoft.Extensions.Configuration.IConfiguration> istanza in un componente per accedere ai dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="3e3f7-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

<span data-ttu-id="3e3f7-142">Per leggere altri file di configurazione dalla cartella *wwwroot* alla configurazione, usare un `HttpClient` per ottenere il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-142">To read other configuration files from the *wwwroot* folder into configuration, use an `HttpClient` to obtain the file's content.</span></span> <span data-ttu-id="3e3f7-143">Quando si utilizza questo approccio, la `HttpClient` registrazione del servizio esistente può utilizzare il client locale creato per leggere il file, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="3e3f7-143">When using this approach, the existing `HttpClient` service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="3e3f7-144">*wwwroot/Cars. JSON*:</span><span class="sxs-lookup"><span data-stu-id="3e3f7-144">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="3e3f7-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="3e3f7-145">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddTransient(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

#### <a name="authentication-configuration"></a><span data-ttu-id="3e3f7-146">Configurazione dell'autenticazione</span><span class="sxs-lookup"><span data-stu-id="3e3f7-146">Authentication configuration</span></span>

<span data-ttu-id="3e3f7-147">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="3e3f7-147">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="3e3f7-148">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="3e3f7-148">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="3e3f7-149">Configurazione della registrazione</span><span class="sxs-lookup"><span data-stu-id="3e3f7-149">Logging configuration</span></span>

<span data-ttu-id="3e3f7-150">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="3e3f7-150">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="3e3f7-151">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="3e3f7-151">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="3e3f7-152">Configurazione del generatore host</span><span class="sxs-lookup"><span data-stu-id="3e3f7-152">Host builder configuration</span></span>

<span data-ttu-id="3e3f7-153">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="3e3f7-153">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="3e3f7-154">Configurazione memorizzata nella cache</span><span class="sxs-lookup"><span data-stu-id="3e3f7-154">Cached configuration</span></span>

<span data-ttu-id="3e3f7-155">I file di configurazione vengono memorizzati nella cache per l'uso offline.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-155">Configuration files are cached for offline use.</span></span> <span data-ttu-id="3e3f7-156">Con [le applicazioni Web progressive (PWA)](xref:blazor/progressive-web-app)è possibile aggiornare solo i file di configurazione quando si crea una nuova distribuzione.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-156">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="3e3f7-157">La modifica dei file di configurazione tra le distribuzioni non ha effetto perché:</span><span class="sxs-lookup"><span data-stu-id="3e3f7-157">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="3e3f7-158">Gli utenti dispongono di versioni memorizzate nella cache dei file che continuano a usare.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-158">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="3e3f7-159">I file *service-worker. js* e *service-worker-assets. js* di PWA devono essere ricompilati durante la compilazione, che segnalano all'app la prossima visita online dell'utente che l'app è stata ridistribuita.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-159">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="3e3f7-160">Per ulteriori informazioni sul modo in cui gli aggiornamenti in background vengono gestiti <xref:blazor/progressive-web-app#background-updates>da PWA, vedere.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-160">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="3e3f7-161">Registrazione</span><span class="sxs-lookup"><span data-stu-id="3e3f7-161">Logging</span></span>

<span data-ttu-id="3e3f7-162">Per informazioni sul supporto per la registrazione di webassembly blazer, vedere <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-162">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="3e3f7-163">Server Blazor</span><span class="sxs-lookup"><span data-stu-id="3e3f7-163">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="3e3f7-164">Riflette lo stato di connessione nell'interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="3e3f7-164">Reflect the connection state in the UI</span></span>

<span data-ttu-id="3e3f7-165">Quando il client rileva che la connessione è stata persa, viene visualizzata un'interfaccia utente predefinita quando il client tenta di riconnettersi.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-165">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="3e3f7-166">Se la riconnessione non riesce, all'utente viene offerta l'opzione per riprovare.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-166">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="3e3f7-167">Per personalizzare l'interfaccia utente, definire un elemento con `id` un `components-reconnect-modal` valore di `<body>` nell'oggetto della pagina Razor *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3e3f7-167">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="3e3f7-168">Nella tabella seguente vengono descritte le classi CSS applicate all' `components-reconnect-modal` elemento.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-168">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="3e3f7-169">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="3e3f7-169">CSS class</span></span>                       | <span data-ttu-id="3e3f7-170">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="3e3f7-170">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="3e3f7-171">Connessione persa.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-171">A lost connection.</span></span> <span data-ttu-id="3e3f7-172">È in corso un tentativo di riconnessione del client.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-172">The client is attempting to reconnect.</span></span> <span data-ttu-id="3e3f7-173">Mostra il modale.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-173">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="3e3f7-174">Viene ristabilita una connessione attiva al server.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-174">An active connection is re-established to the server.</span></span> <span data-ttu-id="3e3f7-175">Nascondere il modale.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-175">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="3e3f7-176">Riconnessione non riuscita. probabilmente a causa di un errore di rete.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-176">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="3e3f7-177">Per tentare la riconnessione, `window.Blazor.reconnect()`chiamare.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-177">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="3e3f7-178">Riconnessione rifiutata.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-178">Reconnection rejected.</span></span> <span data-ttu-id="3e3f7-179">Il server è stato raggiunto ma ha rifiutato la connessione e lo stato dell'utente nel server è andato perso.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-179">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="3e3f7-180">Per ricaricare l'app, `location.reload()`chiamare.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-180">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="3e3f7-181">Questo stato di connessione può verificarsi nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="3e3f7-181">This connection state may result when:</span></span><ul><li><span data-ttu-id="3e3f7-182">Si verifica un arresto anomalo del circuito sul lato server.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-182">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="3e3f7-183">Il client viene disconnesso abbastanza a lungo da consentire al server di eliminare lo stato dell'utente.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-183">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="3e3f7-184">Le istanze dei componenti con cui l'utente interagisce sono state eliminate.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-184">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="3e3f7-185">Il server viene riavviato oppure il processo di lavoro dell'app viene riciclato.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-185">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="3e3f7-186">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="3e3f7-186">Render mode</span></span>

<span data-ttu-id="3e3f7-187">Per impostazione predefinita, le app del server Blaze sono configurate per eseguire il prerendering dell'interfaccia utente nel server prima che venga stabilita la connessione client al server.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-187">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="3e3f7-188">Questa impostazione è configurata nella pagina Razor *_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3e3f7-188">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="3e3f7-189">`RenderMode`Configura se il componente:</span><span class="sxs-lookup"><span data-stu-id="3e3f7-189">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="3e3f7-190">Viene preeseguito nella pagina.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-190">Is prerendered into the page.</span></span>
* <span data-ttu-id="3e3f7-191">Viene visualizzato come HTML statico nella pagina o se include le informazioni necessarie per eseguire il bootstrap di un'app Blazer dall'agente utente.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-191">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="3e3f7-192">Description</span><span class="sxs-lookup"><span data-stu-id="3e3f7-192">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="3e3f7-193">Esegue il rendering del componente in HTML statico e include un marcatore Blazor per un'app Server.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-193">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="3e3f7-194">Quando l'agente utente viene avviato, questo marcatore viene usato per il Blazor bootstrap di un'app.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-194">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="3e3f7-195">Esegue il rendering di un marcatore per un' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-195">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="3e3f7-196">L'output del componente non è incluso.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-196">Output from the component isn't included.</span></span> <span data-ttu-id="3e3f7-197">Quando l'agente utente viene avviato, questo marcatore viene usato per il Blazor bootstrap di un'app.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-197">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="3e3f7-198">Esegue il rendering del componente in HTML statico.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-198">Renders the component into static HTML.</span></span> |

<span data-ttu-id="3e3f7-199">Il rendering dei componenti server da una pagina HTML statica non è supportato.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-199">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="3e3f7-200">Configurare il SignalR client per Blazor le app Server</span><span class="sxs-lookup"><span data-stu-id="3e3f7-200">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="3e3f7-201">In alcuni casi, è necessario configurare SignalR il client usato Blazor dalle app Server.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-201">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="3e3f7-202">È ad esempio possibile configurare la SignalR registrazione sul client per diagnosticare un problema di connessione.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-202">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="3e3f7-203">Per configurare il SignalR client nel file *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3e3f7-203">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="3e3f7-204">Aggiungere un `autostart="false"` attributo al `<script>` tag per lo `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-204">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="3e3f7-205">Chiamare `Blazor.start` e passare un oggetto di configurazione che specifichi il SignalR generatore.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-205">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="3e3f7-206">Registrazione</span><span class="sxs-lookup"><span data-stu-id="3e3f7-206">Logging</span></span>

<span data-ttu-id="3e3f7-207">Per informazioni sul Blazor supporto per la registrazione del <xref:fundamentals/logging/index#create-logs-in-blazor>server, vedere.</span><span class="sxs-lookup"><span data-stu-id="3e3f7-207">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
