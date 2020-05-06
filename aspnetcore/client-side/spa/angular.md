---
title: Usare il modello di progetto per Angular con ASP.NET Core
author: SteveSandersonMS
description: Informazioni su come iniziare a usare il modello di progetto per applicazioni a pagina singola di ASP.NET Core per Angular e l'interfaccia della riga di comando di Angular.
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 02/06/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: spa/angular
ms.openlocfilehash: d6e52a7e2c8e9c2e440b187312eeee9fc06699ae
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773742"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a><span data-ttu-id="4528a-103">Usare il modello di progetto per Angular con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4528a-103">Use the Angular project template with ASP.NET Core</span></span>

<span data-ttu-id="4528a-104">Il modello di progetto aggiornato per Angular fornisce un ottimo punto di partenza per le app ASP.NET Core che usano Angular e l'interfaccia della riga di comando di Angular per implementare un'interfaccia utente avanzata sul lato client.</span><span class="sxs-lookup"><span data-stu-id="4528a-104">The updated Angular project template provides a convenient starting point for ASP.NET Core apps using Angular and the Angular CLI to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="4528a-105">Il modello è equivalente alla creazione di un progetto ASP.NET Core che opera come un back-end API e un progetto per l'interfaccia della riga di comando di Angular che opera come un'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="4528a-105">The template is equivalent to creating an ASP.NET Core project to act as an API backend and an Angular CLI project to act as a UI.</span></span> <span data-ttu-id="4528a-106">Il modello offre la praticità di ospitare entrambi i tipi di progetto in un singolo progetto di app.</span><span class="sxs-lookup"><span data-stu-id="4528a-106">The template offers the convenience of hosting both project types in a single app project.</span></span> <span data-ttu-id="4528a-107">Di conseguenza, il progetto di app può essere compilato e pubblicato come una singola unità.</span><span class="sxs-lookup"><span data-stu-id="4528a-107">Consequently, the app project can be built and published as a single unit.</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="4528a-108">Creare una nuova app</span><span class="sxs-lookup"><span data-stu-id="4528a-108">Create a new app</span></span>

<span data-ttu-id="4528a-109">Se ASP.NET Core 2.1 è installato, non è necessario installare il modello di progetto Angular.</span><span class="sxs-lookup"><span data-stu-id="4528a-109">If you have ASP.NET Core 2.1 installed, there's no need to install the Angular project template.</span></span>

