---
<span data-ttu-id="002e1-101">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-101">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-102">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-103">'Blazor'</span></span>
- <span data-ttu-id="002e1-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-104">'Identity'</span></span>
- <span data-ttu-id="002e1-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-106">'Razor'</span></span>
- <span data-ttu-id="002e1-107">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="002e1-108">BlazorConfigurazione del modello di hosting ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="002e1-108">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="002e1-109">Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="002e1-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="002e1-110">Questo articolo illustra la configurazione del modello di hosting.</span><span class="sxs-lookup"><span data-stu-id="002e1-110">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="002e1-111">Webassembly</span><span class="sxs-lookup"><span data-stu-id="002e1-111"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="002e1-112">Environment</span><span class="sxs-lookup"><span data-stu-id="002e1-112">Environment</span></span>

<span data-ttu-id="002e1-113">Quando si esegue un'app localmente, per impostazione predefinita viene impostato lo sviluppo per l'ambiente.</span><span class="sxs-lookup"><span data-stu-id="002e1-113">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="002e1-114">Quando l'app viene pubblicata, per impostazione predefinita viene impostato l'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="002e1-114">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="002e1-115">Un' Blazor app webassembly ospitata preleva l'ambiente dal server tramite un middleware che comunica l'ambiente al browser aggiungendo l' `blazor-environment` intestazione.</span><span class="sxs-lookup"><span data-stu-id="002e1-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="002e1-116">Il valore dell'intestazione è l'ambiente.</span><span class="sxs-lookup"><span data-stu-id="002e1-116">The value of the header is the environment.</span></span> <span data-ttu-id="002e1-117">L'app ospitata Blazor e l'app Server condividono lo stesso ambiente.</span><span class="sxs-lookup"><span data-stu-id="002e1-117">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="002e1-118">Per ulteriori informazioni, tra cui come configurare l'ambiente, vedere <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="002e1-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="002e1-119">Per un'app autonoma in esecuzione in locale, il server di sviluppo aggiunge l' `blazor-environment` intestazione per specificare l'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="002e1-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="002e1-120">Per specificare l'ambiente per altri ambienti host, aggiungere l' `blazor-environment` intestazione.</span><span class="sxs-lookup"><span data-stu-id="002e1-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="002e1-121">Nell'esempio seguente per IIS aggiungere l'intestazione personalizzata al file *Web. config* pubblicato.</span><span class="sxs-lookup"><span data-stu-id="002e1-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="002e1-122">Il file *Web. config* si trova nella cartella *bin/Release/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="002e1-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="002e1-123">Per usare un file *Web. config* personalizzato per IIS che non viene sovrascritto quando l'app viene pubblicata nella cartella di *pubblicazione* , vedere <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="002e1-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="002e1-124">Ottenere l'ambiente dell'app in un componente inserendo `IWebAssemblyHostEnvironment` e leggendo la `Environment` proprietà:</span><span class="sxs-lookup"><span data-stu-id="002e1-124">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="002e1-125">Durante l'avvio, `WebAssemblyHostBuilder` espone l'oggetto `IWebAssemblyHostEnvironment` tramite la `HostEnvironment` proprietà, che consente agli sviluppatori di disporre di logica specifica dell'ambiente nel codice:</span><span class="sxs-lookup"><span data-stu-id="002e1-125">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="002e1-126">I metodi di estensione pratici seguenti consentono di controllare l'ambiente corrente per i nomi di sviluppo, produzione, gestione temporanea e ambiente personalizzato:</span><span class="sxs-lookup"><span data-stu-id="002e1-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="002e1-127">' Outenvironment ("{nome ambiente}")</span><span class="sxs-lookup"><span data-stu-id="002e1-127">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

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

<span data-ttu-id="002e1-128">La `IWebAssemblyHostEnvironment.BaseAddress` proprietà può essere utilizzata durante l'avvio quando il `NavigationManager` servizio non è disponibile.</span><span class="sxs-lookup"><span data-stu-id="002e1-128">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="002e1-129">Configurazione</span><span class="sxs-lookup"><span data-stu-id="002e1-129">Configuration</span></span>

