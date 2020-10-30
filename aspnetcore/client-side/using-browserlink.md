---
title: Browser Link in ASP.NET Core
author: ncarandini
description: Viene illustrato come Browser Link è una funzionalità di Visual Studio che collega l'ambiente di sviluppo a uno o più Web browser.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
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
uid: client-side/using-browserlink
ms.openlocfilehash: 80f05acab55af973faf08b5db79ea4cbaf896b14
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054489"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="e678c-103">Browser Link in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e678c-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="e678c-104">Di [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson)e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="e678c-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="e678c-105">Browser Link è una funzionalità di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e678c-105">Browser Link is a Visual Studio feature.</span></span> <span data-ttu-id="e678c-106">Viene creato un canale di comunicazione tra l'ambiente di sviluppo e uno o più Web browser.</span><span class="sxs-lookup"><span data-stu-id="e678c-106">It creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="e678c-107">È possibile usare Browser Link per aggiornare l'app Web in diversi browser contemporaneamente, operazione utile per i test tra browser.</span><span class="sxs-lookup"><span data-stu-id="e678c-107">You can use Browser Link to refresh your web app in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="e678c-108">Installazione di Browser Link</span><span class="sxs-lookup"><span data-stu-id="e678c-108">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e678c-109">Aggiungere il pacchetto [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) al progetto.</span><span class="sxs-lookup"><span data-stu-id="e678c-109">Add the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package to your project.</span></span> <span data-ttu-id="e678c-110">Per ASP.NET Core :::no-loc(Razor)::: pagine o progetti MVC, abilitare anche la compilazione in fase di esecuzione dei :::no-loc(Razor)::: file (con *estensione cshtml* ) come descritto in <xref:mvc/views/view-compilation> .</span><span class="sxs-lookup"><span data-stu-id="e678c-110">For ASP.NET Core :::no-loc(Razor)::: Pages or MVC projects, also enable runtime compilation of :::no-loc(Razor)::: ( *.cshtml* ) files as described in <xref:mvc/views/view-compilation>.</span></span> <span data-ttu-id="e678c-111">:::no-loc(Razor)::: le modifiche alla sintassi vengono applicate solo quando è stata abilitata la compilazione del runtime.</span><span class="sxs-lookup"><span data-stu-id="e678c-111">:::no-loc(Razor)::: syntax changes are applied only when runtime compilation has been enabled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="e678c-112">Quando si converte un progetto ASP.NET Core 2,0 in ASP.NET Core 2,1 e si esegue la transizione al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), installare il pacchetto [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) per browser link funzionalità.</span><span class="sxs-lookup"><span data-stu-id="e678c-112">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for Browser Link functionality.</span></span> <span data-ttu-id="e678c-113">Per impostazione predefinita, i modelli di progetto ASP.NET Core 2,1 usano il `Microsoft.AspNetCore.App` metapacchetto.</span><span class="sxs-lookup"><span data-stu-id="e678c-113">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="e678c-114">I modelli di progetto **applicazione Web** , **vuoto** e **API Web** di ASP.NET Core 2,0 usano il [metapacchetto Microsoft. AspNetCore. All](xref:fundamentals/metapackage), che contiene un riferimento al pacchetto per [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span><span class="sxs-lookup"><span data-stu-id="e678c-114">The ASP.NET Core 2.0 **Web Application** , **Empty** , and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="e678c-115">Pertanto, l'utilizzo del `Microsoft.AspNetCore.All` metapacchetto non richiede ulteriori azioni per rendere browser link disponibili per l'utilizzo.</span><span class="sxs-lookup"><span data-stu-id="e678c-115">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="e678c-116">Il modello di progetto **applicazione Web** ASP.NET Core 1. x include un riferimento al pacchetto per il pacchetto [Microsoft. VisualStudio. Web. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) .</span><span class="sxs-lookup"><span data-stu-id="e678c-116">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="e678c-117">Per gli altri tipi di progetto è necessario aggiungere un riferimento al pacchetto `Microsoft.VisualStudio.Web.BrowserLink` .</span><span class="sxs-lookup"><span data-stu-id="e678c-117">Other project types require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="e678c-118">Configurazione</span><span class="sxs-lookup"><span data-stu-id="e678c-118">Configuration</span></span>

<span data-ttu-id="e678c-119">Chiamare `UseBrowserLink` nel metodo `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="e678c-119">Call `UseBrowserLink` in the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="e678c-120">La `UseBrowserLink` chiamata viene in genere posizionata all'interno di un `if` blocco che consente solo browser link nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="e678c-120">The `UseBrowserLink` call is typically placed inside an `if` block that only enables Browser Link in the Development environment.</span></span> <span data-ttu-id="e678c-121">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="e678c-121">For example:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="e678c-122">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="e678c-122">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="e678c-123">Come usare Browser Link</span><span class="sxs-lookup"><span data-stu-id="e678c-123">How to use Browser Link</span></span>

<span data-ttu-id="e678c-124">Quando si dispone di un progetto ASP.NET Core aperto, Visual Studio Mostra il controllo della barra degli strumenti Browser Link accanto al controllo della barra degli strumenti della **destinazione di debug** :</span><span class="sxs-lookup"><span data-stu-id="e678c-124">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Menu a discesa Browser Link](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="e678c-126">Dal controllo Browser Link barra degli strumenti è possibile:</span><span class="sxs-lookup"><span data-stu-id="e678c-126">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="e678c-127">Aggiornare l'app Web in più browser contemporaneamente.</span><span class="sxs-lookup"><span data-stu-id="e678c-127">Refresh the web app in several browsers at once.</span></span>
* <span data-ttu-id="e678c-128">Aprire il **Dashboard browser link** .</span><span class="sxs-lookup"><span data-stu-id="e678c-128">Open the **Browser Link Dashboard** .</span></span>
* <span data-ttu-id="e678c-129">Abilitare o disabilitare **browser link** .</span><span class="sxs-lookup"><span data-stu-id="e678c-129">Enable or disable **Browser Link** .</span></span> <span data-ttu-id="e678c-130">Nota: per impostazione predefinita, Browser Link è disabilitato in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e678c-130">Note: Browser Link is disabled by default in Visual Studio.</span></span>
* <span data-ttu-id="e678c-131">Abilitare o disabilitare la [sincronizzazione automatica CSS](#enable-or-disable-css-auto-sync).</span><span class="sxs-lookup"><span data-stu-id="e678c-131">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="e678c-132">Aggiornare l'app Web in più browser contemporaneamente</span><span class="sxs-lookup"><span data-stu-id="e678c-132">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="e678c-133">Per scegliere un singolo Web browser da avviare all'avvio del progetto, usare il menu a discesa nel controllo della barra degli strumenti della **destinazione di debug** :</span><span class="sxs-lookup"><span data-stu-id="e678c-133">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![Menu a discesa F5](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="e678c-135">Per aprire più browser contemporaneamente, scegliere **Sfoglia con...** dallo stesso elenco a discesa.</span><span class="sxs-lookup"><span data-stu-id="e678c-135">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="e678c-136">Tenere premuto il tasto <kbd>CTRL</kbd> per selezionare i browser desiderati, quindi fare clic su **Sfoglia** :</span><span class="sxs-lookup"><span data-stu-id="e678c-136">Hold down the <kbd>Ctrl</kbd> key to select the browsers you want, and then click **Browse** :</span></span>

![Apri molti browser contemporaneamente](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="e678c-138">La schermata seguente mostra Visual Studio con la visualizzazione indice aperta e due browser aperti:</span><span class="sxs-lookup"><span data-stu-id="e678c-138">The following screenshot shows Visual Studio with the Index view open and two open browsers:</span></span>

![Esempio di sincronizzazione con due browser](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="e678c-140">Passare il puntatore del mouse sul controllo della barra degli strumenti Browser Link per visualizzare i browser connessi al progetto:</span><span class="sxs-lookup"><span data-stu-id="e678c-140">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Suggerimento per il passaggio del mouse](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="e678c-142">Modificare la visualizzazione dell'indice e tutti i browser connessi vengono aggiornati quando si fa clic sul pulsante Browser Link Refresh:</span><span class="sxs-lookup"><span data-stu-id="e678c-142">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![browser-Sincronizza modifiche](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="e678c-144">Browser Link funziona anche con i browser che si avviano dall'esterno di Visual Studio e si passa all'URL dell'app.</span><span class="sxs-lookup"><span data-stu-id="e678c-144">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the app URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="e678c-145">Dashboard Browser Link</span><span class="sxs-lookup"><span data-stu-id="e678c-145">The Browser Link Dashboard</span></span>

<span data-ttu-id="e678c-146">Aprire la finestra **Dashboard browser link** dal menu a discesa browser link per gestire la connessione con i browser aperti:</span><span class="sxs-lookup"><span data-stu-id="e678c-146">Open the **Browser Link Dashboard** window from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![Apri-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="e678c-148">Se non è connesso alcun browser, è possibile avviare una sessione non di debug selezionando la **vista nel** collegamento del browser:</span><span class="sxs-lookup"><span data-stu-id="e678c-148">If no browser is connected, you can start a non-debugging session by selecting the **View in Browser** link:</span></span>

![browserlink-dashboard-no-Connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="e678c-150">In caso contrario, i browser connessi vengono visualizzati con il percorso della pagina visualizzata da ogni browser:</span><span class="sxs-lookup"><span data-stu-id="e678c-150">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-dashboard-due connessioni](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="e678c-152">È anche possibile fare clic su un singolo nome del browser per aggiornare solo quel browser.</span><span class="sxs-lookup"><span data-stu-id="e678c-152">You can also click on an individual browser name to refresh only that browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="e678c-153">Abilitare o disabilitare Browser Link</span><span class="sxs-lookup"><span data-stu-id="e678c-153">Enable or disable Browser Link</span></span>

<span data-ttu-id="e678c-154">Quando si riabilita Browser Link dopo averla disabilitata, è necessario aggiornare i browser per riconnetterli.</span><span class="sxs-lookup"><span data-stu-id="e678c-154">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="e678c-155">Abilitare o disabilitare la sincronizzazione automatica CSS</span><span class="sxs-lookup"><span data-stu-id="e678c-155">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="e678c-156">Quando è abilitata la sincronizzazione automatica CSS, i browser connessi vengono aggiornati automaticamente quando si apportano modifiche ai file CSS.</span><span class="sxs-lookup"><span data-stu-id="e678c-156">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="e678c-157">Funzionamento</span><span class="sxs-lookup"><span data-stu-id="e678c-157">How it works</span></span>

<span data-ttu-id="e678c-158">Browser Link USA [:::no-loc(SignalR):::](xref:signalr/introduction) per creare un canale di comunicazione tra Visual Studio e il browser.</span><span class="sxs-lookup"><span data-stu-id="e678c-158">Browser Link uses [:::no-loc(SignalR):::](xref:signalr/introduction) to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="e678c-159">Quando Browser Link è abilitato, Visual Studio funge da :::no-loc(SignalR)::: server a cui possono connettersi più client (browser).</span><span class="sxs-lookup"><span data-stu-id="e678c-159">When Browser Link is enabled, Visual Studio acts as a :::no-loc(SignalR)::: server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="e678c-160">Browser Link registra anche un componente middleware nella pipeline delle richieste di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e678c-160">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="e678c-161">Questo componente inserisce riferimenti speciali `<script>` in ogni richiesta di pagina dal server.</span><span class="sxs-lookup"><span data-stu-id="e678c-161">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="e678c-162">È possibile visualizzare i riferimenti agli script selezionando **Visualizza origine** nel browser e scorrendo fino alla fine del `<body>` contenuto del Tag:</span><span class="sxs-lookup"><span data-stu-id="e678c-162">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="e678c-163">I file di origine non sono stati modificati.</span><span class="sxs-lookup"><span data-stu-id="e678c-163">Your source files aren't modified.</span></span> <span data-ttu-id="e678c-164">Il componente middleware inserisce i riferimenti allo script in modo dinamico.</span><span class="sxs-lookup"><span data-stu-id="e678c-164">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="e678c-165">Poiché il codice sul lato browser è tutto JavaScript, funziona su tutti i browser che :::no-loc(SignalR)::: supportano senza richiedere un plug-in del browser.</span><span class="sxs-lookup"><span data-stu-id="e678c-165">Because the browser-side code is all JavaScript, it works on all browsers that :::no-loc(SignalR)::: supports without requiring a browser plug-in.</span></span>
