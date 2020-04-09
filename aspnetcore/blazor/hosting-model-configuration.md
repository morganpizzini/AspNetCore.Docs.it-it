---
title: Configurazione Blazor del modello di hosting ASP.NET Core
author: guardrex
description: Informazioni Blazor sulla configurazione del modello di hosting, incluso come integrare i componenti Razor nelle pagine Razor e nelle app MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/24/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 1f71ac63bbe9dc9d56cfca2ded19a5b863be828f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80306430"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>ASP.NET configurazione del modello di hosting Core Blazor

Di [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Questo articolo illustra la configurazione del modello di hosting.

## <a name="blazor-webassembly"></a>WebAssembly Blazor

A partire dalla versione ASP.NET Core 3.2 Preview 3, Blazor WebAssembly supporta la configurazione da:

* *wwwroot/appsettings.json*
* *wwwroot/appsettings. AMBIENTE.*

In un'app Blazor Hosted, [l'ambiente](xref:fundamentals/environments) di runtime corrisponde al valore dell'app server.

Quando si esegue l'app in locale, l'ambiente predefinito è Sviluppo.When running the app locally, the environment defaults to Development. Quando l'app viene pubblicata, l'ambiente viene impostato su Produzione per impostazione predefinita. Per ulteriori informazioni, incluso come <xref:fundamentals/environments>configurare l'ambiente, vedere .

> [!WARNING]
> La configurazione in un'app Blazor WebAssembly è visibile agli utenti. **Non archiviare segreti o credenziali dell'app nella configurazione.**

I file di configurazione vengono memorizzati nella cache per l'utilizzo offline. Con [Progressive Web Applications (PWAs),](xref:blazor/progressive-web-app)è possibile aggiornare i file di configurazione solo quando si crea una nuova distribuzione. La modifica dei file di configurazione tra le distribuzioni non ha alcun effetto perché:Editing configuration files between deployments has no effect because:

* Gli utenti hanno memorizzato nella cache le versioni dei file che continuano a utilizzare.
* I file *service-worker.js* e *service-worker-assets.js* di PWA devono essere ricompilati durante la compilazione, che segnalano all'app la prossima visita online dell'utente che l'app è stata ridistribuita.

Per ulteriori informazioni sulla modalità di gestione degli <xref:blazor/progressive-web-app#background-updates>aggiornamenti in background da parte dei PWA, vedere .

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
