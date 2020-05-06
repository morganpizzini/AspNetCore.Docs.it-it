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
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>Configurazione del modello di hosting di ASP.NET Core Blazer

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Questo articolo illustra la configurazione del modello di hosting.

## <a name="blazor-webassembly"></a>WebAssembly Blazor

### <a name="environment"></a>Ambiente

Quando si esegue un'app localmente, per impostazione predefinita viene impostato lo sviluppo per l'ambiente. Quando l'app viene pubblicata, per impostazione predefinita viene impostato l'ambiente di produzione.

Un'app webassembly di Blazer ospitata preleva l'ambiente dal server tramite un middleware che comunica l'ambiente al browser aggiungendo l' `blazor-environment` intestazione. Il valore dell'intestazione è l'ambiente. L'app di Blazer ospitata e l'app Server condividono lo stesso ambiente. Per ulteriori informazioni, tra cui come configurare l'ambiente, vedere <xref:fundamentals/environments>.

Per un'app autonoma in esecuzione in locale, il server `blazor-environment` di sviluppo aggiunge l'intestazione per specificare l'ambiente di sviluppo. Per specificare l'ambiente per altri ambienti host, aggiungere l' `blazor-environment` intestazione.

Nell'esempio seguente per IIS aggiungere l'intestazione personalizzata al file *Web. config* pubblicato. Il file *Web. config* si trova nella cartella *bin/Release/{Target Framework}/Publish* :

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
> Per usare un file *Web. config* personalizzato per IIS che non viene sovrascritto quando l'app viene pubblicata nella cartella di *pubblicazione* , <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>vedere.

Ottenere l'ambiente dell'app in un componente inserendo `IWebAssemblyHostEnvironment` e leggendo la `Environment` proprietà:

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Durante l'avvio, `WebAssemblyHostBuilder` espone l' `IWebAssemblyHostEnvironment` oggetto tramite `HostEnvironment` la proprietà, che consente agli sviluppatori di disporre di logica specifica dell'ambiente nel codice:

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

I metodi di estensione pratici seguenti consentono di controllare l'ambiente corrente per i nomi di sviluppo, produzione, gestione temporanea e ambiente personalizzato:

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* ' Outenvironment ("{nome ambiente}")

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

La `IWebAssemblyHostEnvironment.BaseAddress` proprietà può essere utilizzata durante l'avvio quando `NavigationManager` il servizio non è disponibile.

### <a name="configuration"></a>Configurazione

La configurazione viene caricata dal webassembly blazer da:

* File di impostazioni dell'app per impostazione predefinita:
  * *Wwwroot/appSettings. JSON*
  * *Wwwroot/appSettings. {ENVIRONMENT}. JSON*
