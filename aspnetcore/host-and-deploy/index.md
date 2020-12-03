---
title: Hosting e distribuzione di ASP.NET Core
author: rick-anderson
description: Informazioni sulla configurazione degli ambienti host e sulla distribuzione delle app ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/index
ms.openlocfilehash: 4f3d4c29a189cf6aa14eb10f570f0b35d8ff9abc
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556619"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="0b304-103">Hosting e distribuzione di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0b304-103">Host and deploy ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="0b304-104">In generale, per distribuire un'app ASP.NET Core in un ambiente host:</span><span class="sxs-lookup"><span data-stu-id="0b304-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="0b304-105">Distribuire l'app pubblicata in una cartella nel server di hosting.</span><span class="sxs-lookup"><span data-stu-id="0b304-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="0b304-106">Configurare un gestore processi che avvia l'app quando arrivano richieste e la riavvia quando si blocca o quando il server viene riavviato.</span><span class="sxs-lookup"><span data-stu-id="0b304-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="0b304-107">Per la configurazione di un proxy inverso, configurare un proxy inverso per inoltrare le richieste all'app.</span><span class="sxs-lookup"><span data-stu-id="0b304-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="0b304-108">Pubblicare in una cartella</span><span class="sxs-lookup"><span data-stu-id="0b304-108">Publish to a folder</span></span>

<span data-ttu-id="0b304-109">Il comando [dotnet publish](/dotnet/core/tools/dotnet-publish) compila il codice dell'app e copia il file necessario per eseguire l'app in una cartella *publish*.</span><span class="sxs-lookup"><span data-stu-id="0b304-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="0b304-110">Quando si esegue la distribuzione da Visual Studio, il passaggio `dotnet publish` viene eseguito automaticamente prima della copia dei file nella destinazione di distribuzione.</span><span class="sxs-lookup"><span data-stu-id="0b304-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

## <a name="publish-settings-files"></a><span data-ttu-id="0b304-111">File di impostazioni di pubblicazione</span><span class="sxs-lookup"><span data-stu-id="0b304-111">Publish settings files</span></span>

<span data-ttu-id="0b304-112">`*.json` per impostazione predefinita, i file vengono pubblicati.</span><span class="sxs-lookup"><span data-stu-id="0b304-112">`*.json` files are published by default.</span></span> <span data-ttu-id="0b304-113">Per pubblicare altri file di impostazioni, specificarli in un [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) elemento nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="0b304-113">To publish other settings files, specify them in an [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) element in the project file.</span></span> <span data-ttu-id="0b304-114">Nell'esempio seguente vengono pubblicati i file XML:</span><span class="sxs-lookup"><span data-stu-id="0b304-114">The following example publishes XML files:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.xml" Exclude="bin\**\*;obj\**\*"
    CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

### <a name="folder-contents"></a><span data-ttu-id="0b304-115">Contenuto cartelle</span><span class="sxs-lookup"><span data-stu-id="0b304-115">Folder contents</span></span>

