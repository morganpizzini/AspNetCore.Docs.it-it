---
title: Collegamento al browser in ASP.NET Core
author: ncarandini
description: Viene illustrato come Browser Link è una funzionalità di Visual Studio che collega l'ambiente di sviluppo con uno o più Web browser.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 19cc3c2ed91bd9e05df3c036123c78ecbf81fcc0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658851"
---
# <a name="browser-link-in-aspnet-core"></a>Collegamento al browser in ASP.NET Core

Di [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), e [Tom Dykstra](https://github.com/tdykstra)

Collegamento browser è una funzionalità di Visual Studio.Browser Link is a Visual Studio feature. Crea un canale di comunicazione tra l'ambiente di sviluppo e uno o più browser web. È possibile utilizzare Browser Link per aggiornare l'app Web in più browser contemporaneamente, il che è utile per il test tra browser.

## <a name="browser-link-setup"></a>Impostazione del collegamento del browser

::: moniker range=">= aspnetcore-3.0"

Aggiungere il pacchetto [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) al progetto. Per ASP.NET Core Razor Pages o progetti MVC, abilitare anche la compilazione in runtime dei file Razor (*.cshtml*) come descritto in <xref:mvc/views/view-compilation>. Le modifiche della sintassi Razor vengono applicate solo quando è stata abilitata la compilazione di runtime.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Quando si converte un progetto ASP.NET Core 2.0 in ASP.NET Core 2.1 e si passa al [metadocumento Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), installare il pacchetto [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) per la funzionalità di collegamento del browser. I modelli di progetto di `Microsoft.AspNetCore.App` ASP.NET Core 2.1 usano il metapacchetto per impostazione predefinita.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

I modelli di progetto **Applicazione Web**2.0 , **Vuoto**e Applicazione **API Web** ASP.NET utilizzano il [metapacchetto Microsoft.AspNetCore.All](xref:fundamentals/metapackage), che contiene un riferimento al pacchetto per [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/). Pertanto, `Microsoft.AspNetCore.All` l'utilizzo del metapacchetto non richiede ulteriori azioni per rendere Browser Link disponibile per l'uso.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

Il modello di progetto di applicazione **Web** ASP.NET Core 1.x include un riferimento al pacchetto per il pacchetto [Microsoft.VisualStudio.Web.BrowserLink.](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) Altri tipi di progetto richiedono l'aggiunta di un riferimento al pacchetto a `Microsoft.VisualStudio.Web.BrowserLink`.

::: moniker-end

### <a name="configuration"></a>Configurazione

Chiamare `UseBrowserLink` nel metodo `Startup.Configure`:

```csharp
app.UseBrowserLink();
```

La `UseBrowserLink` chiamata viene in `if` genere inserita all'interno di un blocco che abilita solo browser link nell'ambiente di sviluppo. Ad esempio:

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

Per altre informazioni, vedere <xref:fundamentals/environments>.

## <a name="how-to-use-browser-link"></a>Come utilizzare Browser Link

Quando è aperto un progetto ASP.NET Core, Visual Studio mostra il controllo della barra degli strumenti Collegamento browser accanto al controllo della barra degli strumenti **Debug destinazione:When** you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the Debug Target toolbar control:

![Menu a discesa Collegamento browser](using-browserlink/_static/browserLink-dropdown-menu.png)

Dal controllo della barra degli strumenti Browser Link, è possibile:

* Aggiorna l'app Web in più browser contemporaneamente.
* Aprire il **dashboard di collegamento del browser**.
* Attivare o disattivare **Browser Link**. Nota: collegamento browser è disabilitato per impostazione predefinita in Visual Studio.
* Attivare o disattivare [la sincronizzazione automatica CSS](#enable-or-disable-css-auto-sync).

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>Aggiornare l'app Web in più browser contemporaneamente

Per scegliere un singolo Web browser da avviare all'avvio del progetto, utilizzare il menu a discesa nel controllo della barra degli strumenti **Debug destinazione:**

![Menu a discesa F5](using-browserlink/_static/debug-target-dropdown-menu.png)

Per aprire più browser contemporaneamente, scegliere **Sfoglia con...** dallo stesso menu a discesa. Tenere premuto il <kbd>tasto Ctrl</kbd> per selezionare i browser desiderati e quindi fare clic su **Sfoglia**:

![Aprire molti browser contemporaneamente](using-browserlink/_static/open-many-browsers-at-once.png)

La schermata seguente mostra Visual Studio con la visualizzazione Indice aperta e due browser aperti:The following screenshot shows Visual Studio with the Index view open and two open browsers:

![Esempio di sincronizzazione con due browser](using-browserlink/_static/sync-with-two-browsers-example.png)

Passare il puntatore del mouse sul controllo della barra degli strumenti Browser Link per visualizzare i browser connessi al progetto:

![Suggerimento al passaggio del mouse](using-browserlink/_static/hoover-tip.png)

Modificare la visualizzazione Indice e tutti i browser connessi vengono aggiornati quando si fa clic sul pulsante Aggiorna collegamento browser:

![browser-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

Browser Link funziona anche con i browser avviati dall'esterno di Visual Studio e passare all'URL dell'app.

### <a name="the-browser-link-dashboard"></a>Dashboard di collegamento del browser

Aprire la finestra **Browser Link Dashboard** dal menu a discesa Browser Link per gestire la connessione con i browser aperti:

![open-browserlink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

Se non è connesso alcun browser, è possibile avviare una sessione di non debug selezionando il collegamento **Visualizza nel browser:**

![browserlink-dashboard-no-connessioni](using-browserlink/_static/browserlink-dashboard-no-connections.png)

In caso contrario, i browser connessi vengono visualizzati con il percorso della pagina visualizzata da ogni browser:

![browserlink-dashboard-due-connessioni](using-browserlink/_static/browserlink-dashboard-two-connections.png)

È inoltre possibile fare clic sul nome di un singolo browser per aggiornare solo tale browser.

### <a name="enable-or-disable-browser-link"></a>Attivare o disattivare Browser Link

Quando si riattiva Browser Link dopo averlo disattivato, è necessario aggiornare i browser per riconnetterli.

### <a name="enable-or-disable-css-auto-sync"></a>Attivare o disattivare la sincronizzazione automatica CSS

Quando la sincronizzazione automatica CSS è abilitata, i browser connessi vengono aggiornati automaticamente quando si apporta noto ai file CSS.

## <a name="how-it-works"></a>Funzionamento

Browser Link [SignalR](xref:signalr/introduction) utilizza per creare un canale di comunicazione tra Visual Studio e il browser. Quando browser Link è abilitato, SignalR Visual Studio funge da server a cui possono connettersi più client (browser). Browser Link registra anche un componente middleware nella pipeline di richiesta ASP.NET Core. Questo componente inserisce riferimenti speciali `<script>` in ogni richiesta di pagina dal server. È possibile visualizzare i riferimenti agli script selezionando **Visualizza origine** nel `<body>` browser e scorrendo fino alla fine del contenuto del tag:

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

I file di origine non vengono modificati. Il componente middleware inserisce i riferimenti dello script in modo dinamico.

Poiché il codice lato browser è tutto JavaScript, funziona su tutti i browser che SignalR supportano senza richiedere un plug-in del browser.
