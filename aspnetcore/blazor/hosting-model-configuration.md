---
<span data-ttu-id="e6f8a-101">title:' ASP.NET Core Blazor configurazione del modello di hosting ' Author: guardrex Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-101">title: 'ASP.NET Core Blazor hosting model configuration' author: guardrex description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e6f8a-102">monikerRange:' >= aspnetcore-3,1' ms. Author: Riande ms. Custom: MVC ms. Date: 05/28/2020 no-loc:</span><span class="sxs-lookup"><span data-stu-id="e6f8a-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/28/2020 no-loc:</span></span>
- <span data-ttu-id="e6f8a-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e6f8a-103">'Blazor'</span></span>
- <span data-ttu-id="e6f8a-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e6f8a-104">'Identity'</span></span>
- <span data-ttu-id="e6f8a-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e6f8a-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="e6f8a-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e6f8a-106">'Razor'</span></span>
- <span data-ttu-id="e6f8a-107">' SignalR ' UID: blazer/hosting-Model-Configuration</span><span class="sxs-lookup"><span data-stu-id="e6f8a-107">'SignalR' uid: blazor/hosting-model-configuration</span></span>

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="e6f8a-108">BlazorConfigurazione del modello di hosting ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e6f8a-108">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="e6f8a-109">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e6f8a-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e6f8a-110">Questo articolo illustra la configurazione del modello di hosting.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-110">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="e6f8a-111">Webassembly</span><span class="sxs-lookup"><span data-stu-id="e6f8a-111"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="e6f8a-112">Ambiente</span><span class="sxs-lookup"><span data-stu-id="e6f8a-112">Environment</span></span>

<span data-ttu-id="e6f8a-113">Quando si esegue un'app localmente, per impostazione predefinita viene impostato lo sviluppo per l'ambiente.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-113">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="e6f8a-114">Quando l'app viene pubblicata, per impostazione predefinita viene impostato l'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-114">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="e6f8a-115">Un' Blazor app webassembly ospitata preleva l'ambiente dal server tramite un middleware che comunica l'ambiente al browser aggiungendo l' `blazor-environment` intestazione.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="e6f8a-116">Il valore dell'intestazione è l'ambiente.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-116">The value of the header is the environment.</span></span> <span data-ttu-id="e6f8a-117">L'app ospitata Blazor e l'app Server condividono lo stesso ambiente.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-117">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="e6f8a-118">Per ulteriori informazioni, tra cui come configurare l'ambiente, vedere <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="e6f8a-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="e6f8a-119">Per un'app autonoma in esecuzione in locale, il server di sviluppo aggiunge l' `blazor-environment` intestazione per specificare l'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="e6f8a-120">Per specificare l'ambiente per altri ambienti host, aggiungere l' `blazor-environment` intestazione.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="e6f8a-121">Nell'esempio seguente per IIS aggiungere l'intestazione personalizzata al file *Web. config* pubblicato.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="e6f8a-122">Il file *Web. config* si trova nella cartella *bin/Release/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="e6f8a-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="e6f8a-123">Per usare un file *Web. config* personalizzato per IIS che non viene sovrascritto quando l'app viene pubblicata nella cartella di *pubblicazione* , vedere <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="e6f8a-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="e6f8a-124">Ottenere l'ambiente dell'app in un componente inserendo <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> e leggendo la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> proprietà:</span><span class="sxs-lookup"><span data-stu-id="e6f8a-124">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="e6f8a-125">Durante l'avvio, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> espone l'oggetto <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> tramite la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> proprietà, che consente agli sviluppatori di disporre di logica specifica dell'ambiente nel codice:</span><span class="sxs-lookup"><span data-stu-id="e6f8a-125">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="e6f8a-126">I metodi di estensione pratici seguenti consentono di controllare l'ambiente corrente per i nomi di sviluppo, produzione, gestione temporanea e ambiente personalizzato:</span><span class="sxs-lookup"><span data-stu-id="e6f8a-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

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

<span data-ttu-id="e6f8a-127">La <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> proprietà può essere utilizzata durante l'avvio quando il <xref:Microsoft.AspNetCore.Components.NavigationManager> servizio non è disponibile.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-127">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="e6f8a-128">Configurazione</span><span class="sxs-lookup"><span data-stu-id="e6f8a-128">Configuration</span></span>

Blazor<span data-ttu-id="e6f8a-129">Webassembly carica la configurazione da:</span><span class="sxs-lookup"><span data-stu-id="e6f8a-129"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="e6f8a-130">File di impostazioni dell'app per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="e6f8a-130">App settings files by default:</span></span>
  * <span data-ttu-id="e6f8a-131">*Wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="e6f8a-131">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="e6f8a-132">*Wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="e6f8a-132">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="e6f8a-133">Altri [provider di configurazione](xref:fundamentals/configuration/index) registrati dall'app.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-133">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="e6f8a-134">Non tutti i provider sono appropriati per le Blazor app webassembly.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-134">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="e6f8a-135">Per verificare quali provider sono supportati per Blazor webassembly, è necessario [chiarire i provider di configurazione per Blazor WASM (DotNet/AspNetCore. docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="e6f8a-135">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="e6f8a-136">La configurazione in un' Blazor app webassembly è visibile agli utenti.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-136">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="e6f8a-137">**Non archiviare i segreti dell'app o le credenziali nella configurazione.**</span><span class="sxs-lookup"><span data-stu-id="e6f8a-137">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="e6f8a-138">Per ulteriori informazioni sui provider di configurazione, vedere <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="e6f8a-138">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="e6f8a-139">Configurazione impostazioni app</span><span class="sxs-lookup"><span data-stu-id="e6f8a-139">App settings configuration</span></span>