<span data-ttu-id="4528a-110">Creare un nuovo progetto da un prompt dei comandi usando il comando `dotnet new angular` in una directory vuota.</span><span class="sxs-lookup"><span data-stu-id="4528a-110">Create a new project from a command prompt using the command `dotnet new angular` in an empty directory.</span></span> <span data-ttu-id="4528a-111">Ad esempio, i comandi seguenti creano l'app in una directory *my-new-app* e passano a tale directory:</span><span class="sxs-lookup"><span data-stu-id="4528a-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new angular -o my-new-app
cd my-new-app
```

<span data-ttu-id="4528a-112">Eseguire l'app da Visual Studio o dall'interfaccia della riga di comando di .NET Core:</span><span class="sxs-lookup"><span data-stu-id="4528a-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4528a-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4528a-113">Visual Studio</span></span>](#tab/visual-studio/)

<span data-ttu-id="4528a-114">Aprire il file con estensione *csproj* generato ed eseguire l'app come di consueto da tale posizione.</span><span class="sxs-lookup"><span data-stu-id="4528a-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="4528a-115">Alla prima esecuzione, il processo di compilazione ripristina le dipendenze di npm. Tale operazione può richiedere alcuni minuti.</span><span class="sxs-lookup"><span data-stu-id="4528a-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="4528a-116">Le compilazioni successive sono molto più veloci.</span><span class="sxs-lookup"><span data-stu-id="4528a-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4528a-117">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="4528a-117">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="4528a-118">Assicurarsi di disporre di una variabile di ambiente denominata `ASPNETCORE_Environment` con valore `Development`.</span><span class="sxs-lookup"><span data-stu-id="4528a-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with a value of `Development`.</span></span> <span data-ttu-id="4528a-119">In Windows (in prompt non PowerShell) eseguire `SET ASPNETCORE_Environment=Development`.</span><span class="sxs-lookup"><span data-stu-id="4528a-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="4528a-120">In Linux o Mac OS eseguire `export ASPNETCORE_Environment=Development`.</span><span class="sxs-lookup"><span data-stu-id="4528a-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="4528a-121">Eseguire [dotnet build](/dotnet/core/tools/dotnet-build) per verificare che l'app venga compilata correttamente.</span><span class="sxs-lookup"><span data-stu-id="4528a-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify the app builds correctly.</span></span> <span data-ttu-id="4528a-122">Alla prima esecuzione, il processo di compilazione ripristina le dipendenze di npm. Tale operazione può richiedere alcuni minuti.</span><span class="sxs-lookup"><span data-stu-id="4528a-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="4528a-123">Le compilazioni successive sono molto più veloci.</span><span class="sxs-lookup"><span data-stu-id="4528a-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="4528a-124">Eseguire [dotnet run](/dotnet/core/tools/dotnet-run) per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="4528a-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span> <span data-ttu-id="4528a-125">Verrà registrato un messaggio simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="4528a-125">A message similar to the following is logged:</span></span>

```console
Now listening on: http://localhost:<port>
```

<span data-ttu-id="4528a-126">Passare a questo URL in un browser.</span><span class="sxs-lookup"><span data-stu-id="4528a-126">Navigate to this URL in a browser.</span></span>

> [!WARNING]
> <span data-ttu-id="4528a-127">L'app avvia in background un'istanza del server dell'interfaccia della riga di comando di Angular.</span><span class="sxs-lookup"><span data-stu-id="4528a-127">The app starts up an instance of the Angular CLI server in the background.</span></span> <span data-ttu-id="4528a-128">Viene registrato un messaggio simile al seguente: *ng Live server di sviluppo è in ascolto su localhost:&lt;otherport&gt;, apre un browser a http://localhost:&lt; otherport&gt;*.</span><span class="sxs-lookup"><span data-stu-id="4528a-128">A message similar to the following is logged: *NG Live Development Server is listening on localhost:&lt;otherport&gt;, open a browser to http://localhost:&lt;otherport&gt;/*.</span></span> <span data-ttu-id="4528a-129">Ignorare questo messaggio: **non** si tratta dell'URL per l'app combinata per ASP.NET Core e l'interfaccia della riga di comando di Angular.</span><span class="sxs-lookup"><span data-stu-id="4528a-129">Ignore this message&mdash;it's **not** the URL for the combined ASP.NET Core and Angular CLI app.</span></span>

---

<span data-ttu-id="4528a-130">Il modello di progetto crea un'app ASP.NET Core e un'app Angular.</span><span class="sxs-lookup"><span data-stu-id="4528a-130">The project template creates an ASP.NET Core app and an Angular app.</span></span> <span data-ttu-id="4528a-131">L'app ASP.NET Core è destinata all'uso per l'accesso ai dati, l'autorizzazione e altri elementi sul lato server.</span><span class="sxs-lookup"><span data-stu-id="4528a-131">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="4528a-132">L'app Angular, disponibile nella sottodirectory *ClientApp*, è destinata all'uso per tutti gli aspetti relativi all'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="4528a-132">The Angular app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="4528a-133">Aggiungere pagine, immagini, stili, moduli e così via.</span><span class="sxs-lookup"><span data-stu-id="4528a-133">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="4528a-134">La directory *ClientApp* contiene un'app standard per l'interfaccia della riga di comando di Angular.</span><span class="sxs-lookup"><span data-stu-id="4528a-134">The *ClientApp* directory contains a standard Angular CLI app.</span></span> <span data-ttu-id="4528a-135">Per altre informazioni, vedere la [documentazione ufficiale di Angular](https://angular.io).</span><span class="sxs-lookup"><span data-stu-id="4528a-135">See the official [Angular documentation](https://angular.io) for more information.</span></span>

<span data-ttu-id="4528a-136">Vi sono piccole differenze tra l'app Angular creata tramite questo modello e quella creata tramite l'interfaccia della riga di comando di Angular stessa (mediante `ng new`). Le funzionalità dell'app restano comunque invariate.</span><span class="sxs-lookup"><span data-stu-id="4528a-136">There are slight differences between the Angular app created by this template and the one created by Angular CLI itself (via `ng new`); however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="4528a-137">L'app creata tramite il modello contiene un layout basato su [bootstrap](https://getbootstrap.com/) e un esempio di routing di base.</span><span class="sxs-lookup"><span data-stu-id="4528a-137">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="run-ng-commands"></a><span data-ttu-id="4528a-138">Eseguire i comandi ng</span><span class="sxs-lookup"><span data-stu-id="4528a-138">Run ng commands</span></span>

<span data-ttu-id="4528a-139">In un prompt dei comandi passare alla sottodirectory *ClientApp*:</span><span class="sxs-lookup"><span data-stu-id="4528a-139">In a command prompt, switch to the *ClientApp* subdirectory:</span></span>

```console
cd ClientApp
```

<span data-ttu-id="4528a-140">Se si dispone dello strumento `ng` installato globalmente, è possibile eseguire i relativi comandi.</span><span class="sxs-lookup"><span data-stu-id="4528a-140">If you have the `ng` tool installed globally, you can run any of its commands.</span></span> <span data-ttu-id="4528a-141">Ad esempio, è possibile eseguire `ng lint`, `ng test` o qualsiasi altro [comando dell'interfaccia della riga di comando di Angular](https://angular.io/cli).</span><span class="sxs-lookup"><span data-stu-id="4528a-141">For example, you can run `ng lint`, `ng test`, or any of the other [Angular CLI commands](https://angular.io/cli).</span></span> <span data-ttu-id="4528a-142">Non è tuttavia necessario eseguire `ng serve`, poiché l'app ASP.NET Core si occupa della gestione sia delle parti sul lato server che di quelle sul lato client dell'app.</span><span class="sxs-lookup"><span data-stu-id="4528a-142">There's no need to run `ng serve` though, because your ASP.NET Core app deals with serving both server-side and client-side parts of your app.</span></span> <span data-ttu-id="4528a-143">Internamente, usa `ng serve` in fase di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="4528a-143">Internally, it uses `ng serve` in development.</span></span>

<span data-ttu-id="4528a-144">Se lo strumento `ng` non è installato, eseguire `npm run ng`.</span><span class="sxs-lookup"><span data-stu-id="4528a-144">If you don't have the `ng` tool installed, run `npm run ng` instead.</span></span> <span data-ttu-id="4528a-145">Ad esempio, è possibile eseguire `npm run ng lint` o `npm run ng test`.</span><span class="sxs-lookup"><span data-stu-id="4528a-145">For example, you can run `npm run ng lint` or `npm run ng test`.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="4528a-146">Installa nuovi pacchetti npm</span><span class="sxs-lookup"><span data-stu-id="4528a-146">Install npm packages</span></span>

<span data-ttu-id="4528a-147">Per installare i pacchetti npm di terze parti, usare un prompt dei comandi nella sottodirectory *ClientApp*.</span><span class="sxs-lookup"><span data-stu-id="4528a-147">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="4528a-148">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="4528a-148">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="4528a-149">Pubblicare e distribuire</span><span class="sxs-lookup"><span data-stu-id="4528a-149">Publish and deploy</span></span>

<span data-ttu-id="4528a-150">In fase di sviluppo, l'app viene eseguita in una modalità ottimizzata per gli sviluppatori.</span><span class="sxs-lookup"><span data-stu-id="4528a-150">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="4528a-151">Ad esempio, i bundle JavaScript includono il mapping di origine, in modo da poter visualizzare il codice TypeScript originale durante il debug.</span><span class="sxs-lookup"><span data-stu-id="4528a-151">For example, JavaScript bundles include source maps (so that when debugging, you can see your original TypeScript code).</span></span> <span data-ttu-id="4528a-152">L'app controlla le modifiche dei file TypeScript, HTML e CSS su disco, quindi esegue automaticamente la ricompilazione e il ricaricamento quando rileva modifiche dei file.</span><span class="sxs-lookup"><span data-stu-id="4528a-152">The app watches for TypeScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="4528a-153">Nell'ambiente di produzione usare una versione dell'app ottimizzata per le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="4528a-153">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="4528a-154">Questo comportamento è configurato per l'esecuzione automatica.</span><span class="sxs-lookup"><span data-stu-id="4528a-154">This is configured to happen automatically.</span></span> <span data-ttu-id="4528a-155">Quando si esegue la pubblicazione, la configurazione della build genera una compilazione minimizzata AOT (Ahead Of Time) del codice sul lato client.</span><span class="sxs-lookup"><span data-stu-id="4528a-155">When you publish, the build configuration emits a minified, ahead-of-time (AoT) compiled build of your client-side code.</span></span> <span data-ttu-id="4528a-156">A differenza della build di sviluppo, la compilazione di produzione non richiede l'installazione di node. js nel server (a meno che non sia stato abilitato il rendering lato server (SSR)).</span><span class="sxs-lookup"><span data-stu-id="4528a-156">Unlike the development build, the production build doesn't require Node.js to be installed on the server (unless you have enabled server-side rendering (SSR)).</span></span>

<span data-ttu-id="4528a-157">È possibile usare i [metodi standard di hosting e distribuzione di ASP.NET Core](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="4528a-157">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-ng-serve-independently"></a><span data-ttu-id="4528a-158">Eseguire "ng serve" in modo indipendente</span><span class="sxs-lookup"><span data-stu-id="4528a-158">Run "ng serve" independently</span></span>

<span data-ttu-id="4528a-159">Il progetto è configurato in modo da avviare in background la propria istanza del server dell'interfaccia della riga di comando di Angular all'avvio dell'app ASP.NET Core in modalità di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="4528a-159">The project is configured to start its own instance of the Angular CLI server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="4528a-160">Ciò risulta utile in quanto evita di dover eseguire manualmente un server distinto.</span><span class="sxs-lookup"><span data-stu-id="4528a-160">This is convenient because you don't have to run a separate server manually.</span></span>

<span data-ttu-id="4528a-161">Questa configurazione predefinita presenta tuttavia uno svantaggio.</span><span class="sxs-lookup"><span data-stu-id="4528a-161">There's a drawback to this default setup.</span></span> <span data-ttu-id="4528a-162">Ogni volta che si modifica il codice C# ed è necessario riavviare l'app ASP.NET Core, il server dell'interfaccia della riga di comando di Angular viene riavviato.</span><span class="sxs-lookup"><span data-stu-id="4528a-162">Each time you modify your C# code and your ASP.NET Core app needs to restart, the Angular CLI server restarts.</span></span> <span data-ttu-id="4528a-163">Per avviare il backup sono necessari circa 10 secondi.</span><span class="sxs-lookup"><span data-stu-id="4528a-163">Around 10 seconds is required to start back up.</span></span> <span data-ttu-id="4528a-164">Se si apportano frequentemente modifiche al codice C# e non si vuole attendere il riavvio dell'interfaccia della riga di comando di Angular, eseguire il server dell'interfaccia della riga di comando di Angular esternamente, in modo indipendente dal processo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4528a-164">If you're making frequent C# code edits and don't want to wait for Angular CLI to restart, run the Angular CLI server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="4528a-165">A tale scopo, procedere nel seguente modo:</span><span class="sxs-lookup"><span data-stu-id="4528a-165">To do so:</span></span>

1. <span data-ttu-id="4528a-166">In un prompt dei comandi passare alla sottodirectory *ClientApp* e avviare il server di sviluppo dell'interfaccia della riga di comando di Angular:</span><span class="sxs-lookup"><span data-stu-id="4528a-166">In a command prompt, switch to the *ClientApp* subdirectory, and launch the Angular CLI development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > <span data-ttu-id="4528a-167">Usare `npm start` per avviare il server di sviluppo dell'interfaccia della riga di comando di Angular, anziché `ng serve`, in modo che la configurazione in *package.json* venga rispettata.</span><span class="sxs-lookup"><span data-stu-id="4528a-167">Use `npm start` to launch the Angular CLI development server, not `ng serve`, so that the configuration in *package.json* is respected.</span></span> <span data-ttu-id="4528a-168">Per passare parametri aggiuntivi al server dell'interfaccia della riga di comando di Angular, aggiungerli alla riga appropriata `scripts` nel file *package.json*.</span><span class="sxs-lookup"><span data-stu-id="4528a-168">To pass additional parameters to the Angular CLI server, add them to the relevant `scripts` line in your *package.json* file.</span></span>

2. <span data-ttu-id="4528a-169">Modificare l'app ASP.NET Core in modo da usare l'istanza dell'interfaccia della riga di comando di Angular esterna anziché avviarne una autonomamente.</span><span class="sxs-lookup"><span data-stu-id="4528a-169">Modify your ASP.NET Core app to use the external Angular CLI instance instead of launching one of its own.</span></span> <span data-ttu-id="4528a-170">Nella classe *Startup* sostituire la chiamata `spa.UseAngularCliServer` con quanto segue:</span><span class="sxs-lookup"><span data-stu-id="4528a-170">In your *Startup* class, replace the `spa.UseAngularCliServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

