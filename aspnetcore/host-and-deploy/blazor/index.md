---
title: Ospitare e distribuire ASP.NET CoreBlazor
author: guardrex
description: Scopri come ospitare e distribuire Blazor le app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 993c1e49aed2efb4283753ab184506e5ae33a3b2
ms.sourcegitcommit: 6318d2bdd63116e178c34492a904be85ec9ac108
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82604818"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="7ba34-103">Hosting e distribuzione di ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="7ba34-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="7ba34-104">Di [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="7ba34-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a><span data-ttu-id="7ba34-105">Pubblicare l'app</span><span class="sxs-lookup"><span data-stu-id="7ba34-105">Publish the app</span></span>

<span data-ttu-id="7ba34-106">Le app vengono pubblicate per la distribuzione nella configurazione per il rilascio.</span><span class="sxs-lookup"><span data-stu-id="7ba34-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7ba34-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ba34-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="7ba34-108">Selezionare **Compila** > **pubblicazione {applicazione}** dalla barra di spostamento.</span><span class="sxs-lookup"><span data-stu-id="7ba34-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="7ba34-109">Selezionare la *destinazione di pubblicazione*.</span><span class="sxs-lookup"><span data-stu-id="7ba34-109">Select the *publish target*.</span></span> <span data-ttu-id="7ba34-110">Per pubblicare in locale, selezionare **Cartella**.</span><span class="sxs-lookup"><span data-stu-id="7ba34-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="7ba34-111">Accettare il percorso predefinito nel campo **Scegliere una cartella** oppure specificarne uno diverso.</span><span class="sxs-lookup"><span data-stu-id="7ba34-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="7ba34-112">Fare clic sul pulsante **Pubblica**.</span><span class="sxs-lookup"><span data-stu-id="7ba34-112">Select the **Publish** button.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7ba34-113">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="7ba34-113">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="7ba34-114">Selezionare **Compila** > **pubblicazione in cartella**.</span><span class="sxs-lookup"><span data-stu-id="7ba34-114">Select **Build** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="7ba34-115">Confermare la cartella per ricevere gli asset pubblicati e selezionare **pubblica**.</span><span class="sxs-lookup"><span data-stu-id="7ba34-115">Confirm the folder to receive the published assets and select **Publish**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="7ba34-116">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="7ba34-116">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="7ba34-117">Usare il comando [dotnet publish](/dotnet/core/tools/dotnet-publish) per pubblicare l'app con una configurazione per il rilascio:</span><span class="sxs-lookup"><span data-stu-id="7ba34-117">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="7ba34-118">La pubblicazione dell'app attiva un [ripristino](/dotnet/core/tools/dotnet-restore) delle dipendenze del progetto e [compila](/dotnet/core/tools/dotnet-build) il progetto prima di creare gli asset per la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="7ba34-118">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="7ba34-119">Come parte del processo di compilazione, vengono rimossi gli assembly e i metodi non usati per ridurre le dimensioni del download e i tempi di caricamento dell'app.</span><span class="sxs-lookup"><span data-stu-id="7ba34-119">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="7ba34-120">Percorsi di pubblicazione:</span><span class="sxs-lookup"><span data-stu-id="7ba34-120">Publish locations:</span></span>

* Blazor<span data-ttu-id="7ba34-121">Webassembly</span><span class="sxs-lookup"><span data-stu-id="7ba34-121"> WebAssembly</span></span>
  * <span data-ttu-id="7ba34-122">Standalone &ndash; l'app viene pubblicata nella cartella */bin/Release/{Target Framework}/Publish/wwwroot*</span><span class="sxs-lookup"><span data-stu-id="7ba34-122">Standalone &ndash; The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span> <span data-ttu-id="7ba34-123">Per distribuire l'app come sito statico, copiare il contenuto della cartella *wwwroot* nell'host del sito statico.</span><span class="sxs-lookup"><span data-stu-id="7ba34-123">To deploy the app as a static site, copy the contents of the *wwwroot* folder to the static site host.</span></span>
  * <span data-ttu-id="7ba34-124">Ospitata &ndash; l' Blazor app webassembly client viene pubblicata nella cartella */bin/Release/{Target Framework}/Publish/wwwroot* dell'app Server, insieme a qualsiasi altra risorsa Web statica dell'app Server.</span><span class="sxs-lookup"><span data-stu-id="7ba34-124">Hosted &ndash; The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="7ba34-125">Distribuire il contenuto della cartella di *pubblicazione* nell'host.</span><span class="sxs-lookup"><span data-stu-id="7ba34-125">Deploy the contents of the *publish* folder to the host.</span></span>
* Blazor<span data-ttu-id="7ba34-126">Server &ndash; l'app viene pubblicata nella cartella */bin/Release/{Target Framework}/Publish*</span><span class="sxs-lookup"><span data-stu-id="7ba34-126"> Server &ndash; The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="7ba34-127">Distribuire il contenuto della cartella di *pubblicazione* nell'host.</span><span class="sxs-lookup"><span data-stu-id="7ba34-127">Deploy the contents of the *publish* folder to the host.</span></span>

<span data-ttu-id="7ba34-128">Gli asset nella cartella vengono distribuiti nel server Web.</span><span class="sxs-lookup"><span data-stu-id="7ba34-128">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="7ba34-129">La distribuzione potrebbe essere un processo manuale o automatizzato a seconda degli strumenti di sviluppo in uso.</span><span class="sxs-lookup"><span data-stu-id="7ba34-129">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="7ba34-130">Percorso di base dell'app</span><span class="sxs-lookup"><span data-stu-id="7ba34-130">App base path</span></span>

<span data-ttu-id="7ba34-131">Il *percorso di base dell'app* è il percorso dell'URL radice dell'app.</span><span class="sxs-lookup"><span data-stu-id="7ba34-131">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="7ba34-132">Si considerino i seguenti ASP.NET Core Blazor app e l'app secondaria:</span><span class="sxs-lookup"><span data-stu-id="7ba34-132">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="7ba34-133">L'app ASP.NET Core è denominata `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="7ba34-133">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="7ba34-134">L'app risiede fisicamente in *d:/MyApp*.</span><span class="sxs-lookup"><span data-stu-id="7ba34-134">The app physically resides at *d:/MyApp*.</span></span>
  * <span data-ttu-id="7ba34-135">Le richieste vengono ricevute all'indirizzo `https://www.contoso.com/{MYAPP RESOURCE}`.</span><span class="sxs-lookup"><span data-stu-id="7ba34-135">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="7ba34-136">Un' Blazor app denominata `CoolApp` è un'app secondaria di `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="7ba34-136">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="7ba34-137">La Sub-App risiede fisicamente in *d:/MyApp/CoolApp*.</span><span class="sxs-lookup"><span data-stu-id="7ba34-137">The sub-app physically resides at *d:/MyApp/CoolApp*.</span></span>
  * <span data-ttu-id="7ba34-138">Le richieste vengono ricevute all'indirizzo `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span><span class="sxs-lookup"><span data-stu-id="7ba34-138">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="7ba34-139">Se non si specifica una `CoolApp`configurazione aggiuntiva per, la sottoapp in questo scenario non è a conoscenza della posizione in cui risiede nel server.</span><span class="sxs-lookup"><span data-stu-id="7ba34-139">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="7ba34-140">Ad esempio, l'app non può costruire URL relativi corretti per le risorse senza sapere che risiede nel percorso `/CoolApp/`URL relativo.</span><span class="sxs-lookup"><span data-stu-id="7ba34-140">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="7ba34-141">Per fornire la configurazione per Blazor il percorso di base dell' `https://www.contoso.com/CoolApp/`app, `<base>` l' `href` attributo del tag viene impostato sul percorso radice relativo nel file *pages/_Host. cshtml* (Blazor Server) o *wwwroot/index.html* (Blazor webassembly):</span><span class="sxs-lookup"><span data-stu-id="7ba34-141">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

Blazor<span data-ttu-id="7ba34-142">Anche le app Server impostano il percorso di base sul <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> lato server chiamando nella pipeline delle richieste `Startup.Configure`dell'app di:</span><span class="sxs-lookup"><span data-stu-id="7ba34-142"> Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="7ba34-143">Fornendo il percorso URL relativo, un componente che non si trova nella directory radice può costruire URL relativi al percorso radice dell'app.</span><span class="sxs-lookup"><span data-stu-id="7ba34-143">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="7ba34-144">I componenti a livelli diversi della struttura di directory possono creare collegamenti ad altre risorse in posizioni all'interno dell'app.</span><span class="sxs-lookup"><span data-stu-id="7ba34-144">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="7ba34-145">Il percorso di base dell'app viene usato anche per intercettare i collegamenti ipertestuali selezionati in cui la `href` destinazione del collegamento si trova nello spazio URI del percorso di base dell'app.</span><span class="sxs-lookup"><span data-stu-id="7ba34-145">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="7ba34-146">Il Blazor router gestisce la navigazione interna.</span><span class="sxs-lookup"><span data-stu-id="7ba34-146">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="7ba34-147">In molti scenari di hosting il percorso URL relativo dell'app è la radice dell'app.</span><span class="sxs-lookup"><span data-stu-id="7ba34-147">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="7ba34-148">In questi casi, il percorso di base dell'URL relativo dell'app è una barra`<base href="/" />`(), ovvero la configurazione predefinita per un' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="7ba34-148">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="7ba34-149">In altri scenari di hosting, ad esempio pagine di GitHub e sottoapp di IIS, il percorso di base dell'app deve essere impostato sul percorso URL relativo del server dell'app.</span><span class="sxs-lookup"><span data-stu-id="7ba34-149">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="7ba34-150">Per impostare il percorso di base dell'app, aggiornare `<base>` il tag negli `<head>` elementi tag del file *pages/_Host. cshtml* (Blazor Server) o *wwwroot/index.html* (Blazor webassembly).</span><span class="sxs-lookup"><span data-stu-id="7ba34-150">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly).</span></span> <span data-ttu-id="7ba34-151">Impostare il `href` valore dell'attributo `/{RELATIVE URL PATH}/` su (la barra finale è obbligatoria), dove `{RELATIVE URL PATH}` è il percorso URL relativo completo dell'app.</span><span class="sxs-lookup"><span data-stu-id="7ba34-151">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="7ba34-152">Per un' Blazor app webassembly con un percorso URL relativo non radice (ad esempio, `<base href="/CoolApp/">`), l'app non riesce a trovare le proprie risorse quando viene *eseguita localmente*.</span><span class="sxs-lookup"><span data-stu-id="7ba34-152">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="7ba34-153">Per risolvere questo problema durante sviluppo e test locali, è possibile specificare un argomento *base del percorso* corrispondente al valore `href` del tag `<base>` in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="7ba34-153">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="7ba34-154">Non includere una barra finale.</span><span class="sxs-lookup"><span data-stu-id="7ba34-154">Don't include a trailing slash.</span></span> <span data-ttu-id="7ba34-155">Per passare l'argomento di base del percorso quando si esegue l'app in `dotnet run` locale, eseguire il comando dalla directory dell' `--pathbase` app con l'opzione:</span><span class="sxs-lookup"><span data-stu-id="7ba34-155">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="7ba34-156">Per un' Blazor app webassembly con un percorso URL relativo `/CoolApp/` (`<base href="/CoolApp/">`), il comando è:</span><span class="sxs-lookup"><span data-stu-id="7ba34-156">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="7ba34-157">L' Blazor app webassembly risponde localmente all'indirizzo `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="7ba34-157">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="7ba34-158">Distribuzione</span><span class="sxs-lookup"><span data-stu-id="7ba34-158">Deployment</span></span>

<span data-ttu-id="7ba34-159">Per indicazioni sulla distribuzione, vedere gli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="7ba34-159">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
