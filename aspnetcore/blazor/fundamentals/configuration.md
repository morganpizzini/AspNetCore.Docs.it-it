---
title: 'Configurazione di ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: "Informazioni sulla configurazione delle :::no-loc(Blazor)::: app, incluse le impostazioni dell'app, l'autenticazione e la configurazione della registrazione."
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/29/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 430ea2c764e068fe47353115cbeccd2c092617cb
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056010"
---
# <a name="aspnet-core-no-locblazor-configuration"></a><span data-ttu-id="cee6e-103">Configurazione di ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="cee6e-103">ASP.NET Core :::no-loc(Blazor)::: configuration</span></span>

> [!NOTE]
> <span data-ttu-id="cee6e-104">Questo argomento si applica a :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="cee6e-104">This topic applies to :::no-loc(Blazor WebAssembly):::.</span></span> <span data-ttu-id="cee6e-105">Per indicazioni generali sulla configurazione dell'app ASP.NET Core, vedere <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="cee6e-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="cee6e-106">:::no-loc(Blazor WebAssembly)::: carica la configurazione dai file di impostazioni dell'app per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="cee6e-106">:::no-loc(Blazor WebAssembly)::: loads configuration from app settings files by default:</span></span>

* `wwwroot/:::no-loc(appsettings.json):::`
* `wwwroot/appsettings.{ENVIRONMENT}.json`

<span data-ttu-id="cee6e-107">Anche altri provider di configurazione registrati dall'app possono fornire la configurazione.</span><span class="sxs-lookup"><span data-stu-id="cee6e-107">Other configuration providers registered by the app can also provide configuration.</span></span>

<span data-ttu-id="cee6e-108">Non tutti i provider o le funzionalità del provider sono appropriate per le :::no-loc(Blazor WebAssembly)::: app:</span><span class="sxs-lookup"><span data-stu-id="cee6e-108">Not all providers or provider features are appropriate for :::no-loc(Blazor WebAssembly)::: apps:</span></span>

* <span data-ttu-id="cee6e-109">[Provider di configurazione Azure Key Vault](xref:security/key-vault-configuration): il provider non è supportato per l'identità gestita e l'ID applicazione (ID client) con scenari di segreto client.</span><span class="sxs-lookup"><span data-stu-id="cee6e-109">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="cee6e-110">Non è consigliabile usare l'ID applicazione con un segreto client per qualsiasi app ASP.NET Core, in particolare per :::no-loc(Blazor WebAssembly)::: le app perché il segreto client non può essere protetto sul lato client per accedere al servizio.</span><span class="sxs-lookup"><span data-stu-id="cee6e-110">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially :::no-loc(Blazor WebAssembly)::: apps because the client secret can't be secured client-side to access to the service.</span></span>
* <span data-ttu-id="cee6e-111">[Provider di configurazione app Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app): il provider non è appropriato per le :::no-loc(Blazor WebAssembly)::: app perché le :::no-loc(Blazor WebAssembly)::: app non vengono eseguite in un server in Azure.</span><span class="sxs-lookup"><span data-stu-id="cee6e-111">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for :::no-loc(Blazor WebAssembly)::: apps because :::no-loc(Blazor WebAssembly)::: apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="cee6e-112">La configurazione in un' :::no-loc(Blazor WebAssembly)::: app è visibile agli utenti.</span><span class="sxs-lookup"><span data-stu-id="cee6e-112">Configuration in a :::no-loc(Blazor WebAssembly)::: app is visible to users.</span></span> <span data-ttu-id="cee6e-113">**Non archiviare i segreti dell'app o le credenziali nella configurazione.**</span><span class="sxs-lookup"><span data-stu-id="cee6e-113">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="cee6e-114">Per ulteriori informazioni sui provider di configurazione, vedere <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="cee6e-114">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="cee6e-115">Configurazione impostazioni app</span><span class="sxs-lookup"><span data-stu-id="cee6e-115">App settings configuration</span></span>

<span data-ttu-id="cee6e-116">`wwwroot/:::no-loc(appsettings.json):::`:</span><span class="sxs-lookup"><span data-stu-id="cee6e-116">`wwwroot/:::no-loc(appsettings.json):::`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="cee6e-117">Inserire un' <xref:Microsoft.Extensions.Configuration.IConfiguration> istanza in un componente per accedere ai dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="cee6e-117">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="custom-configuration-provider-with-ef-core"></a><span data-ttu-id="cee6e-118">Provider di configurazione personalizzato con EF Core</span><span class="sxs-lookup"><span data-stu-id="cee6e-118">Custom configuration provider with EF Core</span></span>

<span data-ttu-id="cee6e-119">Il provider di configurazione personalizzato con EF Core dimostrato in <xref:fundamentals/configuration/index#custom-configuration-provider> funziona con le :::no-loc(Blazor WebAssembly)::: app.</span><span class="sxs-lookup"><span data-stu-id="cee6e-119">The custom configuration provider with EF Core demonstrated in <xref:fundamentals/configuration/index#custom-configuration-provider> works with :::no-loc(Blazor WebAssembly)::: apps.</span></span>

