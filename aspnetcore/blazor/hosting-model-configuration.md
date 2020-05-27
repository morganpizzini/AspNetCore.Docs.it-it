---
<span data-ttu-id="db192-101">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="db192-101">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="db192-102">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="db192-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db192-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db192-103">'Blazor'</span></span>
- <span data-ttu-id="db192-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db192-104">'Identity'</span></span>
- <span data-ttu-id="db192-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db192-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="db192-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db192-106">'Razor'</span></span>
- <span data-ttu-id="db192-107">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="db192-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="db192-108">BlazorConfigurazione del modello di hosting ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="db192-108">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="db192-109">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="db192-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="db192-110">Questo articolo illustra la configurazione del modello di hosting.</span><span class="sxs-lookup"><span data-stu-id="db192-110">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="db192-111">Webassembly</span><span class="sxs-lookup"><span data-stu-id="db192-111"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="db192-112">Environment</span><span class="sxs-lookup"><span data-stu-id="db192-112">Environment</span></span>

<span data-ttu-id="db192-113">Quando si esegue un'app localmente, per impostazione predefinita viene impostato lo sviluppo per l'ambiente.</span><span class="sxs-lookup"><span data-stu-id="db192-113">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="db192-114">Quando l'app viene pubblicata, per impostazione predefinita viene impostato l'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="db192-114">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="db192-115">Un' Blazor app webassembly ospitata preleva l'ambiente dal server tramite un middleware che comunica l'ambiente al browser aggiungendo l' `blazor-environment` intestazione.</span><span class="sxs-lookup"><span data-stu-id="db192-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="db192-116">Il valore dell'intestazione è l'ambiente.</span><span class="sxs-lookup"><span data-stu-id="db192-116">The value of the header is the environment.</span></span> <span data-ttu-id="db192-117">L'app ospitata Blazor e l'app Server condividono lo stesso ambiente.</span><span class="sxs-lookup"><span data-stu-id="db192-117">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="db192-118">Per ulteriori informazioni, tra cui come configurare l'ambiente, vedere <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="db192-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="db192-119">Per un'app autonoma in esecuzione in locale, il server di sviluppo aggiunge l' `blazor-environment` intestazione per specificare l'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="db192-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="db192-120">Per specificare l'ambiente per altri ambienti host, aggiungere l' `blazor-environment` intestazione.</span><span class="sxs-lookup"><span data-stu-id="db192-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="db192-121">Nell'esempio seguente per IIS aggiungere l'intestazione personalizzata al file *Web. config* pubblicato.</span><span class="sxs-lookup"><span data-stu-id="db192-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="db192-122">Il file *Web. config* si trova nella cartella *bin/Release/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="db192-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="db192-123">Per usare un file *Web. config* personalizzato per IIS che non viene sovrascritto quando l'app viene pubblicata nella cartella di *pubblicazione* , vedere <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="db192-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="db192-124">Ottenere l'ambiente dell'app in un componente inserendo <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> e leggendo la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> proprietà:</span><span class="sxs-lookup"><span data-stu-id="db192-124">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="db192-125">Durante l'avvio, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> espone l'oggetto <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> tramite la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> proprietà, che consente agli sviluppatori di disporre di logica specifica dell'ambiente nel codice:</span><span class="sxs-lookup"><span data-stu-id="db192-125">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="db192-126">I metodi di estensione pratici seguenti consentono di controllare l'ambiente corrente per i nomi di sviluppo, produzione, gestione temporanea e ambiente personalizzato:</span><span class="sxs-lookup"><span data-stu-id="db192-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

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

<span data-ttu-id="db192-127">La <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> proprietà può essere utilizzata durante l'avvio quando il <xref:Microsoft.AspNetCore.Components.NavigationManager> servizio non è disponibile.</span><span class="sxs-lookup"><span data-stu-id="db192-127">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="db192-128">Configurazione</span><span class="sxs-lookup"><span data-stu-id="db192-128">Configuration</span></span>