<span data-ttu-id="4528a-171">All'avvio dell'app ASP.NET Core, questa non avvierà un server dell'interfaccia della riga di comando di Angular.</span><span class="sxs-lookup"><span data-stu-id="4528a-171">When you start your ASP.NET Core app, it won't launch an Angular CLI server.</span></span> <span data-ttu-id="4528a-172">Verrà invece usata l'istanza che è stata avviata manualmente.</span><span class="sxs-lookup"><span data-stu-id="4528a-172">The instance you started manually is used instead.</span></span> <span data-ttu-id="4528a-173">Ciò consente di velocizzare l'avvio e il riavvio.</span><span class="sxs-lookup"><span data-stu-id="4528a-173">This enables it to start and restart faster.</span></span> <span data-ttu-id="4528a-174">Non è più necessario attendere che l'app client venga ricompilata ogni volta dall'interfaccia della riga di comando di Angular.</span><span class="sxs-lookup"><span data-stu-id="4528a-174">It's no longer waiting for Angular CLI to rebuild your client app each time.</span></span>

### <a name="pass-data-from-net-code-into-typescript-code"></a><span data-ttu-id="4528a-175">Passare dati dal codice .NET nel codice TypeScript</span><span class="sxs-lookup"><span data-stu-id="4528a-175">Pass data from .NET code into TypeScript code</span></span>

