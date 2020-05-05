---
title: Browser Link in ASP.NET Core
author: ncarandini
description: Viene illustrato come Browser Link è una funzionalità di Visual Studio che collega l'ambiente di sviluppo a uno o più Web browser.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 619d19ba90298b2455d4a558fea138c86a751f07
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773657"
---
# <a name="browser-link-in-aspnet-core"></a>Browser Link in ASP.NET Core

Di [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson)e [Tom Dykstra](https://github.com/tdykstra)

Browser Link è una funzionalità di Visual Studio. Viene creato un canale di comunicazione tra l'ambiente di sviluppo e uno o più Web browser. È possibile usare Browser Link per aggiornare l'app Web in diversi browser contemporaneamente, operazione utile per i test tra browser.

## <a name="browser-link-setup"></a>Installazione di Browser Link

::: moniker range=">= aspnetcore-3.0"

Aggiungere il pacchetto [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) al progetto. Per ASP.NET Core Razor pagine o progetti MVC, abilitare anche la compilazione in Razor fase di esecuzione dei file (con*estensione cshtml*) come descritto in <xref:mvc/views/view-compilation>. Razorle modifiche alla sintassi vengono applicate solo quando è stata abilitata la compilazione del runtime.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Quando si converte un progetto ASP.NET Core 2,0 in ASP.NET Core 2,1 e si esegue la transizione al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), installare il pacchetto [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) per browser link funzionalità. Per impostazione predefinita, i modelli di `Microsoft.AspNetCore.App` progetto ASP.NET Core 2,1 usano il metapacchetto.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

I modelli di progetto **applicazione Web**, **vuoto**e **API Web** di ASP.NET Core 2,0 usano il [metapacchetto Microsoft. AspNetCore. All](xref:fundamentals/metapackage), che contiene un riferimento al pacchetto per [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/). Pertanto, l'utilizzo `Microsoft.AspNetCore.All` del metapacchetto non richiede ulteriori azioni per rendere browser link disponibili per l'utilizzo.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

Il modello di progetto **applicazione Web** ASP.NET Core 1. x include un riferimento al pacchetto per il pacchetto [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) . Per gli altri tipi di progetto è necessario aggiungere un riferimento `Microsoft.VisualStudio.Web.BrowserLink`al pacchetto.

::: moniker-end

### <a name="configuration"></a>Configurazione

Chiamare `UseBrowserLink` nel metodo `Startup.Configure`:

```csharp
app.UseBrowserLink();
```

La `UseBrowserLink` chiamata viene in genere posizionata `if` all'interno di un blocco che consente solo browser link nell'ambiente di sviluppo. Ad esempio:

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

Per altre informazioni, vedere <xref:fundamentals/environments>.

## <a name="how-to-use-browser-link"></a>Come usare Browser Link

Quando si dispone di un progetto ASP.NET Core aperto, Visual Studio Mostra il controllo della barra degli strumenti Browser Link accanto al controllo della barra degli strumenti della **destinazione di debug** :

![Menu a discesa Browser Link](using-browserlink/_static/browserLink-dropdown-menu.png)

Dal controllo Browser Link barra degli strumenti è possibile:

* Aggiornare l'app Web in più browser contemporaneamente.
* Aprire il **Dashboard browser link**.
* Abilitare o disabilitare **browser link**. Nota: per impostazione predefinita, Browser Link è disabilitato in Visual Studio.
* Abilitare o disabilitare la [sincronizzazione automatica CSS](#enable-or-disable-css-auto-sync).

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>Aggiornare l'app Web in più browser contemporaneamente

Per scegliere un singolo Web browser da avviare all'avvio del progetto, usare il menu a discesa nel controllo della barra degli strumenti della **destinazione di debug** :

![Menu a discesa F5](using-browserlink/_static/debug-target-dropdown-menu.png)

Per aprire più browser contemporaneamente, scegliere **Sfoglia con...** dallo stesso elenco a discesa. Tenere premuto il tasto <kbd>CTRL</kbd> per selezionare i browser desiderati, quindi fare clic su **Sfoglia**:

![Apri molti browser contemporaneamente](using-browserlink/_static/open-many-browsers-at-once.png)

La schermata seguente mostra Visual Studio con la visualizzazione indice aperta e due browser aperti:

![Esempio di sincronizzazione con due browser](using-browserlink/_static/sync-with-two-browsers-example.png)

Passare il puntatore del mouse sul controllo della barra degli strumenti Browser Link per visualizzare i browser connessi al progetto:

![Suggerimento per il passaggio del mouse](using-browserlink/_static/hoover-tip.png)

Modificare la visualizzazione dell'indice e tutti i browser connessi vengono aggiornati quando si fa clic sul pulsante Browser Link Refresh:

![browser-Sincronizza modifiche](using-browserlink/_static/browsers-sync-to-changes.png)

Browser Link funziona anche con i browser che si avviano dall'esterno di Visual Studio e si passa all'URL dell'app.

### <a name="the-browser-link-dashboard"></a>Dashboard Browser Link

Aprire la finestra **Dashboard browser link** dal menu a discesa browser link per gestire la connessione con i browser aperti:

![Apri-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

Se non è connesso alcun browser, è possibile avviare una sessione non di debug selezionando la **vista nel** collegamento del browser:

![browserlink-dashboard-no-Connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

In caso contrario, i browser connessi vengono visualizzati con il percorso della pagina visualizzata da ogni browser:

![browserlink-dashboard-due connessioni](using-browserlink/_static/browserlink-dashboard-two-connections.png)

È anche possibile fare clic su un singolo nome del browser per aggiornare solo quel browser.

### <a name="enable-or-disable-browser-link"></a>Abilitare o disabilitare Browser Link

Quando si riabilita Browser Link dopo averla disabilitata, è necessario aggiornare i browser per riconnetterli.

### <a name="enable-or-disable-css-auto-sync"></a>Abilitare o disabilitare la sincronizzazione automatica CSS

Quando è abilitata la sincronizzazione automatica CSS, i browser connessi vengono aggiornati automaticamente quando si apportano modifiche ai file CSS.

## <a name="how-it-works"></a>Funzionamento

Browser Link USA [SignalR](xref:signalr/introduction) per creare un canale di comunicazione tra Visual Studio e il browser. Quando Browser Link è abilitato, Visual Studio funge da SignalR server a cui possono connettersi più client (browser). Browser Link registra anche un componente middleware nella pipeline delle richieste di ASP.NET Core. Questo componente inserisce riferimenti speciali `<script>` in ogni richiesta di pagina dal server. È possibile visualizzare i riferimenti agli script selezionando **Visualizza origine** nel browser e scorrendo fino alla fine del contenuto `<body>` del Tag:

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

I file di origine non sono stati modificati. Il componente middleware inserisce i riferimenti allo script in modo dinamico.

Poiché il codice sul lato browser è tutto JavaScript, funziona su tutti i browser che SignalR supportano senza richiedere un plug-in del browser.