Blazor<span data-ttu-id="db192-129">Webassembly carica la configurazione da:</span><span class="sxs-lookup"><span data-stu-id="db192-129"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="db192-130">File di impostazioni dell'app per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="db192-130">App settings files by default:</span></span>
  * <span data-ttu-id="db192-131">*Wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="db192-131">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="db192-132">*Wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="db192-132">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="db192-133">Altri [provider di configurazione](xref:fundamentals/configuration/index) registrati dall'app.</span><span class="sxs-lookup"><span data-stu-id="db192-133">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="db192-134">Non tutti i provider sono appropriati per le Blazor app webassembly.</span><span class="sxs-lookup"><span data-stu-id="db192-134">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="db192-135">Per verificare quali provider sono supportati per Blazor webassembly, è necessario [chiarire i provider di configurazione per Blazor WASM (DotNet/AspNetCore. docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="db192-135">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="db192-136">La configurazione in un' Blazor app webassembly è visibile agli utenti.</span><span class="sxs-lookup"><span data-stu-id="db192-136">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="db192-137">**Non archiviare i segreti dell'app o le credenziali nella configurazione.**</span><span class="sxs-lookup"><span data-stu-id="db192-137">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="db192-138">Per ulteriori informazioni sui provider di configurazione, vedere <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="db192-138">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="db192-139">Configurazione impostazioni app</span><span class="sxs-lookup"><span data-stu-id="db192-139">App settings configuration</span></span>

<span data-ttu-id="db192-140">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="db192-140">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="db192-141">Inserire un' <xref:Microsoft.Extensions.Configuration.IConfiguration> istanza in un componente per accedere ai dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="db192-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="db192-142">Configurazione provider</span><span class="sxs-lookup"><span data-stu-id="db192-142">Provider configuration</span></span>

<span data-ttu-id="db192-143">Nell'esempio seguente viene usato un oggetto <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> per fornire la configurazione aggiuntiva:</span><span class="sxs-lookup"><span data-stu-id="db192-143">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="db192-144">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="db192-144">`Program.Main`:</span></span>

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

<span data-ttu-id="db192-145">Inserire un' <xref:Microsoft.Extensions.Configuration.IConfiguration> istanza in un componente per accedere ai dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="db192-145">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="db192-146">Per leggere altri file di configurazione dalla cartella *wwwroot* alla configurazione, usare un <xref:System.Net.Http.HttpClient> per ottenere il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="db192-146">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="db192-147">Quando si utilizza questo approccio, la <xref:System.Net.Http.HttpClient> registrazione del servizio esistente può utilizzare il client locale creato per leggere il file, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="db192-147">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="db192-148">*wwwroot/Cars. JSON*:</span><span class="sxs-lookup"><span data-stu-id="db192-148">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="db192-149">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="db192-149">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="db192-150">Configurazione dell'autenticazione</span><span class="sxs-lookup"><span data-stu-id="db192-150">Authentication configuration</span></span>

<span data-ttu-id="db192-151">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="db192-151">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="db192-152">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="db192-152">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="db192-153">Configurazione della registrazione</span><span class="sxs-lookup"><span data-stu-id="db192-153">Logging configuration</span></span>

<span data-ttu-id="db192-154">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="db192-154">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="db192-155">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="db192-155">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="db192-156">Configurazione del generatore host</span><span class="sxs-lookup"><span data-stu-id="db192-156">Host builder configuration</span></span>

<span data-ttu-id="db192-157">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="db192-157">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="db192-158">Configurazione memorizzata nella cache</span><span class="sxs-lookup"><span data-stu-id="db192-158">Cached configuration</span></span>