<span data-ttu-id="4528a-176">Durante il rendering lato server, potrebbe essere necessario passare dati per ogni richiesta dall'app ASP.NET Core nell'app Angular.</span><span class="sxs-lookup"><span data-stu-id="4528a-176">During SSR, you might want to pass per-request data from your ASP.NET Core app into your Angular app.</span></span> <span data-ttu-id="4528a-177">Ad esempio, è possibile passare informazioni sui cookie o dati letti da un database.</span><span class="sxs-lookup"><span data-stu-id="4528a-177">For example, you could pass cookie information or something read from a database.</span></span> <span data-ttu-id="4528a-178">A tale scopo, modificare la classe *Startup*.</span><span class="sxs-lookup"><span data-stu-id="4528a-178">To do this, edit your *Startup* class.</span></span> <span data-ttu-id="4528a-179">Nel callback per `UseSpaPrerendering`, impostare un valore per `options.SupplyData` come il seguente:</span><span class="sxs-lookup"><span data-stu-id="4528a-179">In the callback for `UseSpaPrerendering`, set a value for `options.SupplyData` such as the following:</span></span>

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

<span data-ttu-id="4528a-180">Il callback `SupplyData` consente di passare dati arbitrari serializzabili con JSON per ogni richiesta, ad esempio stringhe, valori booleani o numeri.</span><span class="sxs-lookup"><span data-stu-id="4528a-180">The `SupplyData` callback lets you pass arbitrary, per-request, JSON-serializable data (for example, strings, booleans, or numbers).</span></span> <span data-ttu-id="4528a-181">Il codice *main.server.ts* riceve questi dati come `params.data`.</span><span class="sxs-lookup"><span data-stu-id="4528a-181">Your *main.server.ts* code receives this as `params.data`.</span></span> <span data-ttu-id="4528a-182">Ad esempio, l'esempio di codice precedente passa un valore booleano come `params.data.isHttpsRequest` nel callback `createServerRenderer`.</span><span class="sxs-lookup"><span data-stu-id="4528a-182">For example, the preceding code sample passes a boolean value as `params.data.isHttpsRequest` into the `createServerRenderer` callback.</span></span> <span data-ttu-id="4528a-183">È possibile passare questo valore ad altre parti dell'app in qualsiasi modo supportato da Angular.</span><span class="sxs-lookup"><span data-stu-id="4528a-183">You can pass this to other parts of your app in any way supported by Angular.</span></span> <span data-ttu-id="4528a-184">Ad esempio, si osservi come *main.server.ts* passa il valore `BASE_URL` a qualsiasi componente il cui costruttore viene dichiarato per la relativa ricezione.</span><span class="sxs-lookup"><span data-stu-id="4528a-184">For example, see how *main.server.ts* passes the `BASE_URL` value to any component whose constructor is declared to receive it.</span></span>