Blazor<span data-ttu-id="002e1-130">Webassembly carica la configurazione da:</span><span class="sxs-lookup"><span data-stu-id="002e1-130"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="002e1-131">File di impostazioni dell'app per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="002e1-131">App settings files by default:</span></span>
  * <span data-ttu-id="002e1-132">*Wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="002e1-132">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="002e1-133">*Wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="002e1-133">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="002e1-134">Altri [provider di configurazione](xref:fundamentals/configuration/index) registrati dall'app.</span><span class="sxs-lookup"><span data-stu-id="002e1-134">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="002e1-135">Non tutti i provider sono appropriati per le Blazor app webassembly.</span><span class="sxs-lookup"><span data-stu-id="002e1-135">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="002e1-136">Per verificare quali provider sono supportati per Blazor webassembly, è necessario [chiarire i provider di configurazione per Blazor WASM (DotNet/AspNetCore. docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="002e1-136">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="002e1-137">La configurazione in un' Blazor app webassembly è visibile agli utenti.</span><span class="sxs-lookup"><span data-stu-id="002e1-137">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="002e1-138">**Non archiviare i segreti dell'app o le credenziali nella configurazione.**</span><span class="sxs-lookup"><span data-stu-id="002e1-138">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="002e1-139">Per ulteriori informazioni sui provider di configurazione, vedere <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="002e1-139">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="002e1-140">Configurazione impostazioni app</span><span class="sxs-lookup"><span data-stu-id="002e1-140">App settings configuration</span></span>

<span data-ttu-id="002e1-141">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="002e1-141">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="002e1-142">Inserire un' <xref:Microsoft.Extensions.Configuration.IConfiguration> istanza in un componente per accedere ai dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="002e1-142">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="002e1-143">Configurazione provider</span><span class="sxs-lookup"><span data-stu-id="002e1-143">Provider configuration</span></span>

<span data-ttu-id="002e1-144">Nell'esempio seguente viene usato un oggetto <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> per fornire la configurazione aggiuntiva:</span><span class="sxs-lookup"><span data-stu-id="002e1-144">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="002e1-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="002e1-145">`Program.Main`:</span></span>

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

<span data-ttu-id="002e1-146">Inserire un' <xref:Microsoft.Extensions.Configuration.IConfiguration> istanza in un componente per accedere ai dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="002e1-146">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="002e1-147">Per leggere altri file di configurazione dalla cartella *wwwroot* alla configurazione, usare un `HttpClient` per ottenere il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="002e1-147">To read other configuration files from the *wwwroot* folder into configuration, use an `HttpClient` to obtain the file's content.</span></span> <span data-ttu-id="002e1-148">Quando si utilizza questo approccio, la `HttpClient` registrazione del servizio esistente può utilizzare il client locale creato per leggere il file, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="002e1-148">When using this approach, the existing `HttpClient` service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="002e1-149">*wwwroot/Cars. JSON*:</span><span class="sxs-lookup"><span data-stu-id="002e1-149">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="002e1-150">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="002e1-150">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="002e1-151">Configurazione dell'autenticazione</span><span class="sxs-lookup"><span data-stu-id="002e1-151">Authentication configuration</span></span>

<span data-ttu-id="002e1-152">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="002e1-152">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="002e1-153">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="002e1-153">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="002e1-154">Configurazione della registrazione</span><span class="sxs-lookup"><span data-stu-id="002e1-154">Logging configuration</span></span>

<span data-ttu-id="002e1-155">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="002e1-155">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="002e1-156">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="002e1-156">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="002e1-157">Configurazione del generatore host</span><span class="sxs-lookup"><span data-stu-id="002e1-157">Host builder configuration</span></span>

<span data-ttu-id="002e1-158">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="002e1-158">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="002e1-159">Configurazione memorizzata nella cache</span><span class="sxs-lookup"><span data-stu-id="002e1-159">Cached configuration</span></span>

<span data-ttu-id="002e1-160">I file di configurazione vengono memorizzati nella cache per l'uso offline.</span><span class="sxs-lookup"><span data-stu-id="002e1-160">Configuration files are cached for offline use.</span></span> <span data-ttu-id="002e1-161">Con [le applicazioni Web progressive (PWA)](xref:blazor/progressive-web-app)è possibile aggiornare solo i file di configurazione quando si crea una nuova distribuzione.</span><span class="sxs-lookup"><span data-stu-id="002e1-161">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="002e1-162">La modifica dei file di configurazione tra le distribuzioni non ha effetto perché:</span><span class="sxs-lookup"><span data-stu-id="002e1-162">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="002e1-163">Gli utenti dispongono di versioni memorizzate nella cache dei file che continuano a usare.</span><span class="sxs-lookup"><span data-stu-id="002e1-163">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="002e1-164">I file *service-worker. js* e *service-worker-assets. js* di PWA devono essere ricompilati durante la compilazione, che segnalano all'app la prossima visita online dell'utente che l'app è stata ridistribuita.</span><span class="sxs-lookup"><span data-stu-id="002e1-164">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="002e1-165">Per ulteriori informazioni sul modo in cui gli aggiornamenti in background vengono gestiti da PWA, vedere <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="002e1-165">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="002e1-166">Registrazione</span><span class="sxs-lookup"><span data-stu-id="002e1-166">Logging</span></span>

