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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/configuration
ms.openlocfilehash: b43eae03c71cabbaafa2bc0d704765e89f743279
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103850"
---
# <a name="aspnet-core-blazor-configuration"></a>Configurazione di ASP.NET Core Blazor

> [!NOTE]
> Questo argomento si applica a Blazor webassembly. Per indicazioni generali sulla configurazione dell'app ASP.NET Core, vedere <xref:fundamentals/configuration/index> .

BlazorWebassembly carica la configurazione da:

* File di impostazioni dell'app per impostazione predefinita:
  * *Wwwroot/appsettings.js*
  * *Wwwroot/appSettings. {ENVIRONMENT}. JSON*
* Altri [provider di configurazione](xref:fundamentals/configuration/index) registrati dall'app. Non tutti i provider sono appropriati per le Blazor app webassembly. Per verificare quali provider sono supportati per Blazor webassembly, è necessario [chiarire i provider di configurazione per Blazor WASM (DotNet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).

> [!WARNING]
> La configurazione in un' Blazor app webassembly è visibile agli utenti. **Non archiviare i segreti dell'app o le credenziali nella configurazione.**

Per ulteriori informazioni sui provider di configurazione, vedere <xref:fundamentals/configuration/index> .

## <a name="app-settings-configuration"></a>Configurazione impostazioni app

*wwwroot/appsettings.jsin*:

```json
{
  "message": "Hello from config!"
}
```

Inserire un' <xref:Microsoft.Extensions.Configuration.IConfiguration> istanza in un componente per accedere ai dati di configurazione:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="provider-configuration"></a>Configurazione provider

Nell'esempio seguente viene usato un oggetto <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> per fornire la configurazione aggiuntiva:

`Program.Main`:

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

Inserire un' <xref:Microsoft.Extensions.Configuration.IConfiguration> istanza in un componente per accedere ai dati di configurazione:

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

Per leggere altri file di configurazione dalla cartella *wwwroot* alla configurazione, usare un <xref:System.Net.Http.HttpClient> per ottenere il contenuto del file. Quando si utilizza questo approccio, la <xref:System.Net.Http.HttpClient> registrazione del servizio esistente può utilizzare il client locale creato per leggere il file, come illustrato nell'esempio seguente:

*wwwroot/cars.jsin*:

```json
{
    "size": "tiny"
}
```

`Program.Main`:

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

## <a name="authentication-configuration"></a>Configurazione dell'autenticazione

*wwwroot/appsettings.jsin*:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a>Configurazione della registrazione

Aggiungere un riferimento al pacchetto per [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

*wwwroot/appsettings.jsin*:

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

`Program.Main`:

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a>Configurazione del generatore host

`Program.Main`:

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a>Configurazione memorizzata nella cache

I file di configurazione vengono memorizzati nella cache per l'uso offline. Con [le applicazioni Web progressive (PWA)](xref:blazor/progressive-web-app)è possibile aggiornare solo i file di configurazione quando si crea una nuova distribuzione. La modifica dei file di configurazione tra le distribuzioni non ha effetto perché:

* Gli utenti dispongono di versioni memorizzate nella cache dei file che continuano a usare.
* I file di *service-worker.js* e *service-worker-assets.js* di PWA devono essere ricompilati durante la compilazione, che segnalano all'app la prossima visita online dell'utente che l'app è stata ridistribuita.

Per ulteriori informazioni sul modo in cui gli aggiornamenti in background vengono gestiti da PWA, vedere <xref:blazor/progressive-web-app#background-updates> .