<span data-ttu-id="db192-159">I file di configurazione vengono memorizzati nella cache per l'uso offline.</span><span class="sxs-lookup"><span data-stu-id="db192-159">Configuration files are cached for offline use.</span></span> <span data-ttu-id="db192-160">Con [le applicazioni Web progressive (PWA)](xref:blazor/progressive-web-app)è possibile aggiornare solo i file di configurazione quando si crea una nuova distribuzione.</span><span class="sxs-lookup"><span data-stu-id="db192-160">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="db192-161">La modifica dei file di configurazione tra le distribuzioni non ha effetto perché:</span><span class="sxs-lookup"><span data-stu-id="db192-161">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="db192-162">Gli utenti dispongono di versioni memorizzate nella cache dei file che continuano a usare.</span><span class="sxs-lookup"><span data-stu-id="db192-162">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="db192-163">I file *service-worker. js* e *service-worker-assets. js* di PWA devono essere ricompilati durante la compilazione, che segnalano all'app la prossima visita online dell'utente che l'app è stata ridistribuita.</span><span class="sxs-lookup"><span data-stu-id="db192-163">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="db192-164">Per ulteriori informazioni sul modo in cui gli aggiornamenti in background vengono gestiti da PWA, vedere <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="db192-164">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="db192-165">Registrazione</span><span class="sxs-lookup"><span data-stu-id="db192-165">Logging</span></span>

<span data-ttu-id="db192-166">Per informazioni sul Blazor supporto della registrazione di webassembly, vedere <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="db192-166">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="db192-167">Server</span><span class="sxs-lookup"><span data-stu-id="db192-167"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="db192-168">Riflette lo stato di connessione nell'interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="db192-168">Reflect the connection state in the UI</span></span>