<span data-ttu-id="002e1-167">Per informazioni sul Blazor supporto della registrazione di webassembly, vedere <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="002e1-167">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="002e1-168">Server</span><span class="sxs-lookup"><span data-stu-id="002e1-168"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="002e1-169">Riflette lo stato di connessione nell'interfaccia utente</span><span class="sxs-lookup"><span data-stu-id="002e1-169">Reflect the connection state in the UI</span></span>

<span data-ttu-id="002e1-170">Quando il client rileva che la connessione è stata persa, viene visualizzata un'interfaccia utente predefinita quando il client tenta di riconnettersi.</span><span class="sxs-lookup"><span data-stu-id="002e1-170">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="002e1-171">Se la riconnessione non riesce, all'utente viene offerta l'opzione per riprovare.</span><span class="sxs-lookup"><span data-stu-id="002e1-171">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="002e1-172">Per personalizzare l'interfaccia utente, definire un elemento con un valore `id` di `components-reconnect-modal` nell'oggetto `<body>` della pagina *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="002e1-172">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="002e1-173">Nella tabella seguente vengono descritte le classi CSS applicate all' `components-reconnect-modal` elemento.</span><span class="sxs-lookup"><span data-stu-id="002e1-173">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="002e1-174">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="002e1-174">CSS class</span></span>                       | <span data-ttu-id="002e1-175">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="002e1-175">Indicates&hellip;</span></span> |
| ---
<span data-ttu-id="002e1-176">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-176">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-177">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-178">'Blazor'</span></span>
- <span data-ttu-id="002e1-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-179">'Identity'</span></span>
- <span data-ttu-id="002e1-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-181">'Razor'</span></span>
- <span data-ttu-id="002e1-182">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-183">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-183">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-184">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-185">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-185">'Blazor'</span></span>
- <span data-ttu-id="002e1-186">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-186">'Identity'</span></span>
- <span data-ttu-id="002e1-187">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-187">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-188">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-188">'Razor'</span></span>
- <span data-ttu-id="002e1-189">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-189">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-190">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-190">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-191">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-192">'Blazor'</span></span>
- <span data-ttu-id="002e1-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-193">'Identity'</span></span>
- <span data-ttu-id="002e1-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-195">'Razor'</span></span>
- <span data-ttu-id="002e1-196">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-197">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-197">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-198">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-199">'Blazor'</span></span>
- <span data-ttu-id="002e1-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-200">'Identity'</span></span>
- <span data-ttu-id="002e1-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-202">'Razor'</span></span>
- <span data-ttu-id="002e1-203">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-204">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-204">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-205">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-205">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-206">'Blazor'</span></span>
- <span data-ttu-id="002e1-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-207">'Identity'</span></span>
- <span data-ttu-id="002e1-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-209">'Razor'</span></span>
- <span data-ttu-id="002e1-210">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-211">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-211">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-212">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-213">'Blazor'</span></span>
- <span data-ttu-id="002e1-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-214">'Identity'</span></span>
- <span data-ttu-id="002e1-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-216">'Razor'</span></span>
- <span data-ttu-id="002e1-217">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-218">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-218">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-219">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-220">'Blazor'</span></span>
- <span data-ttu-id="002e1-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-221">'Identity'</span></span>
- <span data-ttu-id="002e1-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-223">'Razor'</span></span>
- <span data-ttu-id="002e1-224">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-225">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-225">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-226">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-227">'Blazor'</span></span>
- <span data-ttu-id="002e1-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-228">'Identity'</span></span>
- <span data-ttu-id="002e1-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-230">'Razor'</span></span>
- <span data-ttu-id="002e1-231">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-231">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-232">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-232">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-233">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-234">'Blazor'</span></span>
- <span data-ttu-id="002e1-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-235">'Identity'</span></span>
- <span data-ttu-id="002e1-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-237">'Razor'</span></span>
- <span data-ttu-id="002e1-238">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-239">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-239">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-240">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-241">'Blazor'</span></span>
- <span data-ttu-id="002e1-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-242">'Identity'</span></span>
- <span data-ttu-id="002e1-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-244">'Razor'</span></span>
- <span data-ttu-id="002e1-245">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-246">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-246">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-247">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-248">'Blazor'</span></span>
- <span data-ttu-id="002e1-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-249">'Identity'</span></span>
- <span data-ttu-id="002e1-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-251">'Razor'</span></span>
- <span data-ttu-id="002e1-252">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-253">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-253">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-254">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-254">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-255">'Blazor'</span></span>
- <span data-ttu-id="002e1-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-256">'Identity'</span></span>
- <span data-ttu-id="002e1-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-258">'Razor'</span></span>
- <span data-ttu-id="002e1-259">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-260">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-260">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-261">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-261">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-262">'Blazor'</span></span>
- <span data-ttu-id="002e1-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-263">'Identity'</span></span>
- <span data-ttu-id="002e1-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-265">'Razor'</span></span>
- <span data-ttu-id="002e1-266">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-266">'SignalR' uid:</span></span> 