<span data-ttu-id="0b304-116">La cartella *publish* contiene uno o più file di assembly di app, le dipendenze e facoltativamente il runtime di .NET.</span><span class="sxs-lookup"><span data-stu-id="0b304-116">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="0b304-117">È possibile pubblicare un'app .NET Core come *distribuzione indipendente* o come *distribuzione dipendente dal framework*.</span><span class="sxs-lookup"><span data-stu-id="0b304-117">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="0b304-118">Se l'app è indipendente, i file di assembly che contengono il runtime .NET sono inclusi nella cartella *publish*.</span><span class="sxs-lookup"><span data-stu-id="0b304-118">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="0b304-119">Se l'app è dipendente dal framework, i file di runtime .NET non sono inclusi, perché l'app contiene un riferimento a una versione di .NET installata nel server.</span><span class="sxs-lookup"><span data-stu-id="0b304-119">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="0b304-120">Il modello di distribuzione predefinito è il modello dipendente dal framework.</span><span class="sxs-lookup"><span data-stu-id="0b304-120">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="0b304-121">Per altre informazioni, vedere [Distribuzione di applicazioni .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="0b304-121">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="0b304-122">Oltre ai file con estensione *EXE* e *DLL* la cartella *publish* di un'app ASP.NET Core contiene in genere i file di configurazione, gli asset statici e le visualizzazioni MVC.</span><span class="sxs-lookup"><span data-stu-id="0b304-122">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="0b304-123">Per altre informazioni, vedere <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="0b304-123">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="0b304-124">Configurare un gestore processi</span><span class="sxs-lookup"><span data-stu-id="0b304-124">Set up a process manager</span></span>

<span data-ttu-id="0b304-125">Un'app ASP.NET Core è un'app console che deve essere avviata all'avvio di un server e riavviata in caso di arresto anomalo del server stesso.</span><span class="sxs-lookup"><span data-stu-id="0b304-125">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="0b304-126">Per automatizzare le operazioni di avvio e riavvio, deve essere presente un gestore processi.</span><span class="sxs-lookup"><span data-stu-id="0b304-126">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="0b304-127">I gestori processi più comuni per ASP.NET Core sono:</span><span class="sxs-lookup"><span data-stu-id="0b304-127">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="0b304-128">Linux</span><span class="sxs-lookup"><span data-stu-id="0b304-128">Linux</span></span>
  * [<span data-ttu-id="0b304-129">Nginx</span><span class="sxs-lookup"><span data-stu-id="0b304-129">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="0b304-130">Apache</span><span class="sxs-lookup"><span data-stu-id="0b304-130">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="0b304-131">Windows</span><span class="sxs-lookup"><span data-stu-id="0b304-131">Windows</span></span>
  * [<span data-ttu-id="0b304-132">IIS</span><span class="sxs-lookup"><span data-stu-id="0b304-132">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="0b304-133">Servizio Windows</span><span class="sxs-lookup"><span data-stu-id="0b304-133">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="0b304-134">Configurare un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="0b304-134">Set up a reverse proxy</span></span>

<span data-ttu-id="0b304-135">Se l'app usa il server [Kestrel](xref:fundamentals/servers/kestrel) è possibile usare come server proxy inverso [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) o [IIS](xref:host-and-deploy/iis/index).</span><span class="sxs-lookup"><span data-stu-id="0b304-135">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="0b304-136">Il server proxy inverso riceve le richieste HTTP da Internet e le inoltra a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0b304-136">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="0b304-137">Entrambe le configurazioni &mdash;con o senza un server proxy inverso&mdash; sono configurazioni di hosting supportate.</span><span class="sxs-lookup"><span data-stu-id="0b304-137">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="0b304-138">Per altre informazioni, vedere [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy) (Quando usare Kestrel con un proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="0b304-138">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="0b304-139">Scenari con server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="0b304-139">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="0b304-140">Potrebbero essere necessari interventi di configurazione aggiuntivi per le app ospitate dietro a server proxy e a servizi di bilanciamento del carico.</span><span class="sxs-lookup"><span data-stu-id="0b304-140">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="0b304-141">Senza alcuna configurazione aggiuntiva, un'app potrebbe non avere accesso allo schema (HTTP/HTTPS) e all'indirizzo IP remoto di origine di una richiesta.</span><span class="sxs-lookup"><span data-stu-id="0b304-141">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="0b304-142">Per altre informazioni, vedere [Configurare ASP.NET Core per l'utilizzo di server proxy e servizi di bilanciamento del carico](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="0b304-142">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="0b304-143">Usare Visual Studio e MSBuild per automatizzare le distribuzioni</span><span class="sxs-lookup"><span data-stu-id="0b304-143">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="0b304-144">In molti casi la distribuzione richiede attività aggiuntive oltre alla copia dell'output da [dotnet publish](/dotnet/core/tools/dotnet-publish) a un server.</span><span class="sxs-lookup"><span data-stu-id="0b304-144">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="0b304-145">Ad esempio è possibile che nella cartella *publish* siano necessari file aggiuntivi o vengano esclusi uno o più file.</span><span class="sxs-lookup"><span data-stu-id="0b304-145">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="0b304-146">Visual Studio USA [MSBuild](/visualstudio/msbuild/msbuild) per la distribuzione Web e MSBuild può essere personalizzato per eseguire molte altre attività durante la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="0b304-146">Visual Studio uses [MSBuild](/visualstudio/msbuild/msbuild) for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="0b304-147">Per altre informazioni, vedere <xref:host-and-deploy/visual-studio-publish-profiles> e il libro [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) (Uso di MSBuild e Team Foundation Build).</span><span class="sxs-lookup"><span data-stu-id="0b304-147">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="0b304-148">Mediante la [funzionalità Pubblica sito Web](xref:tutorials/publish-to-azure-webapp-using-vs) o il [supporto di Git incorporato](xref:host-and-deploy/azure-apps/azure-continuous-deployment) è possibile eseguire direttamente la distribuzione di app da Visual Studio al Servizio app di Azure.</span><span class="sxs-lookup"><span data-stu-id="0b304-148">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="0b304-149">Azure DevOps Services supporta la [distribuzione continua al Servizio app di Azure](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="0b304-149">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="0b304-150">Per altre informazioni, vedere [DevOps con ASP.NET Core e Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="0b304-150">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="0b304-151">Pubblicazione in Azure</span><span class="sxs-lookup"><span data-stu-id="0b304-151">Publish to Azure</span></span>

<span data-ttu-id="0b304-152">Vedere <xref:tutorials/publish-to-azure-webapp-using-vs> per istruzioni su come pubblicare un'app in Azure usando Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0b304-152">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="0b304-153">Un altro esempio è disponibile in [Creare un'app Web ASP.NET Core in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="0b304-153">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="0b304-154">Pubblicare con MSDeploy in Windows</span><span class="sxs-lookup"><span data-stu-id="0b304-154">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="0b304-155">Vedere <xref:host-and-deploy/visual-studio-publish-profiles> per istruzioni su come pubblicare un'app con un profilo di pubblicazione di Visual Studio, ad esempio da un prompt dei comandi di Windows usando il comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild).</span><span class="sxs-lookup"><span data-stu-id="0b304-155">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="0b304-156">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="0b304-156">Internet Information Services (IIS)</span></span>

<span data-ttu-id="0b304-157">Per le distribuzioni in Internet Information Services (IIS) con la configurazione fornita dal file *web.config* , vedere gli articoli in <xref:host-and-deploy/iis/index> .</span><span class="sxs-lookup"><span data-stu-id="0b304-157">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="0b304-158">Hosting in una Web farm</span><span class="sxs-lookup"><span data-stu-id="0b304-158">Host in a web farm</span></span>

<span data-ttu-id="0b304-159">Per informazioni sulla configurazione per ospitare app ASP.NET Core in un ambiente Web farm (ad esempio, distribuzione di più istanze di un'app per la scalabilità), vedere <xref:host-and-deploy/web-farm>.</span><span class="sxs-lookup"><span data-stu-id="0b304-159">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="0b304-160">Host in Docker</span><span class="sxs-lookup"><span data-stu-id="0b304-160">Host on Docker</span></span>

<span data-ttu-id="0b304-161">Per altre informazioni, vedere <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="0b304-161">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="perform-health-checks"></a><span data-ttu-id="0b304-162">Eseguire controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="0b304-162">Perform health checks</span></span>

<span data-ttu-id="0b304-163">Usare il middleware di controllo integrità per eseguire controlli di integrità su un'app e le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="0b304-163">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="0b304-164">Per altre informazioni, vedere <xref:host-and-deploy/health-checks>.</span><span class="sxs-lookup"><span data-stu-id="0b304-164">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0b304-165">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="0b304-165">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="0b304-166">Hosting di ASP.NET</span><span class="sxs-lookup"><span data-stu-id="0b304-166">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="0b304-167">In generale, per distribuire un'app ASP.NET Core in un ambiente host:</span><span class="sxs-lookup"><span data-stu-id="0b304-167">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="0b304-168">Distribuire l'app pubblicata in una cartella nel server di hosting.</span><span class="sxs-lookup"><span data-stu-id="0b304-168">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="0b304-169">Configurare un gestore processi che avvia l'app quando arrivano richieste e la riavvia quando si blocca o quando il server viene riavviato.</span><span class="sxs-lookup"><span data-stu-id="0b304-169">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="0b304-170">Per la configurazione di un proxy inverso, configurare un proxy inverso per inoltrare le richieste all'app.</span><span class="sxs-lookup"><span data-stu-id="0b304-170">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="0b304-171">Pubblicare in una cartella</span><span class="sxs-lookup"><span data-stu-id="0b304-171">Publish to a folder</span></span>

<span data-ttu-id="0b304-172">Il comando [dotnet publish](/dotnet/core/tools/dotnet-publish) compila il codice dell'app e copia il file necessario per eseguire l'app in una cartella *publish*.</span><span class="sxs-lookup"><span data-stu-id="0b304-172">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="0b304-173">Quando si esegue la distribuzione da Visual Studio, il passaggio `dotnet publish` viene eseguito automaticamente prima della copia dei file nella destinazione di distribuzione.</span><span class="sxs-lookup"><span data-stu-id="0b304-173">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="0b304-174">Contenuto cartelle</span><span class="sxs-lookup"><span data-stu-id="0b304-174">Folder contents</span></span>

<span data-ttu-id="0b304-175">La cartella *publish* contiene uno o più file di assembly di app, le dipendenze e facoltativamente il runtime di .NET.</span><span class="sxs-lookup"><span data-stu-id="0b304-175">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="0b304-176">È possibile pubblicare un'app .NET Core come *distribuzione indipendente* o come *distribuzione dipendente dal framework*.</span><span class="sxs-lookup"><span data-stu-id="0b304-176">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="0b304-177">Se l'app è indipendente, i file di assembly che contengono il runtime .NET sono inclusi nella cartella *publish*.</span><span class="sxs-lookup"><span data-stu-id="0b304-177">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="0b304-178">Se l'app è dipendente dal framework, i file di runtime .NET non sono inclusi, perché l'app contiene un riferimento a una versione di .NET installata nel server.</span><span class="sxs-lookup"><span data-stu-id="0b304-178">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="0b304-179">Il modello di distribuzione predefinito è il modello dipendente dal framework.</span><span class="sxs-lookup"><span data-stu-id="0b304-179">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="0b304-180">Per altre informazioni, vedere [Distribuzione di applicazioni .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="0b304-180">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="0b304-181">Oltre ai file con estensione *EXE* e *DLL* la cartella *publish* di un'app ASP.NET Core contiene in genere i file di configurazione, gli asset statici e le visualizzazioni MVC.</span><span class="sxs-lookup"><span data-stu-id="0b304-181">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="0b304-182">Per altre informazioni, vedere <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="0b304-182">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="0b304-183">Configurare un gestore processi</span><span class="sxs-lookup"><span data-stu-id="0b304-183">Set up a process manager</span></span>

<span data-ttu-id="0b304-184">Un'app ASP.NET Core è un'app console che deve essere avviata all'avvio di un server e riavviata in caso di arresto anomalo del server stesso.</span><span class="sxs-lookup"><span data-stu-id="0b304-184">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="0b304-185">Per automatizzare le operazioni di avvio e riavvio, deve essere presente un gestore processi.</span><span class="sxs-lookup"><span data-stu-id="0b304-185">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="0b304-186">I gestori processi più comuni per ASP.NET Core sono:</span><span class="sxs-lookup"><span data-stu-id="0b304-186">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="0b304-187">Linux</span><span class="sxs-lookup"><span data-stu-id="0b304-187">Linux</span></span>
  * [<span data-ttu-id="0b304-188">Nginx</span><span class="sxs-lookup"><span data-stu-id="0b304-188">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="0b304-189">Apache</span><span class="sxs-lookup"><span data-stu-id="0b304-189">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="0b304-190">Windows</span><span class="sxs-lookup"><span data-stu-id="0b304-190">Windows</span></span>
  * [<span data-ttu-id="0b304-191">IIS</span><span class="sxs-lookup"><span data-stu-id="0b304-191">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="0b304-192">Servizio Windows</span><span class="sxs-lookup"><span data-stu-id="0b304-192">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="0b304-193">Configurare un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="0b304-193">Set up a reverse proxy</span></span>

<span data-ttu-id="0b304-194">Se l'app usa il server [Kestrel](xref:fundamentals/servers/kestrel) è possibile usare come server proxy inverso [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) o [IIS](xref:host-and-deploy/iis/index).</span><span class="sxs-lookup"><span data-stu-id="0b304-194">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="0b304-195">Il server proxy inverso riceve le richieste HTTP da Internet e le inoltra a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0b304-195">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="0b304-196">Entrambe le configurazioni &mdash;con o senza un server proxy inverso&mdash; sono configurazioni di hosting supportate.</span><span class="sxs-lookup"><span data-stu-id="0b304-196">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="0b304-197">Per altre informazioni, vedere [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy) (Quando usare Kestrel con un proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="0b304-197">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="0b304-198">Scenari con server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="0b304-198">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="0b304-199">Potrebbero essere necessari interventi di configurazione aggiuntivi per le app ospitate dietro a server proxy e a servizi di bilanciamento del carico.</span><span class="sxs-lookup"><span data-stu-id="0b304-199">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="0b304-200">Senza alcuna configurazione aggiuntiva, un'app potrebbe non avere accesso allo schema (HTTP/HTTPS) e all'indirizzo IP remoto di origine di una richiesta.</span><span class="sxs-lookup"><span data-stu-id="0b304-200">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="0b304-201">Per altre informazioni, vedere [Configurare ASP.NET Core per l'utilizzo di server proxy e servizi di bilanciamento del carico](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="0b304-201">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="0b304-202">Usare Visual Studio e MSBuild per automatizzare le distribuzioni</span><span class="sxs-lookup"><span data-stu-id="0b304-202">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="0b304-203">In molti casi la distribuzione richiede attività aggiuntive oltre alla copia dell'output da [dotnet publish](/dotnet/core/tools/dotnet-publish) a un server.</span><span class="sxs-lookup"><span data-stu-id="0b304-203">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="0b304-204">Ad esempio è possibile che nella cartella *publish* siano necessari file aggiuntivi o vengano esclusi uno o più file.</span><span class="sxs-lookup"><span data-stu-id="0b304-204">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="0b304-205">Per la distribuzione Web, Visual Studio usa MSBuild, che può essere personalizzato per eseguire molte altre attività durante la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="0b304-205">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="0b304-206">Per altre informazioni, vedere <xref:host-and-deploy/visual-studio-publish-profiles> e il libro [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) (Uso di MSBuild e Team Foundation Build).</span><span class="sxs-lookup"><span data-stu-id="0b304-206">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="0b304-207">Mediante la [funzionalità Pubblica sito Web](xref:tutorials/publish-to-azure-webapp-using-vs) o il [supporto di Git incorporato](xref:host-and-deploy/azure-apps/azure-continuous-deployment) è possibile eseguire direttamente la distribuzione di app da Visual Studio al Servizio app di Azure.</span><span class="sxs-lookup"><span data-stu-id="0b304-207">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="0b304-208">Azure DevOps Services supporta la [distribuzione continua al Servizio app di Azure](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="0b304-208">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="0b304-209">Per altre informazioni, vedere [DevOps con ASP.NET Core e Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="0b304-209">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="0b304-210">Pubblicazione in Azure</span><span class="sxs-lookup"><span data-stu-id="0b304-210">Publish to Azure</span></span>

<span data-ttu-id="0b304-211">Vedere <xref:tutorials/publish-to-azure-webapp-using-vs> per istruzioni su come pubblicare un'app in Azure usando Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0b304-211">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="0b304-212">Un altro esempio è disponibile in [Creare un'app Web ASP.NET Core in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="0b304-212">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="0b304-213">Pubblicare con MSDeploy in Windows</span><span class="sxs-lookup"><span data-stu-id="0b304-213">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="0b304-214">Vedere <xref:host-and-deploy/visual-studio-publish-profiles> per istruzioni su come pubblicare un'app con un profilo di pubblicazione di Visual Studio, ad esempio da un prompt dei comandi di Windows usando il comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild).</span><span class="sxs-lookup"><span data-stu-id="0b304-214">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="0b304-215">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="0b304-215">Internet Information Services (IIS)</span></span>

<span data-ttu-id="0b304-216">Per le distribuzioni in Internet Information Services (IIS) con la configurazione fornita dal file *web.config* , vedere gli articoli in <xref:host-and-deploy/iis/index> .</span><span class="sxs-lookup"><span data-stu-id="0b304-216">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="0b304-217">Hosting in una Web farm</span><span class="sxs-lookup"><span data-stu-id="0b304-217">Host in a web farm</span></span>

<span data-ttu-id="0b304-218">Per informazioni sulla configurazione per ospitare app ASP.NET Core in un ambiente Web farm (ad esempio, distribuzione di più istanze di un'app per la scalabilità), vedere <xref:host-and-deploy/web-farm>.</span><span class="sxs-lookup"><span data-stu-id="0b304-218">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="0b304-219">Host in Docker</span><span class="sxs-lookup"><span data-stu-id="0b304-219">Host on Docker</span></span>

<span data-ttu-id="0b304-220">Per altre informazioni, vedere <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="0b304-220">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0b304-221">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="0b304-221">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="0b304-222">Hosting di ASP.NET</span><span class="sxs-lookup"><span data-stu-id="0b304-222">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
