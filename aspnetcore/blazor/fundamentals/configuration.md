---
title: Configurazione di ASP.NET Core Blazor
author: guardrex
description: Informazioni sulla configurazione delle Blazor app, incluse le impostazioni dell'app, l'autenticazione e la configurazione della registrazione.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 9ae0dcc16b9debd47a61010953243b0abe499c4f
ms.sourcegitcommit: ca6a1f100c1a3f59999189aa962523442dd4ead1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87443976"
---
# <a name="aspnet-core-no-locblazor-configuration"></a>Configurazione di ASP.NET Core Blazor

> [!NOTE]
> Questo argomento si applica a Blazor WebAssembly . Per indicazioni generali sulla configurazione dell'app ASP.NET Core, vedere <xref:fundamentals/configuration/index> .

Blazor WebAssemblycarica la configurazione dai file di impostazioni dell'app per impostazione predefinita:

* `wwwroot/appsettings.json`
* `wwwroot/appsettings.{ENVIRONMENT}.json`

Anche altri provider di configurazione registrati dall'app possono fornire la configurazione.

Non tutti i provider o le funzionalità del provider sono appropriate per le Blazor WebAssembly app:

* [Provider di configurazione Azure Key Vault](xref:security/key-vault-configuration): il provider non è supportato per l'identità gestita e l'ID applicazione (ID client) con scenari di segreto client. Non è consigliabile usare l'ID applicazione con un segreto client per qualsiasi app ASP.NET Core, in particolare per Blazor WebAssembly le app perché il segreto client non può essere protetto sul lato client per accedere al servizio.
* [Provider di configurazione app Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app): il provider non è appropriato per le Blazor WebAssembly app perché le Blazor WebAssembly app non vengono eseguite in un server in Azure.

> [!WARNING]
> La configurazione in un' Blazor WebAssembly app è visibile agli utenti. **Non archiviare i segreti dell'app o le credenziali nella configurazione.**

Per ulteriori informazioni sui provider di configurazione, vedere <xref:fundamentals/configuration/index> .

## <a name="app-settings-configuration"></a>Configurazione impostazioni app

`wwwroot/appsettings.json`:

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

## <a name="custom-configuration-provider-with-ef-core"></a>Provider di configurazione personalizzato con EF Core

Il provider di configurazione personalizzato con EF Core dimostrato in <xref:fundamentals/configuration/index#custom-configuration-provider> funziona con le Blazor WebAssembly app.

Aggiungere il provider di configurazione dell'esempio con il codice seguente in `Program.Main` ( `Program.cs` ):

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

Inserire un' <xref:Microsoft.Extensions.Configuration.IConfiguration> istanza in un componente per accedere ai dati di configurazione:

```razor
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>
```

## <a name="memory-configuration-source"></a>Origine configurazione memoria

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
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");
        
        ...
    }
}
```

Per leggere altri file di configurazione dalla `wwwroot` cartella alla configurazione, usare un <xref:System.Net.Http.HttpClient> per ottenere il contenuto del file. Quando si utilizza questo approccio, la <xref:System.Net.Http.HttpClient> registrazione del servizio esistente può utilizzare il client locale creato per leggere il file, come illustrato nell'esempio seguente:

`wwwroot/cars.json`:

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

builder.Services.AddScoped(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a>Configurazione dell'autenticazione

`wwwroot/appsettings.json`:

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

Aggiungere un riferimento al pacchetto per [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/) :

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

`wwwroot/appsettings.json`:

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
* I `service-worker.js` file e di PWA `service-worker-assets.js` devono essere ricompilati durante la compilazione, che segnalano all'app la prossima visita online dell'utente che l'app è stata ridistribuita.

Per ulteriori informazioni sul modo in cui gli aggiornamenti in background vengono gestiti da PWA, vedere <xref:blazor/progressive-web-app#background-updates> .
