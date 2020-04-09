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
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="1dfbb-103">Collegamento al browser in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1dfbb-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="1dfbb-104">Di [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="1dfbb-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="1dfbb-105">Collegamento browser è una funzionalità di Visual Studio.Browser Link is a Visual Studio feature.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-105">Browser Link is a Visual Studio feature.</span></span> <span data-ttu-id="1dfbb-106">Crea un canale di comunicazione tra l'ambiente di sviluppo e uno o più browser web.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-106">It creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="1dfbb-107">È possibile utilizzare Browser Link per aggiornare l'app Web in più browser contemporaneamente, il che è utile per il test tra browser.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-107">You can use Browser Link to refresh your web app in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="1dfbb-108">Impostazione del collegamento del browser</span><span class="sxs-lookup"><span data-stu-id="1dfbb-108">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1dfbb-109">Aggiungere il pacchetto [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) al progetto.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-109">Add the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package to your project.</span></span> <span data-ttu-id="1dfbb-110">Per ASP.NET Core Razor Pages o progetti MVC, abilitare anche la compilazione in runtime dei file Razor (*.cshtml*) come descritto in <xref:mvc/views/view-compilation>.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-110">For ASP.NET Core Razor Pages or MVC projects, also enable runtime compilation of Razor (*.cshtml*) files as described in <xref:mvc/views/view-compilation>.</span></span> <span data-ttu-id="1dfbb-111">Le modifiche della sintassi Razor vengono applicate solo quando è stata abilitata la compilazione di runtime.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-111">Razor syntax changes are applied only when runtime compilation has been enabled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="1dfbb-112">Quando si converte un progetto ASP.NET Core 2.0 in ASP.NET Core 2.1 e si passa al [metadocumento Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), installare il pacchetto [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) per la funzionalità di collegamento del browser.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-112">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for Browser Link functionality.</span></span> <span data-ttu-id="1dfbb-113">I modelli di progetto di `Microsoft.AspNetCore.App` ASP.NET Core 2.1 usano il metapacchetto per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-113">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="1dfbb-114">I modelli di progetto **Applicazione Web**2.0 , **Vuoto**e Applicazione **API Web** ASP.NET utilizzano il [metapacchetto Microsoft.AspNetCore.All](xref:fundamentals/metapackage), che contiene un riferimento al pacchetto per [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span><span class="sxs-lookup"><span data-stu-id="1dfbb-114">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="1dfbb-115">Pertanto, `Microsoft.AspNetCore.All` l'utilizzo del metapacchetto non richiede ulteriori azioni per rendere Browser Link disponibile per l'uso.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-115">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="1dfbb-116">Il modello di progetto di applicazione **Web** ASP.NET Core 1.x include un riferimento al pacchetto per il pacchetto [Microsoft.VisualStudio.Web.BrowserLink.](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)</span><span class="sxs-lookup"><span data-stu-id="1dfbb-116">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="1dfbb-117">Altri tipi di progetto richiedono l'aggiunta di un riferimento al pacchetto a `Microsoft.VisualStudio.Web.BrowserLink`.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-117">Other project types require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="1dfbb-118">Configurazione</span><span class="sxs-lookup"><span data-stu-id="1dfbb-118">Configuration</span></span>

<span data-ttu-id="1dfbb-119">Chiamare `UseBrowserLink` nel metodo `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="1dfbb-119">Call `UseBrowserLink` in the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="1dfbb-120">La `UseBrowserLink` chiamata viene in `if` genere inserita all'interno di un blocco che abilita solo browser link nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-120">The `UseBrowserLink` call is typically placed inside an `if` block that only enables Browser Link in the Development environment.</span></span> <span data-ttu-id="1dfbb-121">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="1dfbb-121">For example:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="1dfbb-122">Per altre informazioni, vedere <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-122">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="1dfbb-123">Come utilizzare Browser Link</span><span class="sxs-lookup"><span data-stu-id="1dfbb-123">How to use Browser Link</span></span>

<span data-ttu-id="1dfbb-124">Quando è aperto un progetto ASP.NET Core, Visual Studio mostra il controllo della barra degli strumenti Collegamento browser accanto al controllo della barra degli strumenti **Debug destinazione:When** you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the Debug Target toolbar control:</span><span class="sxs-lookup"><span data-stu-id="1dfbb-124">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Menu a discesa Collegamento browser](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="1dfbb-126">Dal controllo della barra degli strumenti Browser Link, è possibile:</span><span class="sxs-lookup"><span data-stu-id="1dfbb-126">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="1dfbb-127">Aggiorna l'app Web in più browser contemporaneamente.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-127">Refresh the web app in several browsers at once.</span></span>
* <span data-ttu-id="1dfbb-128">Aprire il **dashboard di collegamento del browser**.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-128">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="1dfbb-129">Attivare o disattivare **Browser Link**.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-129">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="1dfbb-130">Nota: collegamento browser è disabilitato per impostazione predefinita in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-130">Note: Browser Link is disabled by default in Visual Studio.</span></span>
* <span data-ttu-id="1dfbb-131">Attivare o disattivare [la sincronizzazione automatica CSS](#enable-or-disable-css-auto-sync).</span><span class="sxs-lookup"><span data-stu-id="1dfbb-131">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="1dfbb-132">Aggiornare l'app Web in più browser contemporaneamente</span><span class="sxs-lookup"><span data-stu-id="1dfbb-132">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="1dfbb-133">Per scegliere un singolo Web browser da avviare all'avvio del progetto, utilizzare il menu a discesa nel controllo della barra degli strumenti **Debug destinazione:**</span><span class="sxs-lookup"><span data-stu-id="1dfbb-133">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![Menu a discesa F5](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="1dfbb-135">Per aprire più browser contemporaneamente, scegliere **Sfoglia con...** dallo stesso menu a discesa.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-135">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="1dfbb-136">Tenere premuto il <kbd>tasto Ctrl</kbd> per selezionare i browser desiderati e quindi fare clic su **Sfoglia**:</span><span class="sxs-lookup"><span data-stu-id="1dfbb-136">Hold down the <kbd>Ctrl</kbd> key to select the browsers you want, and then click **Browse**:</span></span>

![Aprire molti browser contemporaneamente](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="1dfbb-138">La schermata seguente mostra Visual Studio con la visualizzazione Indice aperta e due browser aperti:The following screenshot shows Visual Studio with the Index view open and two open browsers:</span><span class="sxs-lookup"><span data-stu-id="1dfbb-138">The following screenshot shows Visual Studio with the Index view open and two open browsers:</span></span>

![Esempio di sincronizzazione con due browser](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="1dfbb-140">Passare il puntatore del mouse sul controllo della barra degli strumenti Browser Link per visualizzare i browser connessi al progetto:</span><span class="sxs-lookup"><span data-stu-id="1dfbb-140">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Suggerimento al passaggio del mouse](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="1dfbb-142">Modificare la visualizzazione Indice e tutti i browser connessi vengono aggiornati quando si fa clic sul pulsante Aggiorna collegamento browser:</span><span class="sxs-lookup"><span data-stu-id="1dfbb-142">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![browser-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="1dfbb-144">Browser Link funziona anche con i browser avviati dall'esterno di Visual Studio e passare all'URL dell'app.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-144">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the app URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="1dfbb-145">Dashboard di collegamento del browser</span><span class="sxs-lookup"><span data-stu-id="1dfbb-145">The Browser Link Dashboard</span></span>

<span data-ttu-id="1dfbb-146">Aprire la finestra **Browser Link Dashboard** dal menu a discesa Browser Link per gestire la connessione con i browser aperti:</span><span class="sxs-lookup"><span data-stu-id="1dfbb-146">Open the **Browser Link Dashboard** window from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![open-browserlink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="1dfbb-148">Se non è connesso alcun browser, è possibile avviare una sessione di non debug selezionando il collegamento **Visualizza nel browser:**</span><span class="sxs-lookup"><span data-stu-id="1dfbb-148">If no browser is connected, you can start a non-debugging session by selecting the **View in Browser** link:</span></span>

![browserlink-dashboard-no-connessioni](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="1dfbb-150">In caso contrario, i browser connessi vengono visualizzati con il percorso della pagina visualizzata da ogni browser:</span><span class="sxs-lookup"><span data-stu-id="1dfbb-150">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-dashboard-due-connessioni](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="1dfbb-152">È inoltre possibile fare clic sul nome di un singolo browser per aggiornare solo tale browser.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-152">You can also click on an individual browser name to refresh only that browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="1dfbb-153">Attivare o disattivare Browser Link</span><span class="sxs-lookup"><span data-stu-id="1dfbb-153">Enable or disable Browser Link</span></span>

<span data-ttu-id="1dfbb-154">Quando si riattiva Browser Link dopo averlo disattivato, è necessario aggiornare i browser per riconnetterli.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-154">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="1dfbb-155">Attivare o disattivare la sincronizzazione automatica CSS</span><span class="sxs-lookup"><span data-stu-id="1dfbb-155">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="1dfbb-156">Quando la sincronizzazione automatica CSS è abilitata, i browser connessi vengono aggiornati automaticamente quando si apporta noto ai file CSS.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-156">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="1dfbb-157">Funzionamento</span><span class="sxs-lookup"><span data-stu-id="1dfbb-157">How it works</span></span>

<span data-ttu-id="1dfbb-158">Browser Link [SignalR](xref:signalr/introduction) utilizza per creare un canale di comunicazione tra Visual Studio e il browser.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-158">Browser Link uses [SignalR](xref:signalr/introduction) to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="1dfbb-159">Quando browser Link è abilitato, SignalR Visual Studio funge da server a cui possono connettersi più client (browser).</span><span class="sxs-lookup"><span data-stu-id="1dfbb-159">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="1dfbb-160">Browser Link registra anche un componente middleware nella pipeline di richiesta ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-160">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="1dfbb-161">Questo componente inserisce riferimenti speciali `<script>` in ogni richiesta di pagina dal server.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-161">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="1dfbb-162">È possibile visualizzare i riferimenti agli script selezionando **Visualizza origine** nel `<body>` browser e scorrendo fino alla fine del contenuto del tag:</span><span class="sxs-lookup"><span data-stu-id="1dfbb-162">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="1dfbb-163">I file di origine non vengono modificati.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-163">Your source files aren't modified.</span></span> <span data-ttu-id="1dfbb-164">Il componente middleware inserisce i riferimenti dello script in modo dinamico.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-164">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="1dfbb-165">Poiché il codice lato browser è tutto JavaScript, funziona su tutti i browser che SignalR supportano senza richiedere un plug-in del browser.</span><span class="sxs-lookup"><span data-stu-id="1dfbb-165">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
