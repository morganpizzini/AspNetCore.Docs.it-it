---
title: BlazorConfigurazione del modello di hosting ASP.NET Core
author: guardrex
description: Informazioni sulla Blazor configurazione del modello di hosting, incluse informazioni su come integrare i Razor componenti in Razor pagine e app MVC.
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
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 3cef67806ce0e2e045122bdc962e93795be68572
ms.sourcegitcommit: 6371114344a5f4fbc5d4a119b0be1ad3762e0216
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84679579"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="d07b2-103">BlazorConfigurazione del modello di hosting ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d07b2-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="d07b2-104">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d07b2-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d07b2-105">Questo articolo illustra la configurazione del modello di hosting.</span><span class="sxs-lookup"><span data-stu-id="d07b2-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="d07b2-106">Webassembly</span><span class="sxs-lookup"><span data-stu-id="d07b2-106"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="d07b2-107">Ambiente</span><span class="sxs-lookup"><span data-stu-id="d07b2-107">Environment</span></span>

<span data-ttu-id="d07b2-108">Quando si esegue un'app localmente, per impostazione predefinita viene impostato lo sviluppo per l'ambiente.</span><span class="sxs-lookup"><span data-stu-id="d07b2-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="d07b2-109">Quando l'app viene pubblicata, per impostazione predefinita viene impostato l'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="d07b2-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="d07b2-110">Un' Blazor app webassembly ospitata preleva l'ambiente dal server tramite un middleware che comunica l'ambiente al browser aggiungendo l' `blazor-environment` intestazione.</span><span class="sxs-lookup"><span data-stu-id="d07b2-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="d07b2-111">Il valore dell'intestazione è l'ambiente.</span><span class="sxs-lookup"><span data-stu-id="d07b2-111">The value of the header is the environment.</span></span> <span data-ttu-id="d07b2-112">L'app ospitata Blazor e l'app Server condividono lo stesso ambiente.</span><span class="sxs-lookup"><span data-stu-id="d07b2-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="d07b2-113">Per ulteriori informazioni, tra cui come configurare l'ambiente, vedere <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="d07b2-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d07b2-114">Per un'app autonoma in esecuzione in locale, il server di sviluppo aggiunge l' `blazor-environment` intestazione per specificare l'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="d07b2-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="d07b2-115">Per specificare l'ambiente per altri ambienti host, aggiungere l' `blazor-environment` intestazione.</span><span class="sxs-lookup"><span data-stu-id="d07b2-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="d07b2-116">Nell'esempio seguente per IIS aggiungere l'intestazione personalizzata al file del *web.config* pubblicato.</span><span class="sxs-lookup"><span data-stu-id="d07b2-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="d07b2-117">Il file di *web.config* si trova nella cartella *bin/Release/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="d07b2-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="d07b2-118">Per usare un file di *web.config* personalizzato per IIS che non viene sovrascritto quando l'app viene pubblicata nella cartella di *pubblicazione* , vedere <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="d07b2-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="d07b2-119">Ottenere l'ambiente dell'app in un componente inserendo <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> e leggendo la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> proprietà:</span><span class="sxs-lookup"><span data-stu-id="d07b2-119">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="d07b2-120">Durante l'avvio, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> espone l'oggetto <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> tramite la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> proprietà, che consente agli sviluppatori di disporre di logica specifica dell'ambiente nel codice:</span><span class="sxs-lookup"><span data-stu-id="d07b2-120">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="d07b2-121">I metodi di estensione pratici seguenti consentono di controllare l'ambiente corrente per i nomi di sviluppo, produzione, gestione temporanea e ambiente personalizzato:</span><span class="sxs-lookup"><span data-stu-id="d07b2-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

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

<span data-ttu-id="d07b2-122">La <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> proprietà può essere utilizzata durante l'avvio quando il <xref:Microsoft.AspNetCore.Components.NavigationManager> servizio non è disponibile.</span><span class="sxs-lookup"><span data-stu-id="d07b2-122">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="d07b2-123">Configurazione</span><span class="sxs-lookup"><span data-stu-id="d07b2-123">Configuration</span></span>