<span data-ttu-id="cee6e-120">Aggiungere il provider di configurazione dell'esempio con il codice seguente in `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="cee6e-120">Add the example's configuration provider with the following code in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<span data-ttu-id="cee6e-121">Inserire un' <xref:Microsoft.Extensions.Configuration.IConfiguration> istanza in un componente per accedere ai dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="cee6e-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>
```

## <a name="memory-configuration-source"></a><span data-ttu-id="cee6e-122">Origine configurazione memoria</span><span class="sxs-lookup"><span data-stu-id="cee6e-122">Memory Configuration Source</span></span>

<span data-ttu-id="cee6e-123">Nell'esempio seguente viene usato un oggetto <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> per fornire la configurazione aggiuntiva:</span><span class="sxs-lookup"><span data-stu-id="cee6e-123">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="cee6e-124">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cee6e-124">`Program.Main`:</span></span>

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

<span data-ttu-id="cee6e-125">Inserire un' <xref:Microsoft.Extensions.Configuration.IConfiguration> istanza in un componente per accedere ai dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="cee6e-125">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");
        
        ...
    }
}
```

<span data-ttu-id="cee6e-126">Per leggere altri file di configurazione dalla `wwwroot` cartella alla configurazione, usare un <xref:System.Net.Http.HttpClient> per ottenere il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="cee6e-126">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="cee6e-127">Quando si utilizza questo approccio, la <xref:System.Net.Http.HttpClient> registrazione del servizio esistente può utilizzare il client locale creato per leggere il file, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="cee6e-127">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="cee6e-128">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="cee6e-128">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="cee6e-129">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cee6e-129">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a><span data-ttu-id="cee6e-130">Configurazione dell'autenticazione</span><span class="sxs-lookup"><span data-stu-id="cee6e-130">Authentication configuration</span></span>

<span data-ttu-id="cee6e-131">`wwwroot/:::no-loc(appsettings.json):::`:</span><span class="sxs-lookup"><span data-stu-id="cee6e-131">`wwwroot/:::no-loc(appsettings.json):::`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="cee6e-132">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cee6e-132">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="cee6e-133">Configurazione della registrazione</span><span class="sxs-lookup"><span data-stu-id="cee6e-133">Logging configuration</span></span>

<span data-ttu-id="cee6e-134">Aggiungere un riferimento al pacchetto per [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) :</span><span class="sxs-lookup"><span data-stu-id="cee6e-134">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="cee6e-135">Per il segnaposto `{VERSION}` , la versione stabile più recente del pacchetto che corrisponde alla versione del Framework condiviso dell'app è disponibile nella cronologia delle **versioni** del pacchetto in [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration).</span><span class="sxs-lookup"><span data-stu-id="cee6e-135">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration).</span></span>

<span data-ttu-id="cee6e-136">`wwwroot/:::no-loc(appsettings.json):::`:</span><span class="sxs-lookup"><span data-stu-id="cee6e-136">`wwwroot/:::no-loc(appsettings.json):::`:</span></span>

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

<span data-ttu-id="cee6e-137">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cee6e-137">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="cee6e-138">Configurazione del generatore host</span><span class="sxs-lookup"><span data-stu-id="cee6e-138">Host builder configuration</span></span>

<span data-ttu-id="cee6e-139">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cee6e-139">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="cee6e-140">Configurazione memorizzata nella cache</span><span class="sxs-lookup"><span data-stu-id="cee6e-140">Cached configuration</span></span>

<span data-ttu-id="cee6e-141">I file di configurazione vengono memorizzati nella cache per l'uso offline.</span><span class="sxs-lookup"><span data-stu-id="cee6e-141">Configuration files are cached for offline use.</span></span> <span data-ttu-id="cee6e-142">Con [le applicazioni Web progressive (PWA)](xref:blazor/progressive-web-app)è possibile aggiornare solo i file di configurazione quando si crea una nuova distribuzione.</span><span class="sxs-lookup"><span data-stu-id="cee6e-142">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="cee6e-143">La modifica dei file di configurazione tra le distribuzioni non ha effetto perché:</span><span class="sxs-lookup"><span data-stu-id="cee6e-143">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="cee6e-144">Gli utenti dispongono di versioni memorizzate nella cache dei file che continuano a usare.</span><span class="sxs-lookup"><span data-stu-id="cee6e-144">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="cee6e-145">I `service-worker.js` file e di PWA `service-worker-assets.js` devono essere ricompilati durante la compilazione, che segnalano all'app la prossima visita online dell'utente che l'app è stata ridistribuita.</span><span class="sxs-lookup"><span data-stu-id="cee6e-145">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="cee6e-146">Per ulteriori informazioni sul modo in cui gli aggiornamenti in background vengono gestiti da PWA, vedere <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="cee6e-146">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