<span data-ttu-id="db192-169">Quando il client rileva che la connessione è stata persa, viene visualizzata un'interfaccia utente predefinita quando il client tenta di riconnettersi.</span><span class="sxs-lookup"><span data-stu-id="db192-169">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="db192-170">Se la riconnessione non riesce, all'utente viene offerta l'opzione per riprovare.</span><span class="sxs-lookup"><span data-stu-id="db192-170">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="db192-171">Per personalizzare l'interfaccia utente, definire un elemento con un valore `id` di `components-reconnect-modal` nell'oggetto `<body>` della pagina *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="db192-171">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="db192-172">Nella tabella seguente vengono descritte le classi CSS applicate all' `components-reconnect-modal` elemento.</span><span class="sxs-lookup"><span data-stu-id="db192-172">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="db192-173">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="db192-173">CSS class</span></span>                       | <span data-ttu-id="db192-174">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="db192-174">Indicates&hellip;</span></span> |
| ---
<span data-ttu-id="db192-175">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="db192-175">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="db192-176">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="db192-176">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db192-177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db192-177">'Blazor'</span></span>
- <span data-ttu-id="db192-178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db192-178">'Identity'</span></span>
- <span data-ttu-id="db192-179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db192-179">'Let's Encrypt'</span></span>
- <span data-ttu-id="db192-180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db192-180">'Razor'</span></span>
- <span data-ttu-id="db192-181">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="db192-181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db192-182">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="db192-182">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="db192-183">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="db192-183">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db192-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db192-184">'Blazor'</span></span>
- <span data-ttu-id="db192-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db192-185">'Identity'</span></span>
- <span data-ttu-id="db192-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db192-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="db192-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db192-187">'Razor'</span></span>
- <span data-ttu-id="db192-188">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="db192-188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db192-189">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="db192-189">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="db192-190">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="db192-190">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db192-191">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db192-191">'Blazor'</span></span>
- <span data-ttu-id="db192-192">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db192-192">'Identity'</span></span>
- <span data-ttu-id="db192-193">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db192-193">'Let's Encrypt'</span></span>
- <span data-ttu-id="db192-194">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db192-194">'Razor'</span></span>
- <span data-ttu-id="db192-195">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="db192-195">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db192-196">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="db192-196">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="db192-197">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="db192-197">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db192-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db192-198">'Blazor'</span></span>
- <span data-ttu-id="db192-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db192-199">'Identity'</span></span>
- <span data-ttu-id="db192-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db192-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="db192-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db192-201">'Razor'</span></span>
- <span data-ttu-id="db192-202">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="db192-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db192-203">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="db192-203">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="db192-204">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="db192-204">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db192-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db192-205">'Blazor'</span></span>
- <span data-ttu-id="db192-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db192-206">'Identity'</span></span>
- <span data-ttu-id="db192-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db192-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="db192-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db192-208">'Razor'</span></span>
- <span data-ttu-id="db192-209">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="db192-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db192-210">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="db192-210">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="db192-211">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="db192-211">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db192-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db192-212">'Blazor'</span></span>
- <span data-ttu-id="db192-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db192-213">'Identity'</span></span>
- <span data-ttu-id="db192-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db192-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="db192-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db192-215">'Razor'</span></span>
- <span data-ttu-id="db192-216">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="db192-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db192-217">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="db192-217">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="db192-218">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="db192-218">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db192-219">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db192-219">'Blazor'</span></span>
- <span data-ttu-id="db192-220">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db192-220">'Identity'</span></span>
- <span data-ttu-id="db192-221">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db192-221">'Let's Encrypt'</span></span>
- <span data-ttu-id="db192-222">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db192-222">'Razor'</span></span>
- <span data-ttu-id="db192-223">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="db192-223">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db192-224">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="db192-224">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="db192-225">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="db192-225">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db192-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db192-226">'Blazor'</span></span>
- <span data-ttu-id="db192-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db192-227">'Identity'</span></span>
- <span data-ttu-id="db192-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db192-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="db192-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db192-229">'Razor'</span></span>
- <span data-ttu-id="db192-230">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="db192-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db192-231">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="db192-231">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="db192-232">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="db192-232">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db192-233">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db192-233">'Blazor'</span></span>
- <span data-ttu-id="db192-234">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db192-234">'Identity'</span></span>
- <span data-ttu-id="db192-235">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db192-235">'Let's Encrypt'</span></span>
- <span data-ttu-id="db192-236">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db192-236">'Razor'</span></span>
- <span data-ttu-id="db192-237">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="db192-237">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db192-238">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="db192-238">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="db192-239">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="db192-239">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db192-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db192-240">'Blazor'</span></span>
- <span data-ttu-id="db192-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db192-241">'Identity'</span></span>
- <span data-ttu-id="db192-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db192-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="db192-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db192-243">'Razor'</span></span>
- <span data-ttu-id="db192-244">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="db192-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db192-245">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="db192-245">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="db192-246">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="db192-246">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db192-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db192-247">'Blazor'</span></span>
- <span data-ttu-id="db192-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db192-248">'Identity'</span></span>
- <span data-ttu-id="db192-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db192-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="db192-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db192-250">'Razor'</span></span>
- <span data-ttu-id="db192-251">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="db192-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db192-252">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="db192-252">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="db192-253">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="db192-253">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db192-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db192-254">'Blazor'</span></span>
- <span data-ttu-id="db192-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db192-255">'Identity'</span></span>
- <span data-ttu-id="db192-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db192-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="db192-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db192-257">'Razor'</span></span>
- <span data-ttu-id="db192-258">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="db192-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db192-259">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="db192-259">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="db192-260">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="db192-260">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db192-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db192-261">'Blazor'</span></span>
- <span data-ttu-id="db192-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db192-262">'Identity'</span></span>
- <span data-ttu-id="db192-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db192-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="db192-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db192-264">'Razor'</span></span>
- <span data-ttu-id="db192-265">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="db192-265">'SignalR' uid:</span></span> 

