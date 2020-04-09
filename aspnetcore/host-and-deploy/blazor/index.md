---
title: Ospitare e distribuire ASP.NET CoreBlazor
author: guardrex
description: Scopri come ospitare Blazor e distribuire app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: ddf70da29a82d462422c1bdf74ff45b92bb10b56
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434265"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="f0558-103">Hosting e distribuzione di ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="f0558-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="f0558-104">Di [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="f0558-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a><span data-ttu-id="f0558-105">Pubblicare l'app</span><span class="sxs-lookup"><span data-stu-id="f0558-105">Publish the app</span></span>

<span data-ttu-id="f0558-106">Le app vengono pubblicate per la distribuzione nella configurazione per il rilascio.</span><span class="sxs-lookup"><span data-stu-id="f0558-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f0558-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f0558-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f0558-108">Selezionare **Compilazione** > **Pubblicazione ,APPLICATION dalla** barra di spostamento.</span><span class="sxs-lookup"><span data-stu-id="f0558-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="f0558-109">Selezionare la *destinazione di pubblicazione*.</span><span class="sxs-lookup"><span data-stu-id="f0558-109">Select the *publish target*.</span></span> <span data-ttu-id="f0558-110">Per pubblicare in locale, selezionare **Cartella**.</span><span class="sxs-lookup"><span data-stu-id="f0558-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="f0558-111">Accettare il percorso predefinito nel campo **Scegliere una cartella** oppure specificarne uno diverso.</span><span class="sxs-lookup"><span data-stu-id="f0558-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="f0558-112">Fare clic sul pulsante **Pubblica**.</span><span class="sxs-lookup"><span data-stu-id="f0558-112">Select the **Publish** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f0558-113">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="f0558-113">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f0558-114">Usare il comando [dotnet publish](/dotnet/core/tools/dotnet-publish) per pubblicare l'app con una configurazione per il rilascio:</span><span class="sxs-lookup"><span data-stu-id="f0558-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="f0558-115">La pubblicazione dell'app attiva un [ripristino](/dotnet/core/tools/dotnet-restore) delle dipendenze del progetto e [compila](/dotnet/core/tools/dotnet-build) il progetto prima di creare gli asset per la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="f0558-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="f0558-116">Come parte del processo di compilazione, vengono rimossi gli assembly e i metodi non usati per ridurre le dimensioni del download e i tempi di caricamento dell'app.</span><span class="sxs-lookup"><span data-stu-id="f0558-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="f0558-117">Posizioni di pubblicazione:</span><span class="sxs-lookup"><span data-stu-id="f0558-117">Publish locations:</span></span>

* Blazor<span data-ttu-id="f0558-118">Assembly Web</span><span class="sxs-lookup"><span data-stu-id="f0558-118"> WebAssembly</span></span>
  * <span data-ttu-id="f0558-119">L'app è autonoma &ndash; pubblicata nella cartella */bin/Release/'TARGET FRAMEWORK'/publish/wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="f0558-119">Standalone &ndash; The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span> <span data-ttu-id="f0558-120">Per distribuire l'app come sito statico, copiare il contenuto della cartella *wwwroot* nell'host del sito statico.</span><span class="sxs-lookup"><span data-stu-id="f0558-120">To deploy the app as a static site, copy the contents of the *wwwroot* folder to the static site host.</span></span>
  * <span data-ttu-id="f0558-121">Hosted &ndash; L'app WebAssembly del client Blazor viene pubblicata nella cartella */bin/Release/*</span><span class="sxs-lookup"><span data-stu-id="f0558-121">Hosted &ndash; The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="f0558-122">Distribuire il contenuto della cartella di *pubblicazione* nell'host.</span><span class="sxs-lookup"><span data-stu-id="f0558-122">Deploy the contents of the *publish* folder to the host.</span></span>
* Blazor<span data-ttu-id="f0558-123">Server &ndash; L'app viene pubblicata nella cartella */bin/Release/'TARGET FRAMEWORK'/publish.*</span><span class="sxs-lookup"><span data-stu-id="f0558-123"> Server &ndash; The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="f0558-124">Distribuire il contenuto della cartella di *pubblicazione* nell'host.</span><span class="sxs-lookup"><span data-stu-id="f0558-124">Deploy the contents of the *publish* folder to the host.</span></span>

<span data-ttu-id="f0558-125">Gli asset nella cartella vengono distribuiti nel server Web.</span><span class="sxs-lookup"><span data-stu-id="f0558-125">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="f0558-126">La distribuzione potrebbe essere un processo manuale o automatizzato a seconda degli strumenti di sviluppo in uso.</span><span class="sxs-lookup"><span data-stu-id="f0558-126">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="f0558-127">Percorso di base dell'app</span><span class="sxs-lookup"><span data-stu-id="f0558-127">App base path</span></span>

<span data-ttu-id="f0558-128">Il *percorso di base dell'app* è il percorso dell'URL radice dell'app.</span><span class="sxs-lookup"><span data-stu-id="f0558-128">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="f0558-129">Si consideri il Blazor seguente ASP.NET app core e sotto-app:</span><span class="sxs-lookup"><span data-stu-id="f0558-129">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="f0558-130">L'app ASP.NET `MyApp`Core è denominata:</span><span class="sxs-lookup"><span data-stu-id="f0558-130">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="f0558-131">L'app risiede fisicamente in *d:/MyApp*.</span><span class="sxs-lookup"><span data-stu-id="f0558-131">The app physically resides at *d:/MyApp*.</span></span>
  * <span data-ttu-id="f0558-132">Le richieste `https://www.contoso.com/{MYAPP RESOURCE}`vengono ricevute all'indirizzo .</span><span class="sxs-lookup"><span data-stu-id="f0558-132">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="f0558-133">Blazor Un'app `CoolApp` denominata è `MyApp`un'app secondaria di:</span><span class="sxs-lookup"><span data-stu-id="f0558-133">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="f0558-134">La sotto-app risiede fisicamente in *d:/MyApp/CoolApp*.</span><span class="sxs-lookup"><span data-stu-id="f0558-134">The sub-app physically resides at *d:/MyApp/CoolApp*.</span></span>
  * <span data-ttu-id="f0558-135">Le richieste `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`vengono ricevute all'indirizzo .</span><span class="sxs-lookup"><span data-stu-id="f0558-135">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="f0558-136">Senza specificare la `CoolApp`configurazione aggiuntiva per , la sotto-app in questo scenario non ha alcuna conoscenza di dove risiede sul server.</span><span class="sxs-lookup"><span data-stu-id="f0558-136">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="f0558-137">Ad esempio, l'app non può costruire URL relativi corretti alle relative risorse senza `/CoolApp/`sapere che si trova nel percorso URL relativo.</span><span class="sxs-lookup"><span data-stu-id="f0558-137">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="f0558-138">Per fornire la Blazor configurazione per il `https://www.contoso.com/CoolApp/`percorso `<base>` di `href` base dell'app di , l'attributo del tagBlazor viene impostato sul percorso radice relativo nel file *Pages/_Host.cshtml* (Server) o nel file *di wwwroot/index.html* (WebAssembly):Blazor</span><span class="sxs-lookup"><span data-stu-id="f0558-138">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

Blazor<span data-ttu-id="f0558-139">Le app server impostano inoltre il <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> percorso di base lato `Startup.Configure`server chiamando nella pipeline di richiesta dell'app:</span><span class="sxs-lookup"><span data-stu-id="f0558-139"> Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="f0558-140">Fornendo il percorso URL relativo, un componente che non si fa nella directory radice può costruire URL relativi al percorso radice dell'app.</span><span class="sxs-lookup"><span data-stu-id="f0558-140">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="f0558-141">I componenti a diversi livelli della struttura di directory possono creare collegamenti ad altre risorse in posizioni in tutta l'app.</span><span class="sxs-lookup"><span data-stu-id="f0558-141">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="f0558-142">Il percorso di base dell'app viene `href` usato anche per intercettare i collegamenti ipertestuali selezionati in cui la destinazione del collegamento si trova all'interno dello spazio URI del percorso di base dell'app.</span><span class="sxs-lookup"><span data-stu-id="f0558-142">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="f0558-143">Il Blazor router gestisce la navigazione interna.</span><span class="sxs-lookup"><span data-stu-id="f0558-143">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="f0558-144">In molti scenari di hosting, il percorso URL relativo dell'app è la radice dell'app.</span><span class="sxs-lookup"><span data-stu-id="f0558-144">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="f0558-145">In questi casi, il percorso di base dell'URL relativo dell'app è una barra (`<base href="/" />`), che è la configurazione predefinita per un'app. Blazor</span><span class="sxs-lookup"><span data-stu-id="f0558-145">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="f0558-146">In altri scenari di hosting, ad esempio gitHub Pages e i sottoapp IIS, il percorso di base dell'app deve essere impostato sul percorso URL relativo del server dell'app.</span><span class="sxs-lookup"><span data-stu-id="f0558-146">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="f0558-147">Per impostare il percorso di `<base>` base dell'app, aggiornare il tag `<head>` all'interno degli elementiBlazor tag del file *Pages/_Host.cshtml* (Server)Blazor o del file di *wwwroot/index.html* (WebAssembly).</span><span class="sxs-lookup"><span data-stu-id="f0558-147">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly).</span></span> <span data-ttu-id="f0558-148">Imposta `href` il valore `/{RELATIVE URL PATH}/` dell'attributo su (la barra finale è obbligatoria), dove `{RELATIVE URL PATH}` è il percorso URL relativo completo dell'app.</span><span class="sxs-lookup"><span data-stu-id="f0558-148">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="f0558-149">Per Blazor un'app WebAssembly con un percorso URL `<base href="/CoolApp/">`relativo non radice (ad esempio, ), l'app non riesce a trovare le risorse *quando viene eseguita in locale.*</span><span class="sxs-lookup"><span data-stu-id="f0558-149">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="f0558-150">Per risolvere questo problema durante sviluppo e test locali, è possibile specificare un argomento *base del percorso* corrispondente al valore `href` del tag `<base>` in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="f0558-150">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="f0558-151">Non includere una barra finale.</span><span class="sxs-lookup"><span data-stu-id="f0558-151">Don't include a trailing slash.</span></span> <span data-ttu-id="f0558-152">Per passare l'argomento di base del `dotnet run` percorso durante l'esecuzione dell'app in locale, esegui il comando dalla directory dell'app con l'opzione: `--pathbase`</span><span class="sxs-lookup"><span data-stu-id="f0558-152">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="f0558-153">Per Blazor un'app WebAssembly con `/CoolApp/` un`<base href="/CoolApp/">`percorso URL relativo di ( ), il comando è:</span><span class="sxs-lookup"><span data-stu-id="f0558-153">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="f0558-154">L'app Blazor WebAssembly risponde `http://localhost:port/CoolApp`localmente in corrispondenza di .</span><span class="sxs-lookup"><span data-stu-id="f0558-154">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="f0558-155">Distribuzione</span><span class="sxs-lookup"><span data-stu-id="f0558-155">Deployment</span></span>

<span data-ttu-id="f0558-156">Per indicazioni sulla distribuzione, vedere gli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="f0558-156">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