<span data-ttu-id="002e1-267">---------------- | Titolo---: "ASP.NET Core Blazor configurazione del modello di hosting" autore: Descrizione: "informazioni sulla Blazor configurazione del modello di hosting, incluse informazioni su come integrare i Razor componenti in Razor pagine e app MVC".</span><span class="sxs-lookup"><span data-stu-id="002e1-267">---------------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-268">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-268">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-269">'Blazor'</span></span>
- <span data-ttu-id="002e1-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-270">'Identity'</span></span>
- <span data-ttu-id="002e1-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-272">'Razor'</span></span>
- <span data-ttu-id="002e1-273">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-274">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-274">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-275">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-275">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-276">'Blazor'</span></span>
- <span data-ttu-id="002e1-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-277">'Identity'</span></span>
- <span data-ttu-id="002e1-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-279">'Razor'</span></span>
- <span data-ttu-id="002e1-280">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-281">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-281">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-282">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-282">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-283">'Blazor'</span></span>
- <span data-ttu-id="002e1-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-284">'Identity'</span></span>
- <span data-ttu-id="002e1-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-286">'Razor'</span></span>
- <span data-ttu-id="002e1-287">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-288">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-288">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-289">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-289">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-290">'Blazor'</span></span>
- <span data-ttu-id="002e1-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-291">'Identity'</span></span>
- <span data-ttu-id="002e1-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-293">'Razor'</span></span>
- <span data-ttu-id="002e1-294">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-295">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-295">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-296">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-296">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-297">'Blazor'</span></span>
- <span data-ttu-id="002e1-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-298">'Identity'</span></span>
- <span data-ttu-id="002e1-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-300">'Razor'</span></span>
- <span data-ttu-id="002e1-301">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-302">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-302">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-303">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-303">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-304">'Blazor'</span></span>
- <span data-ttu-id="002e1-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-305">'Identity'</span></span>
- <span data-ttu-id="002e1-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-307">'Razor'</span></span>
- <span data-ttu-id="002e1-308">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-308">'SignalR' uid:</span></span> 