<span data-ttu-id="db192-266">---------------- | Titolo---: "ASP.NET Core Blazor configurazione del modello di hosting" autore: Descrizione: "informazioni sulla Blazor configurazione del modello di hosting, incluse informazioni su come integrare i Razor componenti in Razor pagine e app MVC".</span><span class="sxs-lookup"><span data-stu-id="db192-266">---------------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="db192-267">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="db192-267">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db192-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db192-268">'Blazor'</span></span>
- <span data-ttu-id="db192-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db192-269">'Identity'</span></span>
- <span data-ttu-id="db192-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db192-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="db192-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db192-271">'Razor'</span></span>
- <span data-ttu-id="db192-272">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="db192-272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db192-273">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="db192-273">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="db192-274">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="db192-274">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db192-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db192-275">'Blazor'</span></span>
- <span data-ttu-id="db192-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db192-276">'Identity'</span></span>
- <span data-ttu-id="db192-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db192-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="db192-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db192-278">'Razor'</span></span>
- <span data-ttu-id="db192-279">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="db192-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db192-280">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="db192-280">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="db192-281">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="db192-281">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db192-282">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db192-282">'Blazor'</span></span>
- <span data-ttu-id="db192-283">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db192-283">'Identity'</span></span>
- <span data-ttu-id="db192-284">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db192-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="db192-285">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db192-285">'Razor'</span></span>
- <span data-ttu-id="db192-286">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="db192-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db192-287">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="db192-287">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="db192-288">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="db192-288">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db192-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db192-289">'Blazor'</span></span>
- <span data-ttu-id="db192-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db192-290">'Identity'</span></span>
- <span data-ttu-id="db192-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db192-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="db192-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db192-292">'Razor'</span></span>
- <span data-ttu-id="db192-293">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="db192-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db192-294">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="db192-294">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="db192-295">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="db192-295">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db192-296">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db192-296">'Blazor'</span></span>
- <span data-ttu-id="db192-297">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db192-297">'Identity'</span></span>
- <span data-ttu-id="db192-298">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db192-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="db192-299">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db192-299">'Razor'</span></span>
- <span data-ttu-id="db192-300">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="db192-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db192-301">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="db192-301">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="db192-302">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="db192-302">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db192-303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="db192-303">'Blazor'</span></span>
- <span data-ttu-id="db192-304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="db192-304">'Identity'</span></span>
- <span data-ttu-id="db192-305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="db192-305">'Let's Encrypt'</span></span>
- <span data-ttu-id="db192-306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="db192-306">'Razor'</span></span>
- <span data-ttu-id="db192-307">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="db192-307">'SignalR' uid:</span></span> 

<span data-ttu-id="db192-308">--------- | | `components-reconnect-show`     | Connessione persa.</span><span class="sxs-lookup"><span data-stu-id="db192-308">--------- | | `components-reconnect-show`     | A lost connection.</span></span> <span data-ttu-id="db192-309">È in corso un tentativo di riconnessione del client.</span><span class="sxs-lookup"><span data-stu-id="db192-309">The client is attempting to reconnect.</span></span> <span data-ttu-id="db192-310">Mostra il modale.</span><span class="sxs-lookup"><span data-stu-id="db192-310">Show the modal.</span></span> <span data-ttu-id="db192-311">| | `components-reconnect-hide`     | Viene ristabilita una connessione attiva al server.</span><span class="sxs-lookup"><span data-stu-id="db192-311">| | `components-reconnect-hide`     | An active connection is re-established to the server.</span></span> <span data-ttu-id="db192-312">Nascondere il modale.</span><span class="sxs-lookup"><span data-stu-id="db192-312">Hide the modal.</span></span> <span data-ttu-id="db192-313">| | `components-reconnect-failed`   | Riconnessione non riuscita. probabilmente a causa di un errore di rete.</span><span class="sxs-lookup"><span data-stu-id="db192-313">| | `components-reconnect-failed`   | Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="db192-314">Per tentare la riconnessione, chiamare `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="db192-314">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> <span data-ttu-id="db192-315">| | `components-reconnect-rejected` | Riconnessione rifiutata.</span><span class="sxs-lookup"><span data-stu-id="db192-315">| | `components-reconnect-rejected` | Reconnection rejected.</span></span> <span data-ttu-id="db192-316">Il server è stato raggiunto ma ha rifiutato la connessione e lo stato dell'utente nel server è andato perso.</span><span class="sxs-lookup"><span data-stu-id="db192-316">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="db192-317">Per ricaricare l'app, chiamare `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="db192-317">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="db192-318">Questo stato di connessione può verificarsi nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="db192-318">This connection state may result when:</span></span><ul><li><span data-ttu-id="db192-319">Si verifica un arresto anomalo del circuito sul lato server.</span><span class="sxs-lookup"><span data-stu-id="db192-319">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="db192-320">Il client viene disconnesso abbastanza a lungo da consentire al server di eliminare lo stato dell'utente.</span><span class="sxs-lookup"><span data-stu-id="db192-320">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="db192-321">Le istanze dei componenti con cui l'utente interagisce sono state eliminate.</span><span class="sxs-lookup"><span data-stu-id="db192-321">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="db192-322">Il server viene riavviato oppure il processo di lavoro dell'app viene riciclato.</span><span class="sxs-lookup"><span data-stu-id="db192-322">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="db192-323">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="db192-323">Render mode</span></span>

