---
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>BlazorConfigurazione del modello di hosting ASP.NET Core

Di [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Questo articolo illustra la configurazione del modello di hosting.

## <a name="blazor-webassembly"></a>BlazorWebassembly

### <a name="environment"></a>Environment

Quando si esegue un'app localmente, per impostazione predefinita viene impostato lo sviluppo per l'ambiente. Quando l'app viene pubblicata, per impostazione predefinita viene impostato l'ambiente di produzione.

Un' Blazor app webassembly ospitata preleva l'ambiente dal server tramite un middleware che comunica l'ambiente al browser aggiungendo l' `blazor-environment` intestazione. Il valore dell'intestazione è l'ambiente. L'app ospitata Blazor e l'app Server condividono lo stesso ambiente. Per ulteriori informazioni, tra cui come configurare l'ambiente, vedere <xref:fundamentals/environments> .

Per un'app autonoma in esecuzione in locale, il server di sviluppo aggiunge l' `blazor-environment` intestazione per specificare l'ambiente di sviluppo. Per specificare l'ambiente per altri ambienti host, aggiungere l' `blazor-environment` intestazione.

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
> Per usare un file *Web. config* personalizzato per IIS che non viene sovrascritto quando l'app viene pubblicata nella cartella di *pubblicazione* , vedere <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> .

Ottenere l'ambiente dell'app in un componente inserendo `IWebAssemblyHostEnvironment` e leggendo la `Environment` proprietà:

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Durante l'avvio, `WebAssemblyHostBuilder` espone l'oggetto `IWebAssemblyHostEnvironment` tramite la `HostEnvironment` proprietà, che consente agli sviluppatori di disporre di logica specifica dell'ambiente nel codice:

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

La `IWebAssemblyHostEnvironment.BaseAddress` proprietà può essere utilizzata durante l'avvio quando il `NavigationManager` servizio non è disponibile.

### <a name="configuration"></a>Configurazione

BlazorWebassembly carica la configurazione da:

* File di impostazioni dell'app per impostazione predefinita:
  * *Wwwroot/appSettings. JSON*
  * *Wwwroot/appSettings. {ENVIRONMENT}. JSON*
* Altri [provider di configurazione](xref:fundamentals/configuration/index) registrati dall'app. Non tutti i provider sono appropriati per le Blazor app webassembly. Per verificare quali provider sono supportati per Blazor webassembly, è necessario [chiarire i provider di configurazione per Blazor WASM (DotNet/AspNetCore. docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).

> [!WARNING]
> La configurazione in un' Blazor app webassembly è visibile agli utenti. **Non archiviare i segreti dell'app o le credenziali nella configurazione.**

Per ulteriori informazioni sui provider di configurazione, vedere <xref:fundamentals/configuration/index> .

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

Per ulteriori informazioni sul modo in cui gli aggiornamenti in background vengono gestiti da PWA, vedere <xref:blazor/progressive-web-app#background-updates> .

### <a name="logging"></a>Registrazione

Per informazioni sul Blazor supporto della registrazione di webassembly, vedere <xref:fundamentals/logging/index#create-logs-in-blazor> .

## <a name="blazor-server"></a>BlazorServer

### <a name="reflect-the-connection-state-in-the-ui"></a>Riflette lo stato di connessione nell'interfaccia utente

Quando il client rileva che la connessione è stata persa, viene visualizzata un'interfaccia utente predefinita quando il client tenta di riconnettersi. Se la riconnessione non riesce, all'utente viene offerta l'opzione per riprovare.

Per personalizzare l'interfaccia utente, definire un elemento con un valore `id` di `components-reconnect-modal` nell'oggetto `<body>` della pagina *_Host. cshtml* Razor :

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Nella tabella seguente vengono descritte le classi CSS applicate all' `components-reconnect-modal` elemento.

| Classe CSS                       | Indica&hellip; |
| ---
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

---------------- | Titolo---: "ASP.NET Core Blazor configurazione del modello di hosting" autore: Descrizione: "informazioni sulla Blazor configurazione del modello di hosting, incluse informazioni su come integrare i Razor componenti in Razor pagine e app MVC".
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

--------- | | `components-reconnect-show`     | Connessione persa. È in corso un tentativo di riconnessione del client. Mostra il modale. | | `components-reconnect-hide`     | Viene ristabilita una connessione attiva al server. Nascondere il modale. | | `components-reconnect-failed`   | Riconnessione non riuscita. probabilmente a causa di un errore di rete. Per tentare la riconnessione, chiamare `window.Blazor.reconnect()` . | | `components-reconnect-rejected` | Riconnessione rifiutata. Il server è stato raggiunto ma ha rifiutato la connessione e lo stato dell'utente nel server è andato perso. Per ricaricare l'app, chiamare `location.reload()` . Questo stato di connessione può verificarsi nei casi seguenti:<ul><li>Si verifica un arresto anomalo del circuito sul lato server.</li><li>Il client viene disconnesso abbastanza a lungo da consentire al server di eliminare lo stato dell'utente. Le istanze dei componenti con cui l'utente interagisce sono state eliminate.</li><li>Il server viene riavviato oppure il processo di lavoro dell'app viene riciclato.</li></ul> |

### <a name="render-mode"></a>Modalità di rendering

BlazorPer impostazione predefinita, le app Server sono impostate per eseguire il prerendering dell'interfaccia utente nel server prima che venga stabilita la connessione client al server. Questa impostazione è configurata nella pagina *_Host. cshtml* Razor :

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
* Viene sottoposto a rendering come HTML statico nella pagina o se include le informazioni necessarie per il bootstrap di un' Blazor app dall'agente utente.

| `RenderMode`        | Description |
| ---
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

---------- | Titolo---: "ASP.NET Core Blazor configurazione del modello di hosting" autore: Descrizione: "informazioni sulla Blazor configurazione del modello di hosting, incluse informazioni su come integrare i Razor componenti in Razor pagine e app MVC".
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

-
title:' ASP.NET Core la Blazor configurazione del modello di hosting ' Author: Description:' informazioni sulla Blazor configurazione del modello di hosting, inclusa la modalità di integrazione dei Razor componenti nelle Razor pagine e nelle app MVC '.
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

------ | | `ServerPrerendered` | Esegue il rendering del componente in HTML statico e include un marcatore per un' Blazor app Server. Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app. | | `Server`            | Esegue il rendering di un marcatore per un' Blazor app Server. L'output del componente non è incluso. Quando l'agente utente viene avviato, questo marcatore viene usato per il bootstrap di un' Blazor app. | | `Static`            | Esegue il rendering del componente in HTML statico. |

Il rendering dei componenti server da una pagina HTML statica non è supportato.

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Configurare il SignalR client per le Blazor app Server

In alcuni casi, è necessario configurare il SignalR client usato dalle Blazor app Server. È ad esempio possibile configurare la registrazione sul SignalR client per diagnosticare un problema di connessione.

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

Per informazioni sul Blazor supporto per la registrazione del server, vedere <xref:fundamentals/logging/index#create-logs-in-blazor> .