Blazor<span data-ttu-id="d07b2-124">Webassembly carica la configurazione da:</span><span class="sxs-lookup"><span data-stu-id="d07b2-124"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="d07b2-125">File di impostazioni dell'app per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="d07b2-125">App settings files by default:</span></span>
  * <span data-ttu-id="d07b2-126">*Wwwroot/appsettings.js*</span><span class="sxs-lookup"><span data-stu-id="d07b2-126">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="d07b2-127">*Wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="d07b2-127">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="d07b2-128">Altri [provider di configurazione](xref:fundamentals/configuration/index) registrati dall'app.</span><span class="sxs-lookup"><span data-stu-id="d07b2-128">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="d07b2-129">Non tutti i provider sono appropriati per le Blazor app webassembly.</span><span class="sxs-lookup"><span data-stu-id="d07b2-129">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="d07b2-130">Per verificare quali provider sono supportati per Blazor webassembly, è necessario [chiarire i provider di configurazione per Blazor WASM (DotNet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="d07b2-130">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="d07b2-131">La configurazione in un' Blazor app webassembly è visibile agli utenti.</span><span class="sxs-lookup"><span data-stu-id="d07b2-131">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="d07b2-132">**Non archiviare i segreti dell'app o le credenziali nella configurazione.**</span><span class="sxs-lookup"><span data-stu-id="d07b2-132">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="d07b2-133">Per ulteriori informazioni sui provider di configurazione, vedere <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="d07b2-133">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="d07b2-134">Configurazione impostazioni app</span><span class="sxs-lookup"><span data-stu-id="d07b2-134">App settings configuration</span></span>

<span data-ttu-id="d07b2-135">*wwwroot/appsettings.jsin*:</span><span class="sxs-lookup"><span data-stu-id="d07b2-135">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="d07b2-136">Inserire un' <xref:Microsoft.Extensions.Configuration.IConfiguration> istanza in un componente per accedere ai dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="d07b2-136">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="d07b2-137">Configurazione provider</span><span class="sxs-lookup"><span data-stu-id="d07b2-137">Provider configuration</span></span>

<span data-ttu-id="d07b2-138">Nell'esempio seguente viene usato un oggetto <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> per fornire la configurazione aggiuntiva:</span><span class="sxs-lookup"><span data-stu-id="d07b2-138">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="d07b2-139">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="d07b2-139">`Program.Main`:</span></span>

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

<span data-ttu-id="d07b2-140">Inserire un' <xref:Microsoft.Extensions.Configuration.IConfiguration> istanza in un componente per accedere ai dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="d07b2-140">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="d07b2-141">Per leggere altri file di configurazione dalla cartella *wwwroot* alla configurazione, usare un <xref:System.Net.Http.HttpClient> per ottenere il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="d07b2-141">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="d07b2-142">Quando si utilizza questo approccio, la <xref:System.Net.Http.HttpClient> registrazione del servizio esistente può utilizzare il client locale creato per leggere il file, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="d07b2-142">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="d07b2-143">*wwwroot/cars.jsin*:</span><span class="sxs-lookup"><span data-stu-id="d07b2-143">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="d07b2-144">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="d07b2-144">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="d07b2-145">Configurazione dell'autenticazione</span><span class="sxs-lookup"><span data-stu-id="d07b2-145">Authentication configuration</span></span>

<span data-ttu-id="d07b2-146">*wwwroot/appsettings.jsin*:</span><span class="sxs-lookup"><span data-stu-id="d07b2-146">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="d07b2-147">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="d07b2-147">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

#### <a name="logging-configuration"></a><span data-ttu-id="d07b2-148">Configurazione della registrazione</span><span class="sxs-lookup"><span data-stu-id="d07b2-148">Logging configuration</span></span>

