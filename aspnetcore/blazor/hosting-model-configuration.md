---
title: Configurazione Blazor del modello di hosting ASP.NET Core
author: guardrex
description: Informazioni Blazor sulla configurazione del modello di hosting, incluso come integrare i componenti Razor nelle pagine Razor e nelle app MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 6a3731657d11faed0b005b429058343b2be4c44b
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791470"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET configurazione del modello di hosting Core Blazor

Di [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Questo articolo illustra la configurazione del modello di hosting.

## <a name="blazor-webassembly"></a>WebAssembly Blazor

### <a name="environment"></a>Environment

Quando si esegue un'app in locale, l'ambiente predefinito è Sviluppo.When running an app locally, the environment defaults to Development. Quando l'app viene pubblicata, l'ambiente viene impostato su Produzione per impostazione predefinita.

Un'app Blazor WebAssembly ospitata preleva l'ambiente dal server tramite un middleware che comunica l'ambiente al browser aggiungendo l'intestazione. `blazor-environment` Il valore dell'intestazione è l'ambiente. L'app Blazor ospitata e l'app server condividono lo stesso ambiente. Per ulteriori informazioni, incluso come <xref:fundamentals/environments>configurare l'ambiente, vedere .

Per un'app autonoma in esecuzione `blazor-environment` in locale, il server di sviluppo aggiunge l'intestazione per specificare l'ambiente di sviluppo. Per specificare l'ambiente per `blazor-environment` altri ambienti di hosting, aggiungere l'intestazione.

Nell'esempio seguente per IIS aggiungere l'intestazione personalizzata al file *web.config* pubblicato. Il file *web.config* si trova nella cartella *bin/Release/'TARGET FRAMEWORK/publish:*

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
> Per utilizzare un file *web.config* personalizzato per IIS che non viene sovrascritto <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>quando l'app viene pubblicata nella cartella di *pubblicazione,* vedere .

Ottenere l'ambiente dell'app in `IWebAssemblyHostEnvironment` un componente `Environment` inserendo e leggendo la proprietà:

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Durante l'avvio, l'espone `WebAssemblyHostBuilder` la `IWebAssemblyHostEnvironment` tramite la proprietà , che consente agli sviluppatori di disporre di logica specifica dell'ambiente nel codice:During startup, the exposes the through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

I seguenti metodi di estensione pratico consentono di controllare l'ambiente corrente per i nomi di ambiente personalizzato, produzione, gestione temporanea e personalizzati:

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* 'IsEnvironment("'ENVIRONMENT NAME'")

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

La `IWebAssemblyHostEnvironment.BaseAddress` proprietà può essere utilizzata `NavigationManager` durante l'avvio quando il servizio non è disponibile.

### <a name="configuration"></a>Configurazione

A partire dalla versione ASP.NET Core 3.2 Preview 3[(la versione corrente è 3.2 Preview 4](xref:blazor/get-started)), Blazor WebAssembly supporta la configurazione da:

* *wwwroot/appsettings.json*
* *wwwroot/appsettings. AMBIENTE.*

Aggiungere un file *appsettings.json* nella cartella *wwwroot:*

```json
{
  "message": "Hello from config!"
}
```

Inserire <xref:Microsoft.Extensions.Configuration.IConfiguration> un'istanza in un componente per accedere ai dati di configurazione:Inject an instance into a component to access the configuration data:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

> [!WARNING]
> La configurazione in un'app Blazor WebAssembly è visibile agli utenti. **Non archiviare segreti o credenziali dell'app nella configurazione.**

I file di configurazione vengono memorizzati nella cache per l'utilizzo offline. Con [Progressive Web Applications (PWAs),](xref:blazor/progressive-web-app)è possibile aggiornare i file di configurazione solo quando si crea una nuova distribuzione. La modifica dei file di configurazione tra le distribuzioni non ha alcun effetto perché:Editing configuration files between deployments has no effect because:

* Gli utenti hanno memorizzato nella cache le versioni dei file che continuano a utilizzare.
* I file *service-worker.js* e *service-worker-assets.js* di PWA devono essere ricompilati durante la compilazione, che segnalano all'app la prossima visita online dell'utente che l'app è stata ridistribuita.

Per ulteriori informazioni sulla modalità di gestione degli <xref:blazor/progressive-web-app#background-updates>aggiornamenti in background da parte dei PWA, vedere .

### <a name="logging"></a>Registrazione

Per informazioni sul supporto della registrazione Blazor WebAssembly, vedere <xref:fundamentals/logging/index#create-logs-in-blazor-webassembly>.

## <a name="blazor-server"></a>Server Blazor

### <a name="reflect-the-connection-state-in-the-ui"></a>Riflettere lo stato della connessione nell'interfaccia utente

Quando il client rileva che la connessione è stata persa, un'interfaccia utente predefinita viene visualizzata all'utente mentre il client tenta di riconnettersi. Se la riconnessione non riesce, all'utente viene fornita l'opzione per riprovare.

Per personalizzare l'interfaccia utente, `id` `components-reconnect-modal` definire `<body>` un elemento con un di nella pagina *Razor _Host.cshtml:*

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Nella tabella seguente vengono descritte `components-reconnect-modal` le classi CSS applicate all'elemento.

| Classe CSS                       | Indica&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Una connessione persa. Il client sta tentando di riconnettersi. Mostra il modale. |
| `components-reconnect-hide`     | Viene ristabilita una connessione attiva al server. Nascondi il modale. |
| `components-reconnect-failed`   | Riconnessione non riuscita, probabilmente a causa di un errore di rete. Per tentare la `window.Blazor.reconnect()`riconnessione, chiamare . |
| `components-reconnect-rejected` | Riconnessione rifiutata. Il server è stato raggiunto ma ha rifiutato la connessione e lo stato dell'utente sul server viene perso. Per ricaricare l'app, chiamare `location.reload()`. Questo stato di connessione può verificarsi quando:This connection state may result when:<ul><li>Si verifica un arresto anomalo nel circuito lato server.</li><li>Il client viene disconnesso abbastanza a lungo da richiedere al server di eliminare lo stato dell'utente. Le istanze dei componenti con cui l'utente interagisce vengono eliminate.</li><li>Il server viene riavviato o il processo di lavoro dell'app viene riciclato.</li></ul> |

### <a name="render-mode"></a>Modalità di rendering

Le app del server Blazor sono configurate per impostazione predefinita per eseguire il prerendering dell'interfaccia utente sul server prima che venga stabilita la connessione client al server. Questo è impostato nella pagina *_Host.cshtml* Razor:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode`configura se il componente:

* Viene eseguito il prerendering nella pagina.
* Viene eseguito il rendering come HTML statico nella pagina o se include le informazioni necessarie per eseguire il bootstrap di un'app Blazor dall'agente utente.

| `RenderMode`        | Descrizione |
| ------------------- | ----------- |
| `ServerPrerendered` | Esegue il rendering del componente in Blazor codice HTML statico e include un marcatore per un'app Server. Quando l'agente utente viene avviato, Blazor questo marcatore viene usato per eseguire il bootstrap di un'app. |
| `Server`            | Esegue il rendering Blazor di un marcatore per un'app Server. L'output del componente non è incluso. Quando l'agente utente viene avviato, Blazor questo marcatore viene usato per eseguire il bootstrap di un'app. |
| `Static`            | Esegue il rendering del componente in codice HTML statico. |

Il rendering dei componenti server da una pagina HTML statica non è supportato.

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Rendering di componenti interattivi con stato da pagine e viste RazorRender stateful interactive components from Razor pages and views

I componenti interattivi con stato possono essere aggiunti a una pagina razor o a una visualizzazione.

Quando viene eseguito il rendering della pagina o della visualizzazione:

* Il rendering del componente viene eseguito con il prerendering della pagina o della vista.
* Lo stato iniziale del componente utilizzato per il prerendering viene perso.
* Il nuovo stato del SignalR componente viene creato quando viene stabilita la connessione.

La pagina Razor `Counter` seguente esegue il rendering di un componente:

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Rendering di componenti non interattivi da pagine e viste Razor

Nella pagina Razor seguente, il rendering del `Counter` componente viene eseguito in modo statico con un valore iniziale specificato utilizzando un form:

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Poiché `MyComponent` viene eseguito il rendering statico, il componente non può essere interattivo.

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>Configurare SignalR il Blazor client per le app server

A volte, è SignalR necessario configurare il client utilizzato dalle Blazor app Server. Ad esempio, è possibile configurare SignalR la registrazione sul client per diagnosticare un problema di connessione.

Per configurare il SignalR client nel file *Pages/_Host.cshtml:*

* Aggiungere `autostart="false"` un attributo `<script>` al `blazor.server.js` tag per lo script.
* Chiamare `Blazor.start` e passare un oggetto di SignalR configurazione che specifica il generatore.

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