<span data-ttu-id="002e1-309">--------- | | `components-reconnect-show`     | Connessione persa.</span><span class="sxs-lookup"><span data-stu-id="002e1-309">--------- | | `components-reconnect-show`     | A lost connection.</span></span> <span data-ttu-id="002e1-310">È in corso un tentativo di riconnessione del client.</span><span class="sxs-lookup"><span data-stu-id="002e1-310">The client is attempting to reconnect.</span></span> <span data-ttu-id="002e1-311">Mostra il modale.</span><span class="sxs-lookup"><span data-stu-id="002e1-311">Show the modal.</span></span> <span data-ttu-id="002e1-312">| | `components-reconnect-hide`     | Viene ristabilita una connessione attiva al server.</span><span class="sxs-lookup"><span data-stu-id="002e1-312">| | `components-reconnect-hide`     | An active connection is re-established to the server.</span></span> <span data-ttu-id="002e1-313">Nascondere il modale.</span><span class="sxs-lookup"><span data-stu-id="002e1-313">Hide the modal.</span></span> <span data-ttu-id="002e1-314">| | `components-reconnect-failed`   | Riconnessione non riuscita. probabilmente a causa di un errore di rete.</span><span class="sxs-lookup"><span data-stu-id="002e1-314">| | `components-reconnect-failed`   | Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="002e1-315">Per tentare la riconnessione, chiamare `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="002e1-315">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> <span data-ttu-id="002e1-316">| | `components-reconnect-rejected` | Riconnessione rifiutata.</span><span class="sxs-lookup"><span data-stu-id="002e1-316">| | `components-reconnect-rejected` | Reconnection rejected.</span></span> <span data-ttu-id="002e1-317">Il server è stato raggiunto ma ha rifiutato la connessione e lo stato dell'utente nel server è andato perso.</span><span class="sxs-lookup"><span data-stu-id="002e1-317">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="002e1-318">Per ricaricare l'app, chiamare `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="002e1-318">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="002e1-319">Questo stato di connessione può verificarsi nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="002e1-319">This connection state may result when:</span></span><ul><li><span data-ttu-id="002e1-320">Si verifica un arresto anomalo del circuito sul lato server.</span><span class="sxs-lookup"><span data-stu-id="002e1-320">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="002e1-321">Il client viene disconnesso abbastanza a lungo da consentire al server di eliminare lo stato dell'utente.</span><span class="sxs-lookup"><span data-stu-id="002e1-321">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="002e1-322">Le istanze dei componenti con cui l'utente interagisce sono state eliminate.</span><span class="sxs-lookup"><span data-stu-id="002e1-322">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="002e1-323">Il server viene riavviato oppure il processo di lavoro dell'app viene riciclato.</span><span class="sxs-lookup"><span data-stu-id="002e1-323">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="002e1-324">Modalità di rendering</span><span class="sxs-lookup"><span data-stu-id="002e1-324">Render mode</span></span>

Blazor<span data-ttu-id="002e1-325">Per impostazione predefinita, le app Server sono impostate per eseguire il prerendering dell'interfaccia utente nel server prima che venga stabilita la connessione client al server.</span><span class="sxs-lookup"><span data-stu-id="002e1-325"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="002e1-326">Questa impostazione è configurata nella pagina *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="002e1-326">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="002e1-327">`RenderMode`Configura se il componente:</span><span class="sxs-lookup"><span data-stu-id="002e1-327">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="002e1-328">Viene preeseguito nella pagina.</span><span class="sxs-lookup"><span data-stu-id="002e1-328">Is prerendered into the page.</span></span>
* <span data-ttu-id="002e1-329">Viene sottoposto a rendering come HTML statico nella pagina o se include le informazioni necessarie per il bootstrap di un' Blazor app dall'agente utente.</span><span class="sxs-lookup"><span data-stu-id="002e1-329">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="002e1-330">Description</span><span class="sxs-lookup"><span data-stu-id="002e1-330">Description</span></span> |
| ---
<span data-ttu-id="002e1-331">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-331">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-332">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-332">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-333">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-333">'Blazor'</span></span>
- <span data-ttu-id="002e1-334">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-334">'Identity'</span></span>
- <span data-ttu-id="002e1-335">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-335">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-336">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-336">'Razor'</span></span>
- <span data-ttu-id="002e1-337">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-337">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-338">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-338">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-339">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-339">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-340">'Blazor'</span></span>
- <span data-ttu-id="002e1-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-341">'Identity'</span></span>
- <span data-ttu-id="002e1-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-343">'Razor'</span></span>
- <span data-ttu-id="002e1-344">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-345">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-345">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-346">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-346">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-347">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-347">'Blazor'</span></span>
- <span data-ttu-id="002e1-348">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-348">'Identity'</span></span>
- <span data-ttu-id="002e1-349">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-350">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-350">'Razor'</span></span>
- <span data-ttu-id="002e1-351">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-352">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-352">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-353">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-353">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-354">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-354">'Blazor'</span></span>
- <span data-ttu-id="002e1-355">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-355">'Identity'</span></span>
- <span data-ttu-id="002e1-356">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-356">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-357">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-357">'Razor'</span></span>
- <span data-ttu-id="002e1-358">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-358">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-359">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-359">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-360">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-360">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-361">'Blazor'</span></span>
- <span data-ttu-id="002e1-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-362">'Identity'</span></span>
- <span data-ttu-id="002e1-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-364">'Razor'</span></span>
- <span data-ttu-id="002e1-365">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-366">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-366">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-367">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-367">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-368">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-368">'Blazor'</span></span>
- <span data-ttu-id="002e1-369">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-369">'Identity'</span></span>
- <span data-ttu-id="002e1-370">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-370">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-371">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-371">'Razor'</span></span>
- <span data-ttu-id="002e1-372">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-372">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-373">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-373">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-374">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-374">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-375">'Blazor'</span></span>
- <span data-ttu-id="002e1-376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-376">'Identity'</span></span>
- <span data-ttu-id="002e1-377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-377">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-378">'Razor'</span></span>
- <span data-ttu-id="002e1-379">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-379">'SignalR' uid:</span></span> 