<span data-ttu-id="d07b2-149">Aggiungere un riferimento al pacchetto per [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span><span class="sxs-lookup"><span data-stu-id="d07b2-149">Add a package reference for [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="d07b2-150">*wwwroot/appsettings.jsin*:</span><span class="sxs-lookup"><span data-stu-id="d07b2-150">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="d07b2-151">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="d07b2-151">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="d07b2-152">Configurazione del generatore host</span><span class="sxs-lookup"><span data-stu-id="d07b2-152">Host builder configuration</span></span>

<span data-ttu-id="d07b2-153">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="d07b2-153">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="d07b2-154">Configurazione memorizzata nella cache</span><span class="sxs-lookup"><span data-stu-id="d07b2-154">Cached configuration</span></span>

<span data-ttu-id="d07b2-155">I file di configurazione vengono memorizzati nella cache per l'uso offline.</span><span class="sxs-lookup"><span data-stu-id="d07b2-155">Configuration files are cached for offline use.</span></span> <span data-ttu-id="d07b2-156">Con [le applicazioni Web progressive (PWA)](xref:blazor/progressive-web-app)è possibile aggiornare solo i file di configurazione quando si crea una nuova distribuzione.</span><span class="sxs-lookup"><span data-stu-id="d07b2-156">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="d07b2-157">La modifica dei file di configurazione tra le distribuzioni non ha effetto perché:</span><span class="sxs-lookup"><span data-stu-id="d07b2-157">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="d07b2-158">Gli utenti dispongono di versioni memorizzate nella cache dei file che continuano a usare.</span><span class="sxs-lookup"><span data-stu-id="d07b2-158">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="d07b2-159">I file di *service-worker.js* e *service-worker-assets.js* di PWA devono essere ricompilati durante la compilazione, che segnalano all'app la prossima visita online dell'utente che l'app è stata ridistribuita.</span><span class="sxs-lookup"><span data-stu-id="d07b2-159">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="d07b2-160">Per ulteriori informazioni sul modo in cui gli aggiornamenti in background vengono gestiti da PWA, vedere <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="d07b2-160">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="d07b2-161">Registrazione</span><span class="sxs-lookup"><span data-stu-id="d07b2-161">Logging</span></span>

<span data-ttu-id="d07b2-162">Per informazioni sul Blazor supporto della registrazione di webassembly, vedere <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="d07b2-162">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalR<span data-ttu-id="d07b2-163">negoziazione tra le origini per l'autenticazione</span><span class="sxs-lookup"><span data-stu-id="d07b2-163"> cross-origin negotiation for authentication</span></span>

<span data-ttu-id="d07b2-164">Per configurare SignalR il client sottostante per l'invio di credenziali, ad esempio cookie o intestazioni di autenticazione http:</span><span class="sxs-lookup"><span data-stu-id="d07b2-164">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="d07b2-165">Usare <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> per impostare <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> le richieste di [recupero](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) tra origini:</span><span class="sxs-lookup"><span data-stu-id="d07b2-165">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="d07b2-166">Assegnare <xref:System.Net.Http.HttpMessageHandler> a l' <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opzione:</span><span class="sxs-lookup"><span data-stu-id="d07b2-166">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var client = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessagHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="d07b2-167">Per altre informazioni, vedere <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="d07b2-167">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="d07b2-168">Server</span><span class="sxs-lookup"><span data-stu-id="d07b2-168"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="d07b2-169">Riflette lo stato di connessione nell'interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="d07b2-169">Reflect the connection state in the UI</span></span>

<span data-ttu-id="d07b2-170">Quando il client rileva che la connessione è stata persa, viene visualizzata un'interfaccia utente predefinita quando il client tenta di riconnettersi.</span><span class="sxs-lookup"><span data-stu-id="d07b2-170">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="d07b2-171">Se la riconnessione non riesce, all'utente viene offerta l'opzione per riprovare.</span><span class="sxs-lookup"><span data-stu-id="d07b2-171">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="d07b2-172">Per personalizzare l'interfaccia utente, definire un elemento con un valore `id` di `components-reconnect-modal` nell'oggetto `<body>` della pagina *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="d07b2-172">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="d07b2-173">Nella tabella seguente vengono descritte le classi CSS applicate all' `components-reconnect-modal` elemento.</span><span class="sxs-lookup"><span data-stu-id="d07b2-173">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="d07b2-174">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="d07b2-174">CSS class</span></span>                       | <span data-ttu-id="d07b2-175">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="d07b2-175">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="d07b2-176">Connessione persa.</span><span class="sxs-lookup"><span data-stu-id="d07b2-176">A lost connection.</span></span> <span data-ttu-id="d07b2-177">È in corso un tentativo di riconnessione del client.</span><span class="sxs-lookup"><span data-stu-id="d07b2-177">The client is attempting to reconnect.</span></span> <span data-ttu-id="d07b2-178">Mostra il modale.</span><span class="sxs-lookup"><span data-stu-id="d07b2-178">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="d07b2-179">Viene ristabilita una connessione attiva al server.</span><span class="sxs-lookup"><span data-stu-id="d07b2-179">An active connection is re-established to the server.</span></span> <span data-ttu-id="d07b2-180">Nascondere il modale.</span><span class="sxs-lookup"><span data-stu-id="d07b2-180">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="d07b2-181">Riconnessione non riuscita. probabilmente a causa di un errore di rete.</span><span class="sxs-lookup"><span data-stu-id="d07b2-181">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="d07b2-182">Per tentare la riconnessione, chiamare `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="d07b2-182">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="d07b2-183">Riconnessione rifiutata.</span><span class="sxs-lookup"><span data-stu-id="d07b2-183">Reconnection rejected.</span></span> <span data-ttu-id="d07b2-184">Il server è stato raggiunto ma ha rifiutato la connessione e lo stato dell'utente nel server è andato perso.</span><span class="sxs-lookup"><span data-stu-id="d07b2-184">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="d07b2-185">Per ricaricare l'app, chiamare `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="d07b2-185">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="d07b2-186">Questo stato di connessione può verificarsi nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="d07b2-186">This connection state may result when:</span></span><ul><li><span data-ttu-id="d07b2-187">Si verifica un arresto anomalo del circuito sul lato server.</span><span class="sxs-lookup"><span data-stu-id="d07b2-187">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="d07b2-188">Il client viene disconnesso abbastanza a lungo da consentire al server di eliminare lo stato dell'utente.</span><span class="sxs-lookup"><span data-stu-id="d07b2-188">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="d07b2-189">Le istanze dei componenti con cui l'utente interagisce sono state eliminate.</span><span class="sxs-lookup"><span data-stu-id="d07b2-189">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="d07b2-190">Il server viene riavviato oppure il processo di lavoro dell'app viene riciclato.</span><span class="sxs-lookup"><span data-stu-id="d07b2-190">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="d07b2-191">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="d07b2-191">Render mode</span></span>

Blazor<span data-ttu-id="d07b2-192">Per impostazione predefinita, le app Server sono impostate per eseguire il prerendering dell'interfaccia utente nel server prima che venga stabilita la connessione client al server.</span><span class="sxs-lookup"><span data-stu-id="d07b2-192"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="d07b2-193">Questa impostazione è configurata nella pagina *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="d07b2-193">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="d07b2-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>Configura se il componente:</span><span class="sxs-lookup"><span data-stu-id="d07b2-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="d07b2-195">Viene preeseguito nella pagina.</span><span class="sxs-lookup"><span data-stu-id="d07b2-195">Is prerendered into the page.</span></span>
* <span data-ttu-id="d07b2-196">Viene sottoposto a rendering come HTML statico nella pagina o se include le informazioni necessarie per il bootstrap di un' Blazor app dall'agente utente.</span><span class="sxs-lookup"><span data-stu-id="d07b2-196">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="d07b2-197">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d07b2-197">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="d07b2-198">Esegue il rendering del componente in HTML statico e include un marcatore per un' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="d07b2-198">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="d07b2-199">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="d07b2-199">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="d07b2-200">Esegue il rendering di un marcatore per un' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="d07b2-200">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="d07b2-201">L'output del componente non è incluso.</span><span class="sxs-lookup"><span data-stu-id="d07b2-201">Output from the component isn't included.</span></span> <span data-ttu-id="d07b2-202">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="d07b2-202">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="d07b2-203">Esegue il rendering del componente in HTML statico.</span><span class="sxs-lookup"><span data-stu-id="d07b2-203">Renders the component into static HTML.</span></span> |

<span data-ttu-id="d07b2-204">Il rendering dei componenti server da una pagina HTML statica non è supportato.</span><span class="sxs-lookup"><span data-stu-id="d07b2-204">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="d07b2-205">Configurare il SignalR client per le Blazor app Server</span><span class="sxs-lookup"><span data-stu-id="d07b2-205">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="d07b2-206">In alcuni casi, è necessario configurare il SignalR client usato dalle Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="d07b2-206">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="d07b2-207">È ad esempio possibile configurare la registrazione sul SignalR client per diagnosticare un problema di connessione.</span><span class="sxs-lookup"><span data-stu-id="d07b2-207">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="d07b2-208">Per configurare il SignalR client nel file *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="d07b2-208">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="d07b2-209">Aggiungere un `autostart="false"` attributo al `<script>` tag per lo `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="d07b2-209">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="d07b2-210">Chiamare `Blazor.start` e passare un oggetto di configurazione che specifichi il SignalR generatore.</span><span class="sxs-lookup"><span data-stu-id="d07b2-210">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="d07b2-211">Registrazione</span><span class="sxs-lookup"><span data-stu-id="d07b2-211">Logging</span></span>

<span data-ttu-id="d07b2-212">Per informazioni sul Blazor supporto per la registrazione del server, vedere <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="d07b2-212">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