### <a name="drawbacks-of-ssr"></a><span data-ttu-id="4528a-185">Svantaggi del rendering lato server</span><span class="sxs-lookup"><span data-stu-id="4528a-185">Drawbacks of SSR</span></span>

<span data-ttu-id="4528a-186">Non tutte le app traggono vantaggio dal rendering lato server.</span><span class="sxs-lookup"><span data-stu-id="4528a-186">Not all apps benefit from SSR.</span></span> <span data-ttu-id="4528a-187">Il vantaggio principale è rappresentato dalle prestazioni percepite.</span><span class="sxs-lookup"><span data-stu-id="4528a-187">The primary benefit is perceived performance.</span></span> <span data-ttu-id="4528a-188">I visitatori che raggiungono l'app tramite una connessione di rete lenta o da dispositivi mobili lenti vedono l'interfaccia utente iniziale rapidamente, anche se occorre un certo tempo per recuperare o analizzare i bundle JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4528a-188">Visitors reaching your app over a slow network connection or on slow mobile devices see the initial UI quickly, even if it takes a while to fetch or parse the JavaScript bundles.</span></span> <span data-ttu-id="4528a-189">Tuttavia, molte applicazioni a pagina singola sono usate principalmente in reti aziendali interne, su computer veloci in cui l'app viene visualizzata quasi istantaneamente.</span><span class="sxs-lookup"><span data-stu-id="4528a-189">However, many SPAs are mainly used over fast, internal company networks on fast computers where the app appears almost instantly.</span></span>