* Altri [provider di configurazione](xref:fundamentals/configuration/index) registrati dall'app. Non tutti i provider sono appropriati per le app webassembly blazer. Per verificare quali provider sono supportati per l'assembly WebBlazer, è necessario [chiarire i provider di configurazione per blazer WASM (DotNet/AspNetCore. Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).

> [!WARNING]
> La configurazione in un'app webassembly blazer è visibile agli utenti. **Non archiviare i segreti dell'app o le credenziali nella configurazione.**

Per ulteriori informazioni sui provider di configurazione, <xref:fundamentals/configuration/index>vedere.

#### <a name="app-settings-configuration"></a>Configurazione impostazioni app

*wwwroot/appSettings. JSON*:

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

#### <a name="provider-configuration"></a>Configurazione provider

Nell'esempio seguente viene usato <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> un oggetto per fornire la configurazione aggiuntiva:

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

Per leggere altri file di configurazione dalla cartella *wwwroot* alla configurazione, usare un `HttpClient` per ottenere il contenuto del file. Quando si utilizza questo approccio, la `HttpClient` registrazione del servizio esistente può utilizzare il client locale creato per leggere il file, come illustrato nell'esempio seguente:

*wwwroot/Cars. JSON*:

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

#### <a name="authentication-configuration"></a>Configurazione dell'autenticazione

*wwwroot/appSettings. JSON*:

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a>Configurazione della registrazione

*wwwroot/appSettings. JSON*:

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
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a>Configurazione del generatore host

`Program.Main`:

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a>Configurazione memorizzata nella cache

I file di configurazione vengono memorizzati nella cache per l'uso offline. Con [le applicazioni Web progressive (PWA)](xref:blazor/progressive-web-app)è possibile aggiornare solo i file di configurazione quando si crea una nuova distribuzione. La modifica dei file di configurazione tra le distribuzioni non ha effetto perché:

* Gli utenti dispongono di versioni memorizzate nella cache dei file che continuano a usare.
* I file *service-worker. js* e *service-worker-assets. js* di PWA devono essere ricompilati durante la compilazione, che segnalano all'app la prossima visita online dell'utente che l'app è stata ridistribuita.

Per ulteriori informazioni sul modo in cui gli aggiornamenti in background vengono gestiti <xref:blazor/progressive-web-app#background-updates>da PWA, vedere.

### <a name="logging"></a>Registrazione

Per informazioni sul supporto per la registrazione di webassembly blazer, vedere <xref:fundamentals/logging/index#create-logs-in-blazor>.

## <a name="blazor-server"></a>Server Blazor

### <a name="reflect-the-connection-state-in-the-ui"></a>Riflette lo stato di connessione nell'interfaccia utente

Quando il client rileva che la connessione è stata persa, viene visualizzata un'interfaccia utente predefinita quando il client tenta di riconnettersi. Se la riconnessione non riesce, all'utente viene offerta l'opzione per riprovare.

Per personalizzare l'interfaccia utente, definire un elemento con `id` un `components-reconnect-modal` valore di `<body>` nell'oggetto della pagina Razor *_Host. cshtml* :

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Nella tabella seguente vengono descritte le classi CSS applicate all' `components-reconnect-modal` elemento.

| Classe CSS                       | Indica&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Connessione persa. È in corso un tentativo di riconnessione del client. Mostra il modale. |
| `components-reconnect-hide`     | Viene ristabilita una connessione attiva al server. Nascondere il modale. |
| `components-reconnect-failed`   | Riconnessione non riuscita. probabilmente a causa di un errore di rete. Per tentare la riconnessione, `window.Blazor.reconnect()`chiamare. |
| `components-reconnect-rejected` | Riconnessione rifiutata. Il server è stato raggiunto ma ha rifiutato la connessione e lo stato dell'utente nel server è andato perso. Per ricaricare l'app, `location.reload()`chiamare. Questo stato di connessione può verificarsi nei casi seguenti:<ul><li>Si verifica un arresto anomalo del circuito sul lato server.</li><li>Il client viene disconnesso abbastanza a lungo da consentire al server di eliminare lo stato dell'utente. Le istanze dei componenti con cui l'utente interagisce sono state eliminate.</li><li>Il server viene riavviato oppure il processo di lavoro dell'app viene riciclato.</li></ul> |

### <a name="render-mode"></a>Modalità di rendering

Per impostazione predefinita, le app del server Blaze sono configurate per eseguire il prerendering dell'interfaccia utente nel server prima che venga stabilita la connessione client al server. Questa impostazione è configurata nella pagina Razor *_Host. cshtml* :

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode`Configura se il componente:

* Viene preeseguito nella pagina.
* Viene visualizzato come HTML statico nella pagina o se include le informazioni necessarie per eseguire il bootstrap di un'app Blazer dall'agente utente.

| `RenderMode`        | Description |
| ------------------- | ----------- |
| `ServerPrerendered` | Esegue il rendering del componente in HTML statico e include un marcatore Blazor per un'app Server. Quando l'agente utente viene avviato, questo marcatore viene usato per il Blazor bootstrap di un'app. |
| `Server`            | Esegue il rendering di un marcatore per un' Blazor app Server. L'output del componente non è incluso. Quando l'agente utente viene avviato, questo marcatore viene usato per il Blazor bootstrap di un'app. |
| `Static`            | Esegue il rendering del componente in HTML statico. |

Il rendering dei componenti server da una pagina HTML statica non è supportato.

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Configurare il SignalR client per Blazor le app Server

In alcuni casi, è necessario configurare SignalR il client usato Blazor dalle app Server. È ad esempio possibile configurare la SignalR registrazione sul client per diagnosticare un problema di connessione.

Per configurare il SignalR client nel file *pages/_Host. cshtml* :

* Aggiungere un `autostart="false"` attributo al `<script>` tag per lo `blazor.server.js` script.
* Chiamare `Blazor.start` e passare un oggetto di configurazione che specifichi il SignalR generatore.

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

### <a name="logging"></a>Registrazione

Per informazioni sul Blazor supporto per la registrazione del <xref:fundamentals/logging/index#create-logs-in-blazor>server, vedere.
