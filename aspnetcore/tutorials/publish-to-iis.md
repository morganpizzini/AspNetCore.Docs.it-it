---
title: Pubblicare un'app ASP.NET Core in IIS
author: rick-anderson
description: Informazioni su come ospitare un'app ASP.NET Core in un server IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/publish-to-iis
ms.openlocfilehash: b3c714ea8e741430df1f70b2df258f1e8f1c7ad5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060508"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="e2ef5-103">Pubblicare un'app ASP.NET Core in IIS</span><span class="sxs-lookup"><span data-stu-id="e2ef5-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="e2ef5-104">Questa esercitazione mostra come ospitare un'app ASP.NET Core in un server IIS.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-104">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="e2ef5-105">Questa esercitazione illustra le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="e2ef5-105">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e2ef5-106">Installare il bundle di hosting di.NET Core in Windows Server.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-106">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="e2ef5-107">Creare un sito IIS in Gestione IIS.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-107">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="e2ef5-108">Distribuire un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-108">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e2ef5-109">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="e2ef5-109">Prerequisites</span></span>

* <span data-ttu-id="e2ef5-110">[.NET Core SDK](/dotnet/core/sdk) installato nel computer di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-110">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="e2ef5-111">Windows Server configurato con il ruolo del server **Server Web (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="e2ef5-111">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="e2ef5-112">Se il server non è configurato per ospitare siti Web con IIS, seguire le istruzioni nella sezione *Configurazione di IIS* dell'articolo <xref:host-and-deploy/iis/index#iis-configuration> e quindi tornare a questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-112">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="e2ef5-113">**La configurazione di IIS e la sicurezza del sito Web coinvolgono concetti non trattati in questa esercitazione.**</span><span class="sxs-lookup"><span data-stu-id="e2ef5-113">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="e2ef5-114">Consultare le linee guida per IIS nella [documentazione di Microsoft IIS](https://www.iis.net/) e l'[articolo di ASP.NET Core sull'hosting con IIS](xref:host-and-deploy/iis/index) prima di ospitare app di produzione in IIS.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-114">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="e2ef5-115">Gli scenari importanti per l'hosting di IIS non trattati in questa esercitazione includono:</span><span class="sxs-lookup"><span data-stu-id="e2ef5-115">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="e2ef5-116">Creazione di un hive del Registro di sistema per la protezione dei dati ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e2ef5-116">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/index#data-protection)
> * [<span data-ttu-id="e2ef5-117">Configurazione dell'elenco di controllo di accesso (ACL) del pool di app</span><span class="sxs-lookup"><span data-stu-id="e2ef5-117">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/index#application-pool-identity)
> * <span data-ttu-id="e2ef5-118">Per concentrarsi sui concetti correlati alla distribuzione di IIS, questa esercitazione illustra come distribuire un'app senza sicurezza HTTPS configurata in IIS.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-118">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="e2ef5-119">Per altre informazioni sull'hosting di un'app abilitata per il protocollo HTTPS, vedere gli argomenti relativi alla sicurezza nella sezione [Risorse aggiuntive](#additional-resources) di questo articolo.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-119">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="e2ef5-120">Altre indicazioni per l'hosting di app ASP.NET Core sono disponibili nell'articolo <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-120">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="e2ef5-121">Installare il bundle di hosting .NET Core</span><span class="sxs-lookup"><span data-stu-id="e2ef5-121">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="e2ef5-122">Installare il *bundle di hosting .NET Core* nel server IIS.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-122">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="e2ef5-123">Il bundle installa il runtime di .NET Core, la libreria di .NET Core e il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="e2ef5-123">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="e2ef5-124">Il modulo consente l'esecuzione delle app ASP.NET Core dietro IIS.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-124">The module allows ASP.NET Core apps to run behind IIS.</span></span>

<span data-ttu-id="e2ef5-125">Scaricare il programma di installazione mediante il collegamento seguente:</span><span class="sxs-lookup"><span data-stu-id="e2ef5-125">Download the installer using the following link:</span></span>

[<span data-ttu-id="e2ef5-126">Programma di installazione del bundle di hosting .NET Core corrente (download diretto)</span><span class="sxs-lookup"><span data-stu-id="e2ef5-126">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="e2ef5-127">Eseguire il programma di installazione nel server IIS.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-127">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="e2ef5-128">Riavviare il server o eseguire `net stop was /y` seguito da `net start w3svc` in una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-128">Restart the server or execute `net stop was /y` followed by `net start w3svc` in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="e2ef5-129">Creare il sito IIS</span><span class="sxs-lookup"><span data-stu-id="e2ef5-129">Create the IIS site</span></span>

1. <span data-ttu-id="e2ef5-130">Nel server IIS creare una cartella per contenere le cartelle e i file pubblicati dell'app.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-130">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="e2ef5-131">In un passaggio successivo, il percorso della cartella viene fornito a IIS come percorso fisico dell'app.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-131">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="e2ef5-132">Per altre informazioni sulla cartella di distribuzione e il layout dei file di un'app, vedere <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-132">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="e2ef5-133">In Gestione IIS aprire il nodo del server nel pannello **connessioni** .</span><span class="sxs-lookup"><span data-stu-id="e2ef5-133">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="e2ef5-134">Fare clic con il pulsante destro del mouse sulla cartella **Siti** .</span><span class="sxs-lookup"><span data-stu-id="e2ef5-134">Right-click the **Sites** folder.</span></span> <span data-ttu-id="e2ef5-135">Scegliere **Aggiungi sito Web** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-135">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="e2ef5-136">Specificare un **Nome del sito** e impostare il **Percorso fisico** per la cartella di distribuzione dell'app che è stata creata.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-136">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="e2ef5-137">Specificare la configurazione in **Binding** e creare il sito Web selezionando **OK** .</span><span class="sxs-lookup"><span data-stu-id="e2ef5-137">Provide the **Binding** configuration and create the website by selecting **OK** .</span></span>

   > [!WARNING]
   > <span data-ttu-id="e2ef5-138">Le associazioni con caratteri jolly di livello superiore (`http://*:80/` e `http://+:80`) **non** devono essere usate,</span><span class="sxs-lookup"><span data-stu-id="e2ef5-138">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="e2ef5-139">poiché possono introdurre vulnerabilità a livello di sicurezza nell'app.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-139">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="e2ef5-140">Questo concetto vale sia per i caratteri jolly sicuri che vulnerabili.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-140">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="e2ef5-141">Usare nomi host espliciti al posto di caratteri jolly.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-141">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="e2ef5-142">L'associazione con caratteri jolly del sottodominio (ad esempio, `*.mysub.com`) non costituisce un rischio per la sicurezza se viene controllato l'intero dominio padre (a differenza di `*.com`, che è vulnerabile).</span><span class="sxs-lookup"><span data-stu-id="e2ef5-142">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="e2ef5-143">Vedere la [sezione 5.4 di RFC7230](https://tools.ietf.org/html/rfc7230#section-5.4) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-143">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="e2ef5-144">Confermare che l'identità del modello del processo disponga delle autorizzazioni appropriate.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-144">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="e2ef5-145">Se l'identità predefinita del pool di app ( **modello**  >  **Identity** di processo) viene modificata da `ApplicationPoolIdentity` a un'altra identità, verificare che la nuova identità disponga delle autorizzazioni necessarie per accedere alla cartella dell'app, al database e ad altre risorse richieste.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-145">If the default identity of the app pool ( **Process Model** > **Identity** ) is changed from `ApplicationPoolIdentity` to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="e2ef5-146">Ad esempio, il pool di applicazioni richiede l'accesso in lettura e scrittura alle cartelle in cui l'app legge e scrive i file.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-146">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

## <a name="create-an-aspnet-core-no-locrazor-pages-app"></a><span data-ttu-id="e2ef5-147">Creare un'app di ASP.NET Core Razor pages</span><span class="sxs-lookup"><span data-stu-id="e2ef5-147">Create an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="e2ef5-148">Seguire l' <xref:getting-started> esercitazione per creare un' Razor app pagine.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-148">Follow the <xref:getting-started> tutorial to create a Razor Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="e2ef5-149">Pubblicare e distribuire l'app</span><span class="sxs-lookup"><span data-stu-id="e2ef5-149">Publish and deploy the app</span></span>

<span data-ttu-id="e2ef5-150">*Pubblicare un'app* significa creare un'app compilata che può essere ospitata da un server.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-150">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="e2ef5-151">*Distribuire un'app* significa spostare l'app pubblicata in un sistema di hosting.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-151">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="e2ef5-152">Il passaggio di pubblicazione viene gestito da [.NET Core SDK](/dotnet/core/sdk), mentre la fase di distribuzione può essere gestita tramite vari approcci.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-152">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="e2ef5-153">Questa esercitazione adotta l'approccio di distribuzione tramite una *cartella* , in cui:</span><span class="sxs-lookup"><span data-stu-id="e2ef5-153">This tutorial adopts the *folder* deployment approach, where:</span></span>
 
* <span data-ttu-id="e2ef5-154">L'app viene pubblicata in una cartella.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-154">The app is published to a folder.</span></span>
* <span data-ttu-id="e2ef5-155">Il contenuto della cartella viene spostato nella cartella del sito IIS, ovvero il **percorso fisico** del sito in Gestione IIS.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-155">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e2ef5-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2ef5-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e2ef5-157">Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica** .</span><span class="sxs-lookup"><span data-stu-id="e2ef5-157">Right-click on the project in **Solution Explorer** and select **Publish** .</span></span>
1. <span data-ttu-id="e2ef5-158">Nella finestra di dialogo **Selezionare una destinazione di pubblicazione** selezionare l'opzione di pubblicazione **Cartella** .</span><span class="sxs-lookup"><span data-stu-id="e2ef5-158">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="e2ef5-159">Impostare il percorso **Cartella o condivisione file** .</span><span class="sxs-lookup"><span data-stu-id="e2ef5-159">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="e2ef5-160">Se è stata creata una cartella per il sito IIS disponibile nel computer di sviluppo come una condivisione di rete, specificare il percorso della condivisione.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-160">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="e2ef5-161">L'utente corrente deve avere l'accesso in scrittura per la pubblicazione nella condivisione.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-161">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="e2ef5-162">Se non si è in grado di eseguire la distribuzione direttamente nella cartella del sito IIS nel server IIS, pubblicare in una cartella su supporti rimovibili e spostare fisicamente l'app pubblicata nella cartella del sito IIS nel server, ovvero il **percorso fisico** del sito in Gestione IIS.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-162">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="e2ef5-163">Spostare il contenuto della `bin/Release/{TARGET FRAMEWORK}/publish` cartella nella cartella del sito IIS sul server, ovvero il **percorso fisico** del sito in Gestione IIS.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-163">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e2ef5-164">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="e2ef5-164">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="e2ef5-165">Da una shell dei comandi pubblicare l'app nella configurazione di versione con il comando [dotnet publish](/dotnet/core/tools/dotnet-publish):</span><span class="sxs-lookup"><span data-stu-id="e2ef5-165">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="e2ef5-166">Spostare il contenuto della `bin/Release/{TARGET FRAMEWORK}/publish` cartella nella cartella del sito IIS sul server, ovvero il **percorso fisico** del sito in Gestione IIS.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-166">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e2ef5-167">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="e2ef5-167">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="e2ef5-168">Fare clic con il pulsante destro del mouse sul progetto in **Soluzione** e scegliere **Pubblica** > **Pubblica nella cartella** .</span><span class="sxs-lookup"><span data-stu-id="e2ef5-168">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder** .</span></span>
1. <span data-ttu-id="e2ef5-169">Impostare il percorso in **Scegliere una cartella** .</span><span class="sxs-lookup"><span data-stu-id="e2ef5-169">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="e2ef5-170">Se è stata creata una cartella per il sito IIS disponibile nel computer di sviluppo come una condivisione di rete, specificare il percorso della condivisione.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-170">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="e2ef5-171">L'utente corrente deve avere l'accesso in scrittura per la pubblicazione nella condivisione.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-171">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="e2ef5-172">Se non è possibile eseguire la distribuzione direttamente nella cartella del sito IIS nel server IIS, pubblicare in una cartella su un supporto rimovibile e spostare fisicamente l'app pubblicata nella cartella del sito IIS nel server, ovvero il **percorso fisico** del sito in Gestione IIS.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-172">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="e2ef5-173">Spostare il contenuto della `bin/Release/{TARGET FRAMEWORK}/publish` cartella nella cartella del sito IIS sul server, ovvero il **percorso fisico** del sito in Gestione IIS.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-173">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="e2ef5-174">Esplorare il sito Web</span><span class="sxs-lookup"><span data-stu-id="e2ef5-174">Browse the website</span></span>

<span data-ttu-id="e2ef5-175">L'app è accessibile in un browser dopo la ricezione della prima richiesta.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-175">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="e2ef5-176">Inviare una richiesta all'app nell'associazione dell'endpoint che è stata stabilita in Gestione IIS per il sito.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-176">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e2ef5-177">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="e2ef5-177">Next steps</span></span>

<span data-ttu-id="e2ef5-178">In questa esercitazione sono state illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="e2ef5-178">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e2ef5-179">Installare il bundle di hosting di.NET Core in Windows Server.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-179">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="e2ef5-180">Creare un sito IIS in Gestione IIS.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-180">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="e2ef5-181">Distribuire un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e2ef5-181">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="e2ef5-182">Per altre informazioni sull'hosting di app ASP.NET Core in IIS, vedere la panoramica su IIS:</span><span class="sxs-lookup"><span data-stu-id="e2ef5-182">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="e2ef5-183">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="e2ef5-183">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="e2ef5-184">Articoli nel set di documentazione di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e2ef5-184">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="e2ef5-185">Articoli relativi alla distribuzione di app ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e2ef5-185">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="e2ef5-186">Pubblicare un'app Web in una cartella usando Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="e2ef5-186">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="e2ef5-187">Articoli sulla configurazione HTTPS di IIS</span><span class="sxs-lookup"><span data-stu-id="e2ef5-187">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="e2ef5-188">Configurazione di SSL in Gestione IIS</span><span class="sxs-lookup"><span data-stu-id="e2ef5-188">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="e2ef5-189">Come configurare SSL in IIS</span><span class="sxs-lookup"><span data-stu-id="e2ef5-189">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="e2ef5-190">Articoli su IIS e Windows Server</span><span class="sxs-lookup"><span data-stu-id="e2ef5-190">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="e2ef5-191">Il sito IIS ufficiale di Microsoft</span><span class="sxs-lookup"><span data-stu-id="e2ef5-191">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="e2ef5-192">Libreria di contenuti tecnici di Windows Server</span><span class="sxs-lookup"><span data-stu-id="e2ef5-192">Windows Server technical content library</span></span>](/windows-server/windows-server)

### <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="e2ef5-193">Risorse di distribuzione per amministratori IIS</span><span class="sxs-lookup"><span data-stu-id="e2ef5-193">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="e2ef5-194">Documentazione di ISS</span><span class="sxs-lookup"><span data-stu-id="e2ef5-194">IIS documentation</span></span>](/iis)
* <span data-ttu-id="e2ef5-195">[Getting Started with the IIS Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8) (Introduzione a Gestione IIS in IIS)</span><span class="sxs-lookup"><span data-stu-id="e2ef5-195">[Getting Started with the IIS Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)</span></span>
* [<span data-ttu-id="e2ef5-196">Distribuzione di applicazioni .NET Core</span><span class="sxs-lookup"><span data-stu-id="e2ef5-196">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

