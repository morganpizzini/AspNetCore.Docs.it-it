---
title: Configurazione di ASP.NET Core Blazor
author: guardrex
description: Informazioni sulla configurazione delle Blazor app, incluse le impostazioni dell'app, l'autenticazione e la configurazione della registrazione.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 3e1d265f269e2a2c5ea584e62e80f8a6167f9d3a
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944406"
---
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="f064b-103">Configurazione di ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="f064b-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="f064b-104">Questo argomento si applica a Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="f064b-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="f064b-105">Per indicazioni generali sulla configurazione dell'app ASP.NET Core, vedere <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="f064b-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

Blazor WebAssembly<span data-ttu-id="f064b-106">carica la configurazione da:</span><span class="sxs-lookup"><span data-stu-id="f064b-106"> loads configuration from:</span></span>

* <span data-ttu-id="f064b-107">File di impostazioni dell'app per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="f064b-107">App settings files by default:</span></span>
  * `wwwroot/appsettings.json`
  * `wwwroot/appsettings.{ENVIRONMENT}.json`
* <span data-ttu-id="f064b-108">Altri [provider di configurazione](xref:fundamentals/configuration/index) registrati dall'app.</span><span class="sxs-lookup"><span data-stu-id="f064b-108">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="f064b-109">Non tutti i provider sono appropriati per le Blazor WebAssembly app.</span><span class="sxs-lookup"><span data-stu-id="f064b-109">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="f064b-110">Per chiarire quali provider sono supportati per Blazor WebAssembly viene rilevato, [chiarire i provider di configurazione per Blazor WASM (DotNet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="f064b-110">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="f064b-111">La configurazione in un' Blazor WebAssembly app è visibile agli utenti.</span><span class="sxs-lookup"><span data-stu-id="f064b-111">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="f064b-112">**Non archiviare i segreti dell'app o le credenziali nella configurazione.**</span><span class="sxs-lookup"><span data-stu-id="f064b-112">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="f064b-113">Per ulteriori informazioni sui provider di configurazione, vedere <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="f064b-113">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="f064b-114">Configurazione impostazioni app</span><span class="sxs-lookup"><span data-stu-id="f064b-114">App settings configuration</span></span>

<span data-ttu-id="f064b-115">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="f064b-115">`wwwroot/appsettings.json`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="f064b-116">Inserire un' <xref:Microsoft.Extensions.Configuration.IConfiguration> istanza in un componente per accedere ai dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="f064b-116">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="provider-configuration"></a><span data-ttu-id="f064b-117">Configurazione provider</span><span class="sxs-lookup"><span data-stu-id="f064b-117">Provider configuration</span></span>

<span data-ttu-id="f064b-118">Nell'esempio seguente viene usato un oggetto <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> per fornire la configurazione aggiuntiva:</span><span class="sxs-lookup"><span data-stu-id="f064b-118">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="f064b-119">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f064b-119">`Program.Main`:</span></span>

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

<span data-ttu-id="f064b-120">Inserire un' <xref:Microsoft.Extensions.Configuration.IConfiguration> istanza in un componente per accedere ai dati di configurazione:</span><span class="sxs-lookup"><span data-stu-id="f064b-120">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="f064b-121">Per leggere altri file di configurazione dalla `wwwroot` cartella alla configurazione, usare un <xref:System.Net.Http.HttpClient> per ottenere il contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="f064b-121">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="f064b-122">Quando si utilizza questo approccio, la <xref:System.Net.Http.HttpClient> registrazione del servizio esistente può utilizzare il client locale creato per leggere il file, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="f064b-122">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="f064b-123">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="f064b-123">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="f064b-124">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f064b-124">`Program.Main`:</span></span>

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

## <a name="authentication-configuration"></a><span data-ttu-id="f064b-125">Configurazione dell'autenticazione</span><span class="sxs-lookup"><span data-stu-id="f064b-125">Authentication configuration</span></span>

<span data-ttu-id="f064b-126">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="f064b-126">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="f064b-127">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f064b-127">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="f064b-128">Configurazione della registrazione</span><span class="sxs-lookup"><span data-stu-id="f064b-128">Logging configuration</span></span>

<span data-ttu-id="f064b-129">Aggiungere un riferimento al pacchetto per [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/) :</span><span class="sxs-lookup"><span data-stu-id="f064b-129">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="f064b-130">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="f064b-130">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="f064b-131">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f064b-131">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="f064b-132">Configurazione del generatore host</span><span class="sxs-lookup"><span data-stu-id="f064b-132">Host builder configuration</span></span>

<span data-ttu-id="f064b-133">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f064b-133">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="f064b-134">Configurazione memorizzata nella cache</span><span class="sxs-lookup"><span data-stu-id="f064b-134">Cached configuration</span></span>

<span data-ttu-id="f064b-135">I file di configurazione vengono memorizzati nella cache per l'uso offline.</span><span class="sxs-lookup"><span data-stu-id="f064b-135">Configuration files are cached for offline use.</span></span> <span data-ttu-id="f064b-136">Con [le applicazioni Web progressive (PWA)](xref:blazor/progressive-web-app)è possibile aggiornare solo i file di configurazione quando si crea una nuova distribuzione.</span><span class="sxs-lookup"><span data-stu-id="f064b-136">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="f064b-137">La modifica dei file di configurazione tra le distribuzioni non ha effetto perché:</span><span class="sxs-lookup"><span data-stu-id="f064b-137">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="f064b-138">Gli utenti dispongono di versioni memorizzate nella cache dei file che continuano a usare.</span><span class="sxs-lookup"><span data-stu-id="f064b-138">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="f064b-139">I `service-worker.js` file e di PWA `service-worker-assets.js` devono essere ricompilati durante la compilazione, che segnalano all'app la prossima visita online dell'utente che l'app è stata ridistribuita.</span><span class="sxs-lookup"><span data-stu-id="f064b-139">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="f064b-140">Per ulteriori informazioni sul modo in cui gli aggiornamenti in background vengono gestiti da PWA, vedere <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="f064b-140">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
