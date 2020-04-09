---
title: Utilizzare i servizi JavaScript per creare applicazioni a pagina singola in ASP.NET Core
author: scottaddie
description: Scopri i vantaggi dell'uso di JavaScript Services per creare un'applicazione a pagina singola (SPA) supportata da ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
uid: client-side/spa-services
ms.openlocfilehash: c0c73882afd579510ad9cdf5b485c1d6fbeadd1c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663779"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="a3eb1-103">Utilizzare i servizi JavaScript per creare applicazioni a pagina singola in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a3eb1-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="a3eb1-104">Di [Scott Addie](https://github.com/scottaddie) e [Fiyaz Hasan](https://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="a3eb1-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="a3eb1-105">Un'applicazione a pagina singola (SPA) è un tipo popolare di applicazione web a causa della sua esperienza utente ricca e intrinseca.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="a3eb1-106">L'integrazione di framework o librerie SPA lato client, ad esempio [Angular](https://angular.io/) o [React](https://facebook.github.io/react/), con framework sul lato server, ad esempio ASP.NET Core, può essere difficile.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="a3eb1-107">JavaScript Services è stato sviluppato per ridurre l'attrito nel processo di integrazione.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="a3eb1-108">Consente un funzionamento senza problemi tra i diversi stack di tecnologia client e server.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="a3eb1-109">Le funzionalità descritte in questo articolo sono obsolete a partire da ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="a3eb1-110">Un meccanismo di integrazione dei framework SPA più semplice è disponibile nel pacchetto [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="a3eb1-111">Per ulteriori informazioni, vedere [[Annuncio] Obsoleto Microsoft.AspNetCore.SpaServices e Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span><span class="sxs-lookup"><span data-stu-id="a3eb1-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="a3eb1-112">Che cos'è servizi JavaScript</span><span class="sxs-lookup"><span data-stu-id="a3eb1-112">What is JavaScript Services</span></span>

<span data-ttu-id="a3eb1-113">JavaScript Services è una raccolta di tecnologie lato client per ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="a3eb1-114">Il suo obiettivo è quello di posizionare ASP.NET Core come piattaforma lato server preferito dagli sviluppatori per la creazione di GIR.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="a3eb1-115">JavaScript Services è costituito da due pacchetti NuGet distinti:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="a3eb1-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="a3eb1-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="a3eb1-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="a3eb1-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="a3eb1-118">Questi pacchetti sono utili negli scenari seguenti:These packages are useful in the following scenarios:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="a3eb1-119">Eseguire JavaScript sul server</span><span class="sxs-lookup"><span data-stu-id="a3eb1-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="a3eb1-120">Utilizzare un framework o una libreria SPA</span><span class="sxs-lookup"><span data-stu-id="a3eb1-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="a3eb1-121">Crea risorse lato client con Webpack</span><span class="sxs-lookup"><span data-stu-id="a3eb1-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="a3eb1-122">Gran parte dell'attenzione in questo articolo è posta sull'utilizzo del pacchetto SpaServices.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="a3eb1-123">Che cos'è SpaServices</span><span class="sxs-lookup"><span data-stu-id="a3eb1-123">What is SpaServices</span></span>

<span data-ttu-id="a3eb1-124">SpaServices è stato creato per posizionare ASP.NET Core come piattaforma lato server preferito dagli sviluppatori per la creazione di SPA.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="a3eb1-125">SpaServices non è necessario per sviluppare SPAAs con ASP.NET Core e non blocca gli sviluppatori in un framework client specifico.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="a3eb1-126">SpaServices fornisce un'infrastruttura utile come:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="a3eb1-127">Prerendering lato server</span><span class="sxs-lookup"><span data-stu-id="a3eb1-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="a3eb1-128">Middleware per sviluppatori di webpack</span><span class="sxs-lookup"><span data-stu-id="a3eb1-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="a3eb1-129">Sostituzione modulo caldo</span><span class="sxs-lookup"><span data-stu-id="a3eb1-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="a3eb1-130">Helper di routing</span><span class="sxs-lookup"><span data-stu-id="a3eb1-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="a3eb1-131">Collettivamente, questi componenti dell'infrastruttura migliorano sia il flusso di lavoro di sviluppo che l'esperienza di runtime.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="a3eb1-132">I componenti possono essere adottati singolarmente.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="a3eb1-133">Prerequisiti per l'utilizzo di SpaServicesPrerequisites for using SpaServices</span><span class="sxs-lookup"><span data-stu-id="a3eb1-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="a3eb1-134">Per utilizzare SpaServices, installare quanto segue:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="a3eb1-135">[Node.js](https://nodejs.org/) (versione 6 o successiva) con npm</span><span class="sxs-lookup"><span data-stu-id="a3eb1-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="a3eb1-136">Per verificare che questi componenti siano installati e reperibili, eseguire quanto segue dalla riga di comando:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="a3eb1-137">Se si esegue la distribuzione in un&mdash;sito Web di Azure, non è richiesta alcuna azione Node.js che è installata e disponibile negli ambienti server.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="a3eb1-138">In Windows con Visual Studio 2017, l'SDK viene installato selezionando il carico di lavoro di **sviluppo multipiattaforma .NET Core.On** Windows using Visual Studio 2017, the SDK is installed by selecting the .NET Core cross-platform development workload.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="a3eb1-139">[Pacchetto Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet</span><span class="sxs-lookup"><span data-stu-id="a3eb1-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="a3eb1-140">Prerendering lato server</span><span class="sxs-lookup"><span data-stu-id="a3eb1-140">Server-side prerendering</span></span>

<span data-ttu-id="a3eb1-141">Un'applicazione universale (nota anche come isomorfica) è un'applicazione JavaScript in grado di essere eseguita sia sul server che sul client.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="a3eb1-142">Angolare, React e altri framework popolari forniscono una piattaforma universale per questo stile di sviluppo dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="a3eb1-143">L'idea è prima di eseguire il rendering dei componenti del framework sul server tramite Node.js e quindi delegare ulteriormente l'esecuzione al client.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="a3eb1-144">ASP.NET [gli helper](xref:mvc/views/tag-helpers/intro) Core Tag forniti da SpaServices semplificano l'implementazione del prerendering sul lato server richiamando le funzioni JavaScript sul server.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="a3eb1-145">Prerequisiti di prerendering sul lato serverServer-side prerendering prerequisites</span><span class="sxs-lookup"><span data-stu-id="a3eb1-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="a3eb1-146">Installare il pacchetto [npm di aspnet-prerendering:](https://www.npmjs.com/package/aspnet-prerendering)</span><span class="sxs-lookup"><span data-stu-id="a3eb1-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="a3eb1-147">Configurazione del prerendering sul lato server</span><span class="sxs-lookup"><span data-stu-id="a3eb1-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="a3eb1-148">Gli helper tag vengono resi individuabili tramite la registrazione dello spazio dei nomi nel file _ViewImports.cshtml del progetto:The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="a3eb1-149">Questi tag helper astraggono la complessità della comunicazione diretta con le API di basso livello sfruttando una sintassi simile a HTML all'interno della visualizzazione Razor:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="a3eb1-150">asp-prerender-module Tag Helper</span><span class="sxs-lookup"><span data-stu-id="a3eb1-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="a3eb1-151">Tag `asp-prerender-module` Helper, utilizzato nell'esempio di codice precedente, esegue *ClientApp/dist/main-server.js* sul server tramite Node.js.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="a3eb1-152">Per motivi di chiarezza, il file *main-server.js* è un elemento dell'attività di transpilazione da TypeScript a JavaScript nel processo di compilazione [Webpack.](https://webpack.github.io/)</span><span class="sxs-lookup"><span data-stu-id="a3eb1-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="a3eb1-153">Webpack definisce un alias `main-server`del punto di ingresso di ; e, l'attraversamento del grafico delle dipendenze per questo alias inizia dal file *ClientApp/boot-server.ts:*</span><span class="sxs-lookup"><span data-stu-id="a3eb1-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="a3eb1-154">Nell'esempio angolare seguente, il file *ClientApp/boot-server.ts* utilizza la `createServerRenderer` funzione e `RenderResult` il tipo del pacchetto npm per configurare il `aspnet-prerendering` rendering del server tramite Node.js.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="a3eb1-155">Il markup HTML destinato al rendering sul lato server viene passato a una chiamata `Promise` di funzione resolve, di cui viene eseguito il wrapping in un oggetto JavaScript fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="a3eb1-156">Il `Promise` significato dell'oggetto è che fornisce in modo asincrono il markup HTML alla pagina per l'inserimento nell'elemento segnaposto del DOM.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="a3eb1-157">asp-prerender-data Tag Helper</span><span class="sxs-lookup"><span data-stu-id="a3eb1-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="a3eb1-158">Se associato a `asp-prerender-module` Tag Helper, l'helper `asp-prerender-data` Tag può essere utilizzato per passare informazioni contestuali dalla visualizzazione Razor al JavaScript lato server.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="a3eb1-159">Ad esempio, il markup seguente `main-server` passa i dati utente al modulo:For example, the following markup passes user data to the module:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="a3eb1-160">L'argomento received `UserName` viene serializzato utilizzando il serializzatore `params.data` JSON incorporato e viene archiviato nell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="a3eb1-161">Nell'esempio angolare seguente, i dati vengono utilizzati per `h1` creare un messaggio di saluto personalizzato all'interno di un elemento:In the following Angular example, the data is used to construct a personalized greeting within an element:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="a3eb1-162">I nomi delle proprietà passati negli helper Tag sono rappresentati con la notazione **PascalCase.Property** names passed in Tag Helpers are represented with PascalCase notation.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="a3eb1-163">A differenza di JavaScript, in cui gli stessi nomi di proprietà sono rappresentati con **camelCase**.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-163">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="a3eb1-164">La configurazione di serializzazione JSON predefinita è responsabile di questa differenza.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="a3eb1-165">Per espandere l'esempio di codice precedente, i dati possono essere `globals` passati dal `resolve` server alla visualizzazione idratando la proprietà fornita alla funzione:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="a3eb1-166">La `postList` matrice definita `globals` all'interno dell'oggetto è `window` associata all'oggetto globale del browser.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="a3eb1-167">Questo sollevamento variabile in ambito globale elimina la duplicazione dello sforzo, in particolare per quanto riguarda il caricamento degli stessi dati una volta sul server e di nuovo sul client.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![variabile globale postList associata all'oggetto finestra](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="a3eb1-169">Middleware per sviluppatori di webpack</span><span class="sxs-lookup"><span data-stu-id="a3eb1-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="a3eb1-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduce un flusso di lavoro di sviluppo semplificato in base al quale Webpack crea risorse su richiesta.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="a3eb1-171">Il middleware compila e serve automaticamente le risorse lato client quando una pagina viene ricaricata nel browser.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="a3eb1-172">L'approccio alternativo consiste nel richiamare manualmente Webpack tramite lo script di compilazione npm del progetto quando viene modificata una dipendenza di terze parti o il codice personalizzato.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="a3eb1-173">Nell'esempio seguente viene illustrato uno script di compilazione npm nel file *package.json:An* npm build script in the package.json file is shown in the following example:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="a3eb1-174">Prerequisiti del Middleware per il sviluppo Di Webpack</span><span class="sxs-lookup"><span data-stu-id="a3eb1-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="a3eb1-175">Installare il pacchetto [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="a3eb1-176">Configurazione di Webpack Dev Middleware</span><span class="sxs-lookup"><span data-stu-id="a3eb1-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="a3eb1-177">Webpack Dev Middleware viene registrato nella pipeline delle richieste HTTP `Configure` tramite il codice seguente nel metodo del file *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="a3eb1-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="a3eb1-178">Il `UseWebpackDevMiddleware` metodo di estensione deve essere chiamato `UseStaticFiles` prima di registrare l'hosting di file [statici](xref:fundamentals/static-files) tramite il metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="a3eb1-179">Per motivi di sicurezza, registra il middleware solo quando l'app viene eseguita in modalità di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="a3eb1-180">La proprietà del `output.publicPath` file *webpack.config.js* indica al `dist` middleware di controllare la cartella per le modifiche:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="a3eb1-181">Sostituzione modulo caldo</span><span class="sxs-lookup"><span data-stu-id="a3eb1-181">Hot Module Replacement</span></span>

<span data-ttu-id="a3eb1-182">Pensate alla funzionalità [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) di Webpack come a un'evoluzione di [Webpack Dev Middleware](#webpack-dev-middleware).</span><span class="sxs-lookup"><span data-stu-id="a3eb1-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="a3eb1-183">HMR introduce tutti gli stessi vantaggi, ma semplifica ulteriormente il flusso di lavoro di sviluppo aggiornando automaticamente il contenuto della pagina dopo la compilazione delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="a3eb1-184">Non confondere questo con un aggiornamento del browser, che interferirebbe con lo stato corrente in memoria e la sessione di debug della SPA.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="a3eb1-185">C'è un collegamento live tra il servizio Webpack Dev Middleware e il browser, il che significa che le modifiche vengono inviate al browser.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="a3eb1-186">Prerequisiti per la sostituzione del modulo caldo</span><span class="sxs-lookup"><span data-stu-id="a3eb1-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="a3eb1-187">Installare il pacchetto [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="a3eb1-188">Configurazione di sostituzione del modulo a caldo</span><span class="sxs-lookup"><span data-stu-id="a3eb1-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="a3eb1-189">Il componente HMR deve essere registrato nella pipeline `Configure` delle richieste HTTP di MVC nel metodo:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="a3eb1-190">Come per [Webpack Dev Middleware](#webpack-dev-middleware), il `UseWebpackDevMiddleware` metodo `UseStaticFiles` di estensione deve essere chiamato prima del metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="a3eb1-191">Per motivi di sicurezza, registra il middleware solo quando l'app viene eseguita in modalità di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="a3eb1-192">Il file *webpack.config.js* `plugins` deve definire una matrice, anche se viene lasciata vuota:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="a3eb1-193">Dopo aver caricato l'app nel browser, la scheda Console degli strumenti di sviluppo fornisce la conferma dell'attivazione HMR:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![Messaggio connesso di sostituzione del modulo a caldo](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="a3eb1-195">Helper di routing</span><span class="sxs-lookup"><span data-stu-id="a3eb1-195">Routing helpers</span></span>

<span data-ttu-id="a3eb1-196">Nella maggior parte dei ASP.NET SP basati su Core, il routing sul lato client è spesso desiderato oltre al routing sul lato server.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="a3eb1-197">I sistemi di routing SPA e MVC possono funzionare in modo indipendente senza interferenze.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="a3eb1-198">C'è, tuttavia, un caso limite che pone sfide: identificare 404 risposte HTTP.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="a3eb1-199">Si consideri lo scenario in `/some/page` cui viene utilizzato un percorso senza estensione di.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="a3eb1-200">Si supponga che la richiesta non corrisponda a una route lato server, ma il relativo modello corrisponde a una route lato client.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="a3eb1-201">Si consideri ora `/images/user-512.png`una richiesta in ingresso per , che in genere prevede di trovare un file di immagine sul server.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="a3eb1-202">Se il percorso della risorsa richiesto non corrisponde ad alcuna route sul lato server o&mdash;file statico, è improbabile che l'applicazione lato client gestisca in genere la restituzione di un codice di stato HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="a3eb1-203">Prerequisiti degli helper di routing</span><span class="sxs-lookup"><span data-stu-id="a3eb1-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="a3eb1-204">Installare il pacchetto npm di routing sul lato client.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="a3eb1-205">Utilizzo di Angolare come esempio:Using Angular as an example:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="a3eb1-206">Configurazione degli helper di routing</span><span class="sxs-lookup"><span data-stu-id="a3eb1-206">Routing helpers configuration</span></span>

<span data-ttu-id="a3eb1-207">Un metodo `MapSpaFallbackRoute` di estensione `Configure` denominato viene utilizzato nel metodo:An extension method named is used in the method:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="a3eb1-208">Le route vengono valutate nell'ordine in cui sono configurate.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="a3eb1-209">Di conseguenza, la `default` route nell'esempio di codice precedente viene utilizzata prima per i criteri di ricerca.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="a3eb1-210">Creare un nuovo progetto</span><span class="sxs-lookup"><span data-stu-id="a3eb1-210">Create a new project</span></span>

<span data-ttu-id="a3eb1-211">I servizi JavaScript forniscono modelli di applicazione preconfigurati.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="a3eb1-212">SpaServices viene utilizzato in questi modelli in combinazione con framework e librerie diversi, ad esempio Angular, React e Redux.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="a3eb1-213">Questi modelli possono essere installati tramite l'interfaccia della riga di comando di .NET Core eseguendo il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="a3eb1-214">Viene visualizzato un elenco dei modelli SPA disponibili:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="a3eb1-215">Modelli</span><span class="sxs-lookup"><span data-stu-id="a3eb1-215">Templates</span></span>                                 | <span data-ttu-id="a3eb1-216">Nome breve</span><span class="sxs-lookup"><span data-stu-id="a3eb1-216">Short Name</span></span> | <span data-ttu-id="a3eb1-217">Linguaggio</span><span class="sxs-lookup"><span data-stu-id="a3eb1-217">Language</span></span> | <span data-ttu-id="a3eb1-218">Tag</span><span class="sxs-lookup"><span data-stu-id="a3eb1-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="a3eb1-219">MVC ASP.NET Core con angular</span><span class="sxs-lookup"><span data-stu-id="a3eb1-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="a3eb1-220">angular</span><span class="sxs-lookup"><span data-stu-id="a3eb1-220">angular</span></span>    | <span data-ttu-id="a3eb1-221">[C#]</span><span class="sxs-lookup"><span data-stu-id="a3eb1-221">[C#]</span></span>     | <span data-ttu-id="a3eb1-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="a3eb1-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="a3eb1-223">MVC ASP.NET Core con React.js</span><span class="sxs-lookup"><span data-stu-id="a3eb1-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="a3eb1-224">react</span><span class="sxs-lookup"><span data-stu-id="a3eb1-224">react</span></span>      | <span data-ttu-id="a3eb1-225">[C#]</span><span class="sxs-lookup"><span data-stu-id="a3eb1-225">[C#]</span></span>     | <span data-ttu-id="a3eb1-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="a3eb1-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="a3eb1-227">MVC ASP.NET Core con React.js e Redux</span><span class="sxs-lookup"><span data-stu-id="a3eb1-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="a3eb1-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="a3eb1-228">reactredux</span></span> | <span data-ttu-id="a3eb1-229">[C#]</span><span class="sxs-lookup"><span data-stu-id="a3eb1-229">[C#]</span></span>     | <span data-ttu-id="a3eb1-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="a3eb1-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="a3eb1-231">Per creare un nuovo progetto utilizzando uno dei modelli SPA, includere il **nome breve** del modello nel nuovo comando [dotnet.](/dotnet/core/tools/dotnet-new)</span><span class="sxs-lookup"><span data-stu-id="a3eb1-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="a3eb1-232">Il comando seguente crea un'applicazione angolare con ASP.NET Core MVC configurato per il lato server:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="a3eb1-233">Impostare la modalità di configurazione di runtime</span><span class="sxs-lookup"><span data-stu-id="a3eb1-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="a3eb1-234">Esistono due modalità di configurazione di runtime principale:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="a3eb1-235">**Sviluppo**:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-235">**Development**:</span></span>
  * <span data-ttu-id="a3eb1-236">Include mappe di origine per semplificare il debug.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="a3eb1-237">Non ottimizza il codice lato client per le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="a3eb1-238">**Produzione**:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-238">**Production**:</span></span>
  * <span data-ttu-id="a3eb1-239">Esclude le mappe di origine.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-239">Excludes source maps.</span></span>
  * <span data-ttu-id="a3eb1-240">Ottimizza il codice lato client tramite l'aggregazione e la minimizzazione.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="a3eb1-241">ASP.NET Core utilizza una `ASPNETCORE_ENVIRONMENT` variabile di ambiente denominata per archiviare la modalità di configurazione.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="a3eb1-242">Per ulteriori informazioni, consultate [Impostare l'ambiente.](xref:fundamentals/environments#set-the-environment)</span><span class="sxs-lookup"><span data-stu-id="a3eb1-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="a3eb1-243">Esegui con l'interfaccia della riga di comando di .NET CoreRun with .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a3eb1-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="a3eb1-244">Ripristinare i pacchetti NuGet e npm necessari eseguendo il comando seguente nella radice del progetto:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="a3eb1-245">Compilare ed eseguire l'applicazione:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="a3eb1-246">L'applicazione viene avviata in localhost in base alla modalità di [configurazione runtime.](#set-the-runtime-configuration-mode)</span><span class="sxs-lookup"><span data-stu-id="a3eb1-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="a3eb1-247">Navigando `http://localhost:5000` su nel browser viene visualizzata la pagina di destinazione.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="a3eb1-248">Eseguire con Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="a3eb1-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="a3eb1-249">Aprire il file *con estensione csproj* generato dal [comando dotnet new.](/dotnet/core/tools/dotnet-new)</span><span class="sxs-lookup"><span data-stu-id="a3eb1-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="a3eb1-250">I pacchetti NuGet e npm necessari vengono ripristinati automaticamente all'apertura del progetto.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="a3eb1-251">Questo processo di ripristino può richiedere alcuni minuti e l'applicazione è pronta per essere eseguita al termine.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="a3eb1-252">Fare clic sul pulsante verde Esegui o premere `Ctrl + F5`e il browser si apre sulla pagina di destinazione dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="a3eb1-253">L'applicazione viene eseguita in localhost in base alla modalità di [configurazione runtime.](#set-the-runtime-configuration-mode)</span><span class="sxs-lookup"><span data-stu-id="a3eb1-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="a3eb1-254">Testare l'app</span><span class="sxs-lookup"><span data-stu-id="a3eb1-254">Test the app</span></span>

<span data-ttu-id="a3eb1-255">I modelli SpaServices sono preconfigurati per l'esecuzione di test sul lato client utilizzando [Karma](https://karma-runner.github.io/1.0/index.html) e [Jasmine](https://jasmine.github.io/).</span><span class="sxs-lookup"><span data-stu-id="a3eb1-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="a3eb1-256">Jasmine è un framework di unit test popolare per JavaScript, mentre Karma è un test runner per quei test.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="a3eb1-257">Karma è configurato per funzionare con il [Webpack Dev Middleware](#webpack-dev-middleware) in modo che lo sviluppatore non è necessario arrestare ed eseguire il test ogni volta che vengono apportate modifiche.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="a3eb1-258">Sia che si tratti del codice in esecuzione sul test case o del test case stesso, il test viene eseguito automaticamente.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="a3eb1-259">Utilizzando l'applicazione angolare come esempio, sono già stati forniti `CounterComponent` due test case Jasmine per il nel file *counter.component.spec.ts:*</span><span class="sxs-lookup"><span data-stu-id="a3eb1-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="a3eb1-260">Aprire il prompt dei comandi nella directory *ClientApp.*</span><span class="sxs-lookup"><span data-stu-id="a3eb1-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="a3eb1-261">Eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="a3eb1-262">Lo script avvia il test runner Karma, che legge le impostazioni definite nel file *karma.conf.js.*</span><span class="sxs-lookup"><span data-stu-id="a3eb1-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="a3eb1-263">Tra le altre impostazioni, il *karma.conf.js* identifica i `files` file di test da eseguire tramite la sua matrice:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="a3eb1-264">Pubblicare l'app</span><span class="sxs-lookup"><span data-stu-id="a3eb1-264">Publish the app</span></span>

<span data-ttu-id="a3eb1-265">Vedere questo problema di GitHub per altre informazioni sulla pubblicazione in Azure.See this [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-265">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span></span>

<span data-ttu-id="a3eb1-266">La combinazione degli asset sul lato client generati e degli elementi di base ASP.NET pubblicati in un pacchetto pronto per la distribuzione può essere complessa.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-266">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="a3eb1-267">Fortunatamente, SpaServices orchestra l'intero processo di pubblicazione `RunWebpack`con una destinazione MSBuild personalizzata denominata :</span><span class="sxs-lookup"><span data-stu-id="a3eb1-267">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="a3eb1-268">La destinazione MSBuild ha le seguenti responsabilità:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-268">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="a3eb1-269">Ripristinare i pacchetti npm.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-269">Restore the npm packages.</span></span>
1. <span data-ttu-id="a3eb1-270">Crea una build di livello di produzione delle risorse lato client di terze parti.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-270">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="a3eb1-271">Creare una build di livello di produzione delle risorse lato client personalizzate.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-271">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="a3eb1-272">Copiate le risorse generate dal Pacchetto Web nella cartella di pubblicazione.</span><span class="sxs-lookup"><span data-stu-id="a3eb1-272">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="a3eb1-273">La destinazione MSBuild viene richiamata durante l'esecuzione:The MSBuild target is invoked when running:</span><span class="sxs-lookup"><span data-stu-id="a3eb1-273">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="a3eb1-274">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a3eb1-274">Additional resources</span></span>

* [<span data-ttu-id="a3eb1-275">Documenti angolari</span><span class="sxs-lookup"><span data-stu-id="a3eb1-275">Angular Docs</span></span>](https://angular.io/docs)