<span data-ttu-id="002e1-380">---------- | Titolo---: "ASP.NET Core Blazor configurazione del modello di hosting" autore: Descrizione: "informazioni sulla Blazor configurazione del modello di hosting, incluse informazioni su come integrare i Razor componenti in Razor pagine e app MVC".</span><span class="sxs-lookup"><span data-stu-id="002e1-380">---------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-381">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-381">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-382">'Blazor'</span></span>
- <span data-ttu-id="002e1-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-383">'Identity'</span></span>
- <span data-ttu-id="002e1-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-385">'Razor'</span></span>
- <span data-ttu-id="002e1-386">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-387">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-387">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-388">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-388">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-389">'Blazor'</span></span>
- <span data-ttu-id="002e1-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-390">'Identity'</span></span>
- <span data-ttu-id="002e1-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-392">'Razor'</span></span>
- <span data-ttu-id="002e1-393">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="002e1-394">title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.</span><span class="sxs-lookup"><span data-stu-id="002e1-394">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="002e1-395">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="002e1-395">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="002e1-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="002e1-396">'Blazor'</span></span>
- <span data-ttu-id="002e1-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="002e1-397">'Identity'</span></span>
- <span data-ttu-id="002e1-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="002e1-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="002e1-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="002e1-399">'Razor'</span></span>
- <span data-ttu-id="002e1-400">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="002e1-400">'SignalR' uid:</span></span> 

<span data-ttu-id="002e1-401">------ | | `ServerPrerendered` | Esegue il rendering del componente in HTML statico e include un marcatore per un' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="002e1-401">------ | | `ServerPrerendered` | Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="002e1-402">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="002e1-402">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="002e1-403">| | `Server`            | Esegue il rendering di un marcatore per un' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="002e1-403">| | `Server`            | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="002e1-404">L'output del componente non è incluso.</span><span class="sxs-lookup"><span data-stu-id="002e1-404">Output from the component isn't included.</span></span> <span data-ttu-id="002e1-405">Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="002e1-405">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="002e1-406">| | `Static`            | Esegue il rendering del componente in HTML statico.</span><span class="sxs-lookup"><span data-stu-id="002e1-406">| | `Static`            | Renders the component into static HTML.</span></span> |

<span data-ttu-id="002e1-407">Il rendering dei componenti server da una pagina HTML statica non è supportato.</span><span class="sxs-lookup"><span data-stu-id="002e1-407">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="002e1-408">Configurare il SignalR client per le Blazor app Server</span><span class="sxs-lookup"><span data-stu-id="002e1-408">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="002e1-409">In alcuni casi, è necessario configurare il SignalR client usato dalle Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="002e1-409">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="002e1-410">È ad esempio possibile configurare la registrazione sul SignalR client per diagnosticare un problema di connessione.</span><span class="sxs-lookup"><span data-stu-id="002e1-410">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="002e1-411">Per configurare il SignalR client nel file *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="002e1-411">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="002e1-412">Aggiungere un `autostart="false"` attributo al `<script>` tag per lo `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="002e1-412">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="002e1-413">Chiamare `Blazor.start` e passare un oggetto di configurazione che specifichi il SignalR generatore.</span><span class="sxs-lookup"><span data-stu-id="002e1-413">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="002e1-414">Registrazione</span><span class="sxs-lookup"><span data-stu-id="002e1-414">Logging</span></span>

<span data-ttu-id="002e1-415">Per informazioni sul Blazor supporto per la registrazione del server, vedere <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="002e1-415">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