<span data-ttu-id="4528a-190">Allo stesso tempo, l'abilitazione del rendering lato server presenta alcuni svantaggi significativi.</span><span class="sxs-lookup"><span data-stu-id="4528a-190">At the same time, there are significant drawbacks to enabling SSR.</span></span> <span data-ttu-id="4528a-191">Aumenta la complessità del processo di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="4528a-191">It adds complexity to your development process.</span></span> <span data-ttu-id="4528a-192">Il codice deve essere eseguito in due diversi ambienti: lato client e lato server (in un ambiente Node.js richiamato da ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="4528a-192">Your code must run in two different environments: client-side and server-side (in a Node.js environment invoked from ASP.NET Core).</span></span> <span data-ttu-id="4528a-193">Di seguito sono illustrati alcuni aspetti da tenere presente:</span><span class="sxs-lookup"><span data-stu-id="4528a-193">Here are some things to bear in mind:</span></span>

* <span data-ttu-id="4528a-194">Il rendering lato server richiede un'installazione di Node.js nei server di produzione.</span><span class="sxs-lookup"><span data-stu-id="4528a-194">SSR requires a Node.js installation on your production servers.</span></span> <span data-ttu-id="4528a-195">Ciò avviene automaticamente per alcuni scenari di distribuzione, ad esempio Servizio app di Azure, ma non per altri, come Azure Service Fabric.</span><span class="sxs-lookup"><span data-stu-id="4528a-195">This is automatically the case for some deployment scenarios, such as Azure App Services, but not for others, such as Azure Service Fabric.</span></span>
* <span data-ttu-id="4528a-196">L'abilitazione del flag di compilazione `BuildServerSideRenderer` determina la pubblicazione della directory *node_modules*.</span><span class="sxs-lookup"><span data-stu-id="4528a-196">Enabling the `BuildServerSideRenderer` build flag causes your *node_modules* directory to publish.</span></span> <span data-ttu-id="4528a-197">Questa cartella contiene oltre 20.000 file, che aumentano il tempo di distribuzione.</span><span class="sxs-lookup"><span data-stu-id="4528a-197">This folder contains 20,000+ files, which increases deployment time.</span></span>
* <span data-ttu-id="4528a-198">Per eseguire il codice in un ambiente Node.js, non è possibile basarsi sull'esistenza di API JavaScript specifiche del browser, come `window` o `localStorage`.</span><span class="sxs-lookup"><span data-stu-id="4528a-198">To run your code in a Node.js environment, it can't rely on the existence of browser-specific JavaScript APIs such as `window` or `localStorage`.</span></span> <span data-ttu-id="4528a-199">Se il codice (o una libreria di terze parti a cui si fa riferimento) tenta di usare queste API, verrà visualizzato un errore durante il rendering lato server.</span><span class="sxs-lookup"><span data-stu-id="4528a-199">If your code (or some third-party library you reference) tries to use these APIs, you'll get an error during SSR.</span></span> <span data-ttu-id="4528a-200">Ad esempio, non usare jQuery perché fa riferimento ad API specifiche del browser in numerose posizioni.</span><span class="sxs-lookup"><span data-stu-id="4528a-200">For example, don't use jQuery because it references browser-specific APIs in many places.</span></span> <span data-ttu-id="4528a-201">Per evitare errori, è necessario non usare il rendering lato server o evitare API o librerie specifiche del browser.</span><span class="sxs-lookup"><span data-stu-id="4528a-201">To prevent errors, you must either avoid SSR or avoid browser-specific APIs or libraries.</span></span> <span data-ttu-id="4528a-202">È possibile eseguire il wrapping di tutte le chiamate a tali API nei controlli per assicurarsi che non vengano richiamate durante il rendering lato server.</span><span class="sxs-lookup"><span data-stu-id="4528a-202">You can wrap any calls to such APIs in checks to ensure they aren't invoked during SSR.</span></span> <span data-ttu-id="4528a-203">Nel codice JavaScript o TypeScript, ad esempio, usare un controllo simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="4528a-203">For example, use a check such as the following in JavaScript or TypeScript code:</span></span>

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="4528a-204">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="4528a-204">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