<span data-ttu-id="e6f8a-140">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e6f8a-140">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="e6f8a-141">Inserire un' <xref:Microsoft.Extensions.Configuration.IConfiguration> istanza in un componente per accedere ai dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="e6f8a-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="e6f8a-142">Configurazione provider</span><span class="sxs-lookup"><span data-stu-id="e6f8a-142">Provider configuration</span></span>

<span data-ttu-id="e6f8a-143">Nell'esempio seguente viene usato un oggetto <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> per fornire la configurazione aggiuntiva:</span><span class="sxs-lookup"><span data-stu-id="e6f8a-143">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="e6f8a-144">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e6f8a-144">`Program.Main`:</span></span>

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

<span data-ttu-id="e6f8a-145">Inserire un' <xref:Microsoft.Extensions.Configuration.IConfiguration> istanza in un componente per accedere ai dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="e6f8a-145">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="e6f8a-146">Per leggere altri file di configurazione dalla cartella *wwwroot* alla configurazione, usare un <xref:System.Net.Http.HttpClient> per ottenere il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-146">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="e6f8a-147">Quando si utilizza questo approccio, la <xref:System.Net.Http.HttpClient> registrazione del servizio esistente può utilizzare il client locale creato per leggere il file, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="e6f8a-147">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="e6f8a-148">*wwwroot/Cars. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e6f8a-148">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="e6f8a-149">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e6f8a-149">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="e6f8a-150">Configurazione dell'autenticazione</span><span class="sxs-lookup"><span data-stu-id="e6f8a-150">Authentication configuration</span></span>

<span data-ttu-id="e6f8a-151">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e6f8a-151">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="e6f8a-152">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e6f8a-152">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions);
```

#### <a name="logging-configuration"></a><span data-ttu-id="e6f8a-153">Configurazione della registrazione</span><span class="sxs-lookup"><span data-stu-id="e6f8a-153">Logging configuration</span></span>

<span data-ttu-id="e6f8a-154">Aggiungere un riferimento al pacchetto per [Microsoft. Extensions. Logging. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span><span class="sxs-lookup"><span data-stu-id="e6f8a-154">Add a package reference for [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="e6f8a-155">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e6f8a-155">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="e6f8a-156">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e6f8a-156">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="e6f8a-157">Configurazione del generatore host</span><span class="sxs-lookup"><span data-stu-id="e6f8a-157">Host builder configuration</span></span>

<span data-ttu-id="e6f8a-158">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e6f8a-158">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="e6f8a-159">Configurazione memorizzata nella cache</span><span class="sxs-lookup"><span data-stu-id="e6f8a-159">Cached configuration</span></span>

<span data-ttu-id="e6f8a-160">I file di configurazione vengono memorizzati nella cache per l'uso offline.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-160">Configuration files are cached for offline use.</span></span> <span data-ttu-id="e6f8a-161">Con [le applicazioni Web progressive (PWA)](xref:blazor/progressive-web-app)è possibile aggiornare solo i file di configurazione quando si crea una nuova distribuzione.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-161">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="e6f8a-162">La modifica dei file di configurazione tra le distribuzioni non ha effetto perché:</span><span class="sxs-lookup"><span data-stu-id="e6f8a-162">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="e6f8a-163">Gli utenti dispongono di versioni memorizzate nella cache dei file che continuano a usare.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-163">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="e6f8a-164">I file *service-worker. js* e *service-worker-assets. js* di PWA devono essere ricompilati durante la compilazione, che segnalano all'app la prossima visita online dell'utente che l'app è stata ridistribuita.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-164">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="e6f8a-165">Per ulteriori informazioni sul modo in cui gli aggiornamenti in background vengono gestiti da PWA, vedere <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="e6f8a-165">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="e6f8a-166">Registrazione</span><span class="sxs-lookup"><span data-stu-id="e6f8a-166">Logging</span></span>

<span data-ttu-id="e6f8a-167">Per informazioni sul Blazor supporto della registrazione di webassembly, vedere <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="e6f8a-167">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="e6f8a-168">Server</span><span class="sxs-lookup"><span data-stu-id="e6f8a-168"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="e6f8a-169">Riflette lo stato di connessione nell'interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="e6f8a-169">Reflect the connection state in the UI</span></span>

<span data-ttu-id="e6f8a-170">Quando il client rileva che la connessione è stata persa, viene visualizzata un'interfaccia utente predefinita quando il client tenta di riconnettersi.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-170">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="e6f8a-171">Se la riconnessione non riesce, all'utente viene offerta l'opzione per riprovare.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-171">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="e6f8a-172">Per personalizzare l'interfaccia utente, definire un elemento con un valore `id` di `components-reconnect-modal` nell'oggetto `<body>` della pagina *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="e6f8a-172">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="e6f8a-173">Nella tabella seguente vengono descritte le classi CSS applicate all' `components-reconnect-modal` elemento.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-173">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="e6f8a-174">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="e6f8a-174">CSS class</span></span>                       | <span data-ttu-id="e6f8a-175">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="e6f8a-175">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="e6f8a-176">Connessione persa.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-176">A lost connection.</span></span> <span data-ttu-id="e6f8a-177">È in corso un tentativo di riconnessione del client.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-177">The client is attempting to reconnect.</span></span> <span data-ttu-id="e6f8a-178">Mostra il modale.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-178">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="e6f8a-179">Viene ristabilita una connessione attiva al server.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-179">An active connection is re-established to the server.</span></span> <span data-ttu-id="e6f8a-180">Nascondere il modale.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-180">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="e6f8a-181">Riconnessione non riuscita. probabilmente a causa di un errore di rete.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-181">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="e6f8a-182">Per tentare la riconnessione, chiamare `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="e6f8a-182">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="e6f8a-183">Riconnessione rifiutata.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-183">Reconnection rejected.</span></span> <span data-ttu-id="e6f8a-184">Il server è stato raggiunto ma ha rifiutato la connessione e lo stato dell'utente nel server è andato perso.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-184">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="e6f8a-185">Per ricaricare l'app, chiamare `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="e6f8a-185">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="e6f8a-186">Questo stato di connessione può verificarsi nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="e6f8a-186">This connection state may result when:</span></span><ul><li><span data-ttu-id="e6f8a-187">Si verifica un arresto anomalo del circuito sul lato server.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-187">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="e6f8a-188">Il client viene disconnesso abbastanza a lungo da consentire al server di eliminare lo stato dell'utente.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-188">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="e6f8a-189">Le istanze dei componenti con cui l'utente interagisce sono state eliminate.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-189">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="e6f8a-190">Il server viene riavviato oppure il processo di lavoro dell'app viene riciclato.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-190">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="e6f8a-191">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="e6f8a-191">Render mode</span></span>