Blazor<span data-ttu-id="db192-324">Per impostazione predefinita, le app Server sono impostate per eseguire il prerendering dell'interfaccia utente nel server prima che venga stabilita la connessione client al server.</span><span class="sxs-lookup"><span data-stu-id="db192-324"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="db192-325">Questa impostazione è configurata nella pagina *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="db192-325">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="db192-326"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>Configura se il componente:</span><span class="sxs-lookup"><span data-stu-id="db192-326"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="db192-327">Viene preeseguito nella pagina.</span><span class="sxs-lookup"><span data-stu-id="db192-327">Is prerendered into the page.</span></span>
* <span data-ttu-id="db192-328">Viene sottoposto a rendering come HTML statico nella pagina o se include le informazioni necessarie per il bootstrap di un' Blazor app dall'agente utente.</span><span class="sxs-lookup"><span data-stu-id="db192-328">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="db192-329">Descrizione</span><span class="sxs-lookup"><span data-stu-id="db192-329">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="db192-330">Esegue il rendering del componente in HTML statico e include un marcatore per un' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="db192-330">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="db192-331">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="db192-331">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="db192-332">Esegue il rendering di un marcatore per un' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="db192-332">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="db192-333">L'output del componente non è incluso.</span><span class="sxs-lookup"><span data-stu-id="db192-333">Output from the component isn't included.</span></span> <span data-ttu-id="db192-334">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="db192-334">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="db192-335">Esegue il rendering del componente in HTML statico.</span><span class="sxs-lookup"><span data-stu-id="db192-335">Renders the component into static HTML.</span></span> |

<span data-ttu-id="db192-336">Il rendering dei componenti server da una pagina HTML statica non è supportato.</span><span class="sxs-lookup"><span data-stu-id="db192-336">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="db192-337">Configurare il SignalR client per le Blazor app Server</span><span class="sxs-lookup"><span data-stu-id="db192-337">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="db192-338">In alcuni casi, è necessario configurare il SignalR client usato dalle Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="db192-338">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="db192-339">È ad esempio possibile configurare la registrazione sul SignalR client per diagnosticare un problema di connessione.</span><span class="sxs-lookup"><span data-stu-id="db192-339">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="db192-340">Per configurare il SignalR client nel file *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="db192-340">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="db192-341">Aggiungere un `autostart="false"` attributo al `<script>` tag per lo `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="db192-341">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="db192-342">Chiamare `Blazor.start` e passare un oggetto di configurazione che specifichi il SignalR generatore.</span><span class="sxs-lookup"><span data-stu-id="db192-342">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="db192-343">Registrazione</span><span class="sxs-lookup"><span data-stu-id="db192-343">Logging</span></span>

<span data-ttu-id="db192-344">Per informazioni sul Blazor supporto per la registrazione del server, vedere <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="db192-344">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
