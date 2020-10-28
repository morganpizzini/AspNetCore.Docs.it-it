---
title: Usare i servizi JavaScript per creare applicazioni a pagina singola in ASP.NET Core
author: scottaddie
description: Informazioni sui vantaggi derivanti dall'uso dei servizi JavaScript per creare un'applicazione a pagina singola (SPA) supportata da ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017, devx-track-js
ms.date: 09/06/2019
no-loc:
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
uid: client-side/spa-services
ms.openlocfilehash: 33ae16c033142aa1c0f4cea53ca746e8a6aaf052
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690656"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="2727c-103">Usare i servizi JavaScript per creare applicazioni a pagina singola in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2727c-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="2727c-104">Di [Scott Addie](https://github.com/scottaddie) e [Fiyaz Hasan](https://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="2727c-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="2727c-105">Un'applicazione a pagina singola (SPA) è un tipo comune di applicazione Web grazie alla relativa esperienza utente avanzata.</span><span class="sxs-lookup"><span data-stu-id="2727c-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="2727c-106">L'integrazione di Framework o librerie di SPA lato client, ad esempio [angolare](https://angular.io/) o [React](https://facebook.github.io/react/), con framework lato server come ASP.NET Core può essere difficile.</span><span class="sxs-lookup"><span data-stu-id="2727c-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="2727c-107">Servizi JavaScript è stato sviluppato per ridurre l'attrito nel processo di integrazione.</span><span class="sxs-lookup"><span data-stu-id="2727c-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="2727c-108">Consente un funzionamento uniforme tra i diversi stack di tecnologie client e server.</span><span class="sxs-lookup"><span data-stu-id="2727c-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="2727c-109">Le funzionalità descritte in questo articolo sono obsolete a partire da ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="2727c-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="2727c-110">Un meccanismo di integrazione di Framework SPA più semplice è disponibile nel pacchetto NuGet [Microsoft. AspNetCore. SpaServices. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) .</span><span class="sxs-lookup"><span data-stu-id="2727c-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="2727c-111">Per ulteriori informazioni, vedere [[annuncio] Obsoleting Microsoft. AspNetCore. SpaServices e Microsoft. AspNetCore. NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span><span class="sxs-lookup"><span data-stu-id="2727c-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="2727c-112">Informazioni sui servizi JavaScript</span><span class="sxs-lookup"><span data-stu-id="2727c-112">What is JavaScript Services</span></span>

<span data-ttu-id="2727c-113">JavaScript Services è una raccolta di tecnologie lato client per ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2727c-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="2727c-114">Il suo obiettivo è quello di posizionare ASP.NET Core come la piattaforma lato server preferita dagli sviluppatori per la creazione di Spa.</span><span class="sxs-lookup"><span data-stu-id="2727c-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="2727c-115">Servizi JavaScript è costituito da due pacchetti NuGet distinti:</span><span class="sxs-lookup"><span data-stu-id="2727c-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="2727c-116">[Microsoft. AspNetCore. NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="2727c-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="2727c-117">[Microsoft. AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="2727c-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="2727c-118">Questi pacchetti sono utili negli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="2727c-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="2727c-119">Esegui JavaScript sul server</span><span class="sxs-lookup"><span data-stu-id="2727c-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="2727c-120">Usare un Framework o una libreria SPA</span><span class="sxs-lookup"><span data-stu-id="2727c-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="2727c-121">Crea asset lato client con Webpack</span><span class="sxs-lookup"><span data-stu-id="2727c-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="2727c-122">Gran parte di questo articolo è dedicata all'uso del pacchetto SpaServices.</span><span class="sxs-lookup"><span data-stu-id="2727c-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="2727c-123">Informazioni su SpaServices</span><span class="sxs-lookup"><span data-stu-id="2727c-123">What is SpaServices</span></span>

<span data-ttu-id="2727c-124">SpaServices è stato creato per posizionare ASP.NET Core la piattaforma lato server preferita dagli sviluppatori per la creazione di Spa.</span><span class="sxs-lookup"><span data-stu-id="2727c-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="2727c-125">SpaServices non è necessario per sviluppare Spa con ASP.NET Core e non blocca gli sviluppatori in un particolare framework client.</span><span class="sxs-lookup"><span data-stu-id="2727c-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="2727c-126">SpaServices fornisce un'infrastruttura utile, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="2727c-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="2727c-127">Prerendering lato server</span><span class="sxs-lookup"><span data-stu-id="2727c-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="2727c-128">Middleware dev per Webpack</span><span class="sxs-lookup"><span data-stu-id="2727c-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="2727c-129">Sostituzione del modulo attivo</span><span class="sxs-lookup"><span data-stu-id="2727c-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="2727c-130">Helper di routing</span><span class="sxs-lookup"><span data-stu-id="2727c-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="2727c-131">Insieme, questi componenti dell'infrastruttura migliorano sia il flusso di lavoro di sviluppo che l'esperienza di Runtime.</span><span class="sxs-lookup"><span data-stu-id="2727c-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="2727c-132">I componenti possono essere adottati singolarmente.</span><span class="sxs-lookup"><span data-stu-id="2727c-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="2727c-133">Prerequisiti per l'uso di SpaServices</span><span class="sxs-lookup"><span data-stu-id="2727c-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="2727c-134">Per usare SpaServices, installare quanto segue:</span><span class="sxs-lookup"><span data-stu-id="2727c-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="2727c-135">[Node.js](https://nodejs.org/) (versione 6 o successiva) con NPM</span><span class="sxs-lookup"><span data-stu-id="2727c-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="2727c-136">Per verificare che questi componenti siano installati ed è possibile trovarli, eseguire il comando seguente dalla riga di comando:</span><span class="sxs-lookup"><span data-stu-id="2727c-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="2727c-137">Se si esegue la distribuzione in un sito Web di Azure, non è necessaria alcuna azione &mdash;Node.js viene installato e disponibile negli ambienti server.</span><span class="sxs-lookup"><span data-stu-id="2727c-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="2727c-138">In Windows con Visual Studio 2017, l'SDK viene installato selezionando il carico di lavoro **sviluppo multipiattaforma .NET Core** .</span><span class="sxs-lookup"><span data-stu-id="2727c-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="2727c-139">Pacchetto NuGet [Microsoft. AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/)</span><span class="sxs-lookup"><span data-stu-id="2727c-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="2727c-140">Prerendering lato server</span><span class="sxs-lookup"><span data-stu-id="2727c-140">Server-side prerendering</span></span>

<span data-ttu-id="2727c-141">Un'applicazione universale (nota anche come isomorfi) è un'applicazione JavaScript in grado di eseguire sia nel server che nel client.</span><span class="sxs-lookup"><span data-stu-id="2727c-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="2727c-142">Angolari, React e altri Framework diffusi forniscono una piattaforma universale per questo stile di sviluppo di applicazioni.</span><span class="sxs-lookup"><span data-stu-id="2727c-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="2727c-143">L'idea è eseguire prima di tutto il rendering dei componenti del Framework nel server tramite Node.js e quindi delegare un'ulteriore esecuzione al client.</span><span class="sxs-lookup"><span data-stu-id="2727c-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="2727c-144">ASP.NET Core [Helper Tag](xref:mvc/views/tag-helpers/intro) forniti da SpaServices semplificano l'implementazione del prerendering lato server richiamando le funzioni JavaScript sul server.</span><span class="sxs-lookup"><span data-stu-id="2727c-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="2727c-145">Prerequisiti per il rendering lato server</span><span class="sxs-lookup"><span data-stu-id="2727c-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="2727c-146">Installare il pacchetto NPM per l'esecuzione del [prerendering di ASPNET](https://www.npmjs.com/package/aspnet-prerendering) :</span><span class="sxs-lookup"><span data-stu-id="2727c-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="2727c-147">Configurazione del prerendering lato server</span><span class="sxs-lookup"><span data-stu-id="2727c-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="2727c-148">Gli helper tag vengono resi individuabili tramite la registrazione dello spazio dei nomi nel file *_ViewImports. cshtml* del progetto:</span><span class="sxs-lookup"><span data-stu-id="2727c-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="2727c-149">Questi helper Tag astraggono le complessità della comunicazione diretta con le API di basso livello sfruttando una sintassi simile a HTML all'interno della :::no-loc(Razor)::: vista:</span><span class="sxs-lookup"><span data-stu-id="2727c-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the :::no-loc(Razor)::: view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="2727c-150">ASP-PreRender-Helper tag del modulo</span><span class="sxs-lookup"><span data-stu-id="2727c-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="2727c-151">L' `asp-prerender-module` Helper tag, usato nell'esempio di codice precedente, esegue *ClientApp/dist/main-server.js* sul server tramite Node.js.</span><span class="sxs-lookup"><span data-stu-id="2727c-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="2727c-152">Per maggiore chiarezza, *main-server.js* file è un artefatto dell'attività Transpilazione da typescript a JavaScript nel processo di compilazione di [Webpack](https://webpack.github.io/) .</span><span class="sxs-lookup"><span data-stu-id="2727c-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="2727c-153">Webpack definisce un alias del punto di ingresso `main-server` e l'attraversamento del grafico delle dipendenze per questo alias inizia dal file *ClientApp/boot-server. TS* :</span><span class="sxs-lookup"><span data-stu-id="2727c-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="2727c-154">Nell'esempio angolare seguente il file *ClientApp/boot-server. TS* usa la `createServerRenderer` funzione e il `RenderResult` tipo del `aspnet-prerendering` pacchetto NPM per configurare il rendering del server tramite Node.js.</span><span class="sxs-lookup"><span data-stu-id="2727c-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="2727c-155">Il markup HTML destinato al rendering sul lato server viene passato a una chiamata di funzione Resolve, che è racchiusa in un oggetto JavaScript fortemente tipizzato `Promise` .</span><span class="sxs-lookup"><span data-stu-id="2727c-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="2727c-156">Il `Promise` significato dell'oggetto è che fornisce in modo asincrono il markup HTML alla pagina per l'inserimento nell'elemento segnaposto del Dom.</span><span class="sxs-lookup"><span data-stu-id="2727c-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="2727c-157">ASP-PreRender-Helper tag di dati</span><span class="sxs-lookup"><span data-stu-id="2727c-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="2727c-158">Quando è associato all' `asp-prerender-module` Helper tag, `asp-prerender-data` è possibile usare l'helper tag per passare le informazioni contestuali dalla :::no-loc(Razor)::: visualizzazione al codice JavaScript sul lato server.</span><span class="sxs-lookup"><span data-stu-id="2727c-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the :::no-loc(Razor)::: view to the server-side JavaScript.</span></span> <span data-ttu-id="2727c-159">Il markup seguente, ad esempio, passa i dati utente al `main-server` modulo:</span><span class="sxs-lookup"><span data-stu-id="2727c-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="2727c-160">L'argomento received `UserName` viene serializzato usando il serializzatore JSON incorporato e viene archiviato nell' `params.data` oggetto.</span><span class="sxs-lookup"><span data-stu-id="2727c-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="2727c-161">Nell'esempio angolare seguente i dati vengono usati per costruire un messaggio di saluto personalizzato all'interno di un `h1` elemento:</span><span class="sxs-lookup"><span data-stu-id="2727c-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="2727c-162">I nomi delle proprietà passati negli Helper tag sono rappresentati con la notazione **PascalCase** .</span><span class="sxs-lookup"><span data-stu-id="2727c-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="2727c-163">A differenza di JavaScript, in cui gli stessi nomi di proprietà sono rappresentati con **CamelCase** .</span><span class="sxs-lookup"><span data-stu-id="2727c-163">Contrast that to JavaScript, where the same property names are represented with **camelCase** .</span></span> <span data-ttu-id="2727c-164">La configurazione della serializzazione JSON predefinita è responsabile di questa differenza.</span><span class="sxs-lookup"><span data-stu-id="2727c-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="2727c-165">Per espandersi sull'esempio di codice precedente, è possibile passare i dati dal server alla visualizzazione idratando la `globals` proprietà fornita alla `resolve` funzione:</span><span class="sxs-lookup"><span data-stu-id="2727c-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="2727c-166">La `postList` matrice definita all'interno dell' `globals` oggetto è associata all'oggetto globale del browser `window` .</span><span class="sxs-lookup"><span data-stu-id="2727c-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="2727c-167">Questa variabile che solleva l'ambito globale elimina la duplicazione del lavoro, in particolare per quanto riguarda il caricamento degli stessi dati una volta sul server e di nuovo sul client.</span><span class="sxs-lookup"><span data-stu-id="2727c-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![variabile di postlist globale collegata all'oggetto finestra](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="2727c-169">Middleware dev per Webpack</span><span class="sxs-lookup"><span data-stu-id="2727c-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="2727c-170">Il [middleware dev per Webpack](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduce un flusso di lavoro di sviluppo semplificato, in base al quale Webpack crea risorse su richiesta.</span><span class="sxs-lookup"><span data-stu-id="2727c-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="2727c-171">Il middleware compila e serve automaticamente le risorse sul lato client quando una pagina viene ricaricata nel browser.</span><span class="sxs-lookup"><span data-stu-id="2727c-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="2727c-172">L'approccio alternativo consiste nel richiamare manualmente Webpack tramite lo script di compilazione NPM del progetto quando viene modificata una dipendenza di terze parti o il codice personalizzato.</span><span class="sxs-lookup"><span data-stu-id="2727c-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="2727c-173">Nell'esempio seguente viene illustrato uno script di compilazione NPM nella *package.jssu* file:</span><span class="sxs-lookup"><span data-stu-id="2727c-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="2727c-174">Prerequisiti del middleware dev per Webpack</span><span class="sxs-lookup"><span data-stu-id="2727c-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="2727c-175">Installare il pacchetto NPM [ASPNET-Webpack](https://www.npmjs.com/package/aspnet-webpack) :</span><span class="sxs-lookup"><span data-stu-id="2727c-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="2727c-176">Configurazione di Webpack dev middleware</span><span class="sxs-lookup"><span data-stu-id="2727c-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="2727c-177">Il middleware dev per Webpack viene registrato nella pipeline di richieste HTTP tramite il codice seguente nel metodo del file *Startup.cs* `Configure` :</span><span class="sxs-lookup"><span data-stu-id="2727c-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="2727c-178">Il `UseWebpackDevMiddleware` metodo di estensione deve essere chiamato prima di [registrare l'hosting di file statici](xref:fundamentals/static-files) tramite il `UseStaticFiles` metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="2727c-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="2727c-179">Per motivi di sicurezza, registrare il middleware solo quando l'app viene eseguita in modalità di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="2727c-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="2727c-180">La proprietà del file *webpack.config.js* `output.publicPath` indica al middleware di controllare la `dist` cartella per le modifiche:</span><span class="sxs-lookup"><span data-stu-id="2727c-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="2727c-181">Sostituzione del modulo attivo</span><span class="sxs-lookup"><span data-stu-id="2727c-181">Hot Module Replacement</span></span>

<span data-ttu-id="2727c-182">Si pensi alla funzionalità HMR ( [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) ) di Webpack come un'evoluzione del [middleware dev di Webpack](#webpack-dev-middleware).</span><span class="sxs-lookup"><span data-stu-id="2727c-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="2727c-183">HMR introduce tutti gli stessi vantaggi, ma semplifica ulteriormente il flusso di lavoro di sviluppo aggiornando automaticamente il contenuto della pagina dopo la compilazione delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="2727c-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="2727c-184">Non confonderlo con un aggiornamento del browser, che interferisce con lo stato in memoria e la sessione di debug correnti della SPA.</span><span class="sxs-lookup"><span data-stu-id="2727c-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="2727c-185">Esiste un collegamento attivo tra il servizio middleware dev per Webpack e il browser, che significa che le modifiche vengono inserite nel browser.</span><span class="sxs-lookup"><span data-stu-id="2727c-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="2727c-186">Prerequisiti per la sostituzione dei moduli sensibili</span><span class="sxs-lookup"><span data-stu-id="2727c-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="2727c-187">Installare il pacchetto NPM per [Webpack-Hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) :</span><span class="sxs-lookup"><span data-stu-id="2727c-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="2727c-188">Configurazione della sostituzione del modulo attivo</span><span class="sxs-lookup"><span data-stu-id="2727c-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="2727c-189">Il componente HMR deve essere registrato nella pipeline di richieste HTTP di MVC nel `Configure` Metodo:</span><span class="sxs-lookup"><span data-stu-id="2727c-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="2727c-190">Come accadeva con il [middleware dev di Webpack](#webpack-dev-middleware), il `UseWebpackDevMiddleware` metodo di estensione deve essere chiamato prima del `UseStaticFiles` metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="2727c-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="2727c-191">Per motivi di sicurezza, registrare il middleware solo quando l'app viene eseguita in modalità di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="2727c-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="2727c-192">Il file di *webpack.config.js* deve definire una `plugins` matrice, anche se viene lasciato vuoto:</span><span class="sxs-lookup"><span data-stu-id="2727c-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="2727c-193">Dopo aver caricato l'app nel browser, la scheda della console degli strumenti per sviluppatori fornisce la conferma dell'attivazione di HMR:</span><span class="sxs-lookup"><span data-stu-id="2727c-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![Messaggio connesso di sostituzione del modulo attivo](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="2727c-195">Helper di routing</span><span class="sxs-lookup"><span data-stu-id="2727c-195">Routing helpers</span></span>

<span data-ttu-id="2727c-196">Nella maggior parte delle applicazioni Spa basate su ASP.NET Core, il routing lato client spesso è necessario oltre al routing lato server.</span><span class="sxs-lookup"><span data-stu-id="2727c-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="2727c-197">I sistemi di routing SPA e MVC possono funzionare in modo indipendente senza interferenze.</span><span class="sxs-lookup"><span data-stu-id="2727c-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="2727c-198">Esiste, tuttavia, un caso marginale che pone problemi: identificazione delle risposte HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="2727c-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="2727c-199">Si consideri lo scenario in cui viene utilizzata una route con estensione `/some/page` .</span><span class="sxs-lookup"><span data-stu-id="2727c-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="2727c-200">Si supponga che la richiesta non corrisponda a una route sul lato server, ma il modello corrispondente corrisponde a una route sul lato client.</span><span class="sxs-lookup"><span data-stu-id="2727c-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="2727c-201">Si consideri ora una richiesta in ingresso per `/images/user-512.png` , che in genere prevede di trovare un file di immagine nel server.</span><span class="sxs-lookup"><span data-stu-id="2727c-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="2727c-202">Se il percorso della risorsa richiesto non corrisponde ad alcuna route sul lato server o a un file statico, è improbabile che l'applicazione sul lato client la gestisca &mdash; in genere restituendo un codice di stato HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="2727c-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="2727c-203">Prerequisiti per il routing degli helper</span><span class="sxs-lookup"><span data-stu-id="2727c-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="2727c-204">Installare il pacchetto NPM di routing lato client.</span><span class="sxs-lookup"><span data-stu-id="2727c-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="2727c-205">Uso di angolari come esempio:</span><span class="sxs-lookup"><span data-stu-id="2727c-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="2727c-206">Configurazione degli helper di routing</span><span class="sxs-lookup"><span data-stu-id="2727c-206">Routing helpers configuration</span></span>

<span data-ttu-id="2727c-207">Un metodo di estensione denominato `MapSpaFallbackRoute` viene usato nel `Configure` Metodo:</span><span class="sxs-lookup"><span data-stu-id="2727c-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="2727c-208">Le route vengono valutate nell'ordine in cui sono state configurate.</span><span class="sxs-lookup"><span data-stu-id="2727c-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="2727c-209">Di conseguenza, la `default` route nell'esempio di codice precedente viene usata per prima per i criteri di ricerca.</span><span class="sxs-lookup"><span data-stu-id="2727c-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="2727c-210">Creare un nuovo progetto</span><span class="sxs-lookup"><span data-stu-id="2727c-210">Create a new project</span></span>

<span data-ttu-id="2727c-211">I servizi JavaScript forniscono modelli di applicazione preconfigurati.</span><span class="sxs-lookup"><span data-stu-id="2727c-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="2727c-212">SpaServices viene usato in questi modelli insieme a Framework e librerie diversi, ad esempio angolare, React e Redux.</span><span class="sxs-lookup"><span data-stu-id="2727c-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="2727c-213">Questi modelli possono essere installati tramite il interfaccia della riga di comando di .NET Core eseguendo il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="2727c-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="2727c-214">Viene visualizzato un elenco di modelli di SPA disponibili:</span><span class="sxs-lookup"><span data-stu-id="2727c-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="2727c-215">Modelli</span><span class="sxs-lookup"><span data-stu-id="2727c-215">Templates</span></span>                                 | <span data-ttu-id="2727c-216">Nome breve</span><span class="sxs-lookup"><span data-stu-id="2727c-216">Short Name</span></span> | <span data-ttu-id="2727c-217">Linguaggio</span><span class="sxs-lookup"><span data-stu-id="2727c-217">Language</span></span> | <span data-ttu-id="2727c-218">Tag</span><span class="sxs-lookup"><span data-stu-id="2727c-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="2727c-219">ASP.NET Core MVC con angolare</span><span class="sxs-lookup"><span data-stu-id="2727c-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="2727c-220">angular</span><span class="sxs-lookup"><span data-stu-id="2727c-220">angular</span></span>    | <span data-ttu-id="2727c-221">[C#]</span><span class="sxs-lookup"><span data-stu-id="2727c-221">[C#]</span></span>     | <span data-ttu-id="2727c-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="2727c-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="2727c-223">ASP.NET Core MVC con React.js</span><span class="sxs-lookup"><span data-stu-id="2727c-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="2727c-224">react</span><span class="sxs-lookup"><span data-stu-id="2727c-224">react</span></span>      | <span data-ttu-id="2727c-225">[C#]</span><span class="sxs-lookup"><span data-stu-id="2727c-225">[C#]</span></span>     | <span data-ttu-id="2727c-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="2727c-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="2727c-227">ASP.NET Core MVC con React.js e Redux</span><span class="sxs-lookup"><span data-stu-id="2727c-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="2727c-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="2727c-228">reactredux</span></span> | <span data-ttu-id="2727c-229">[C#]</span><span class="sxs-lookup"><span data-stu-id="2727c-229">[C#]</span></span>     | <span data-ttu-id="2727c-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="2727c-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="2727c-231">Per creare un nuovo progetto usando uno dei modelli di SPA, includere il **nome breve** del modello nel comando [DotNet New](/dotnet/core/tools/dotnet-new) .</span><span class="sxs-lookup"><span data-stu-id="2727c-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="2727c-232">Il comando seguente crea un'applicazione angolare con ASP.NET Core MVC configurato per il lato server:</span><span class="sxs-lookup"><span data-stu-id="2727c-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="2727c-233">Impostare la modalità di configurazione del runtime</span><span class="sxs-lookup"><span data-stu-id="2727c-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="2727c-234">Esistono due modalità di configurazione di runtime primarie:</span><span class="sxs-lookup"><span data-stu-id="2727c-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="2727c-235">**Sviluppo** :</span><span class="sxs-lookup"><span data-stu-id="2727c-235">**Development** :</span></span>
  * <span data-ttu-id="2727c-236">Include i mapping di origine per semplificare il debug.</span><span class="sxs-lookup"><span data-stu-id="2727c-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="2727c-237">Non ottimizza il codice lato client per le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="2727c-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="2727c-238">**Produzione** :</span><span class="sxs-lookup"><span data-stu-id="2727c-238">**Production** :</span></span>
  * <span data-ttu-id="2727c-239">Esclude i mapping di origine.</span><span class="sxs-lookup"><span data-stu-id="2727c-239">Excludes source maps.</span></span>
  * <span data-ttu-id="2727c-240">Ottimizza il codice lato client tramite la creazione di bundle e minification.</span><span class="sxs-lookup"><span data-stu-id="2727c-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="2727c-241">ASP.NET Core usa una variabile di ambiente denominata `ASPNETCORE_ENVIRONMENT` per archiviare la modalità di configurazione.</span><span class="sxs-lookup"><span data-stu-id="2727c-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="2727c-242">Per ulteriori informazioni, vedere [impostazione dell'ambiente](xref:fundamentals/environments#set-the-environment).</span><span class="sxs-lookup"><span data-stu-id="2727c-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="2727c-243">Esegui con interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="2727c-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="2727c-244">Ripristinare i pacchetti NuGet e NPM richiesti eseguendo il comando seguente nella radice del progetto:</span><span class="sxs-lookup"><span data-stu-id="2727c-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="2727c-245">Compilare ed eseguire l'applicazione:</span><span class="sxs-lookup"><span data-stu-id="2727c-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="2727c-246">L'applicazione viene avviata in localhost in base alla [modalità di configurazione del runtime](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="2727c-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="2727c-247">Se si passa a `http://localhost:5000` nel browser, viene visualizzata la pagina di destinazione.</span><span class="sxs-lookup"><span data-stu-id="2727c-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="2727c-248">Eseguire con Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="2727c-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="2727c-249">Aprire il file con *estensione csproj* generato dal comando [DotNet New](/dotnet/core/tools/dotnet-new) .</span><span class="sxs-lookup"><span data-stu-id="2727c-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="2727c-250">I pacchetti NuGet e NPM richiesti vengono ripristinati automaticamente al momento dell'apertura del progetto.</span><span class="sxs-lookup"><span data-stu-id="2727c-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="2727c-251">Questo processo di ripristino può richiedere alcuni minuti e l'applicazione è pronta per essere eseguita al completamento.</span><span class="sxs-lookup"><span data-stu-id="2727c-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="2727c-252">Fare clic sul pulsante di esecuzione verde o premere `Ctrl + F5` e il browser si apre alla pagina di destinazione dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="2727c-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="2727c-253">L'applicazione viene eseguita in localhost in base alla [modalità di configurazione del runtime](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="2727c-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="2727c-254">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="2727c-254">Test the app</span></span>

<span data-ttu-id="2727c-255">I modelli SpaServices sono preconfigurati per eseguire test sul lato client usando [Karma](https://karma-runner.github.io/1.0/index.html) e [Jasmine](https://jasmine.github.io/).</span><span class="sxs-lookup"><span data-stu-id="2727c-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="2727c-256">Jasmine è un Framework di testing unità popolare per JavaScript, mentre Karma è un test runner per i test.</span><span class="sxs-lookup"><span data-stu-id="2727c-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="2727c-257">Karma è configurato per funzionare con il [middleware dev di Webpack](#webpack-dev-middleware) in modo che lo sviluppatore non debba arrestare ed eseguire il test ogni volta che vengono apportate modifiche.</span><span class="sxs-lookup"><span data-stu-id="2727c-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="2727c-258">Indipendentemente dal fatto che il codice sia in esecuzione sul test case o sul test case stesso, il test viene eseguito automaticamente.</span><span class="sxs-lookup"><span data-stu-id="2727c-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="2727c-259">Usando l'applicazione angolare come esempio, due test case Jasmine sono già disponibili per `CounterComponent` nel file *Counter. Component. spec. TS* :</span><span class="sxs-lookup"><span data-stu-id="2727c-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="2727c-260">Aprire il prompt dei comandi nella directory *ClientApp*</span><span class="sxs-lookup"><span data-stu-id="2727c-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="2727c-261">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="2727c-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="2727c-262">Lo script avvia Karma Test Runner, che legge le impostazioni definite nel file di *karma.conf.js* .</span><span class="sxs-lookup"><span data-stu-id="2727c-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="2727c-263">Tra le altre impostazioni, il *karma.conf.js* identifica i file di test da eseguire tramite la relativa `files` matrice:</span><span class="sxs-lookup"><span data-stu-id="2727c-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="2727c-264">Pubblicare l'app</span><span class="sxs-lookup"><span data-stu-id="2727c-264">Publish the app</span></span>

<span data-ttu-id="2727c-265">Per altre informazioni sulla pubblicazione in Azure, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/12474) .</span><span class="sxs-lookup"><span data-stu-id="2727c-265">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span></span>

<span data-ttu-id="2727c-266">La combinazione degli asset lato client generati e degli elementi ASP.NET Core pubblicati in un pacchetto pronto per la distribuzione può essere complessa.</span><span class="sxs-lookup"><span data-stu-id="2727c-266">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="2727c-267">Fortunatamente, SpaServices Orchestra l'intero processo di pubblicazione con una destinazione MSBuild personalizzata denominata `RunWebpack` :</span><span class="sxs-lookup"><span data-stu-id="2727c-267">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="2727c-268">La destinazione MSBuild ha le responsabilità seguenti:</span><span class="sxs-lookup"><span data-stu-id="2727c-268">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="2727c-269">Ripristinare i pacchetti NPM.</span><span class="sxs-lookup"><span data-stu-id="2727c-269">Restore the npm packages.</span></span>
1. <span data-ttu-id="2727c-270">Creare una build di livello produzione degli asset lato client di terze parti.</span><span class="sxs-lookup"><span data-stu-id="2727c-270">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="2727c-271">Creare una compilazione di livello di produzione degli asset personalizzati sul lato client.</span><span class="sxs-lookup"><span data-stu-id="2727c-271">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="2727c-272">Copiare gli asset generati da Webpack nella cartella di pubblicazione.</span><span class="sxs-lookup"><span data-stu-id="2727c-272">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="2727c-273">La destinazione MSBuild viene richiamata durante l'esecuzione di:</span><span class="sxs-lookup"><span data-stu-id="2727c-273">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="2727c-274">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2727c-274">Additional resources</span></span>

* [<span data-ttu-id="2727c-275">Documenti angolari</span><span class="sxs-lookup"><span data-stu-id="2727c-275">Angular Docs</span></span>](https://angular.io/docs)