Blazor<span data-ttu-id="e6f8a-192">Per impostazione predefinita, le app Server sono impostate per eseguire il prerendering dell'interfaccia utente nel server prima che venga stabilita la connessione client al server.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-192"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="e6f8a-193">Questa impostazione è configurata nella pagina *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="e6f8a-193">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="e6f8a-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>Configura se il componente:</span><span class="sxs-lookup"><span data-stu-id="e6f8a-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="e6f8a-195">Viene preeseguito nella pagina.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-195">Is prerendered into the page.</span></span>
* <span data-ttu-id="e6f8a-196">Viene sottoposto a rendering come HTML statico nella pagina o se include le informazioni necessarie per il bootstrap di un' Blazor app dall'agente utente.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-196">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="e6f8a-197">Description</span><span class="sxs-lookup"><span data-stu-id="e6f8a-197">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="e6f8a-198">Esegue il rendering del componente in HTML statico e include un marcatore per un' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-198">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="e6f8a-199">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-199">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="e6f8a-200">Esegue il rendering di un marcatore per un' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-200">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="e6f8a-201">L'output del componente non è incluso.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-201">Output from the component isn't included.</span></span> <span data-ttu-id="e6f8a-202">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-202">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="e6f8a-203">Esegue il rendering del componente in HTML statico.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-203">Renders the component into static HTML.</span></span> |

<span data-ttu-id="e6f8a-204">Il rendering dei componenti server da una pagina HTML statica non è supportato.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-204">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="e6f8a-205">Configurare il SignalR client per le Blazor app Server</span><span class="sxs-lookup"><span data-stu-id="e6f8a-205">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="e6f8a-206">In alcuni casi, è necessario configurare il SignalR client usato dalle Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-206">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="e6f8a-207">È ad esempio possibile configurare la registrazione sul SignalR client per diagnosticare un problema di connessione.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-207">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="e6f8a-208">Per configurare il SignalR client nel file *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="e6f8a-208">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="e6f8a-209">Aggiungere un `autostart="false"` attributo al `<script>` tag per lo `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-209">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="e6f8a-210">Chiamare `Blazor.start` e passare un oggetto di configurazione che specifichi il SignalR generatore.</span><span class="sxs-lookup"><span data-stu-id="e6f8a-210">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="e6f8a-211">Registrazione</span><span class="sxs-lookup"><span data-stu-id="e6f8a-211">Logging</span></span>

<span data-ttu-id="e6f8a-212">Per informazioni sul Blazor supporto per la registrazione del server, vedere <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="e6f8a-212">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
