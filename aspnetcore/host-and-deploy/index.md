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
ms.openlocfilehash: 05d04a9c95910c805ea28578aba21a0658dd779a
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252968"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="2d9bb-103">Hosting e distribuzione di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2d9bb-103">Host and deploy ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="2d9bb-104">In generale, per distribuire un'app ASP.NET Core in un ambiente host:</span><span class="sxs-lookup"><span data-stu-id="2d9bb-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="2d9bb-105">Distribuire l'app pubblicata in una cartella nel server di hosting.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="2d9bb-106">Configurare un gestore processi che avvia l'app quando arrivano richieste e la riavvia quando si blocca o quando il server viene riavviato.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="2d9bb-107">Per la configurazione di un proxy inverso, configurare un proxy inverso per inoltrare le richieste all'app.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="2d9bb-108">Pubblicare in una cartella</span><span class="sxs-lookup"><span data-stu-id="2d9bb-108">Publish to a folder</span></span>

<span data-ttu-id="2d9bb-109">Il comando [dotnet publish](/dotnet/core/tools/dotnet-publish) compila il codice dell'app e copia il file necessario per eseguire l'app in una cartella *publish*.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="2d9bb-110">Quando si esegue la distribuzione da Visual Studio, il passaggio `dotnet publish` viene eseguito automaticamente prima della copia dei file nella destinazione di distribuzione.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

## <a name="publish-settings-files"></a><span data-ttu-id="2d9bb-111">File di impostazioni di pubblicazione</span><span class="sxs-lookup"><span data-stu-id="2d9bb-111">Publish settings files</span></span>

<span data-ttu-id="2d9bb-112">`*.json` per impostazione predefinita, i file vengono pubblicati.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-112">`*.json` files are published by default.</span></span> <span data-ttu-id="2d9bb-113">Per pubblicare altri file di impostazioni, specificarli in un [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) elemento nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-113">To publish other settings files, specify them in an [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) element in the project file.</span></span> <span data-ttu-id="2d9bb-114">Nell'esempio seguente vengono pubblicati i file XML:</span><span class="sxs-lookup"><span data-stu-id="2d9bb-114">The following example publishes XML files:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.xml" Exclude="bin\**\*;obj\**\*"
    CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

### <a name="folder-contents"></a><span data-ttu-id="2d9bb-115">Contenuto cartelle</span><span class="sxs-lookup"><span data-stu-id="2d9bb-115">Folder contents</span></span>

<span data-ttu-id="2d9bb-116">La cartella *publish* contiene uno o più file di assembly di app, le dipendenze e facoltativamente il runtime di .NET.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-116">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="2d9bb-117">È possibile pubblicare un'app .NET Core come *distribuzione indipendente* o come *distribuzione dipendente dal framework*.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-117">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="2d9bb-118">Se l'app è indipendente, i file di assembly che contengono il runtime .NET sono inclusi nella cartella *publish*.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-118">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="2d9bb-119">Se l'app è dipendente dal framework, i file di runtime .NET non sono inclusi, perché l'app contiene un riferimento a una versione di .NET installata nel server.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-119">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="2d9bb-120">Il modello di distribuzione predefinito è il modello dipendente dal framework.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-120">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="2d9bb-121">Per altre informazioni, vedere [Distribuzione di applicazioni .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="2d9bb-121">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="2d9bb-122">Oltre ai file con estensione *EXE* e *DLL* la cartella *publish* di un'app ASP.NET Core contiene in genere i file di configurazione, gli asset statici e le visualizzazioni MVC.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-122">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="2d9bb-123">Per altre informazioni, vedere <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-123">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="2d9bb-124">Configurare un gestore processi</span><span class="sxs-lookup"><span data-stu-id="2d9bb-124">Set up a process manager</span></span>

<span data-ttu-id="2d9bb-125">Un'app ASP.NET Core è un'app console che deve essere avviata all'avvio di un server e riavviata in caso di arresto anomalo del server stesso.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-125">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="2d9bb-126">Per automatizzare le operazioni di avvio e riavvio, deve essere presente un gestore processi.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-126">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="2d9bb-127">I gestori processi più comuni per ASP.NET Core sono:</span><span class="sxs-lookup"><span data-stu-id="2d9bb-127">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="2d9bb-128">Linux</span><span class="sxs-lookup"><span data-stu-id="2d9bb-128">Linux</span></span>
  * [<span data-ttu-id="2d9bb-129">Nginx</span><span class="sxs-lookup"><span data-stu-id="2d9bb-129">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="2d9bb-130">Apache</span><span class="sxs-lookup"><span data-stu-id="2d9bb-130">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="2d9bb-131">Windows</span><span class="sxs-lookup"><span data-stu-id="2d9bb-131">Windows</span></span>
  * [<span data-ttu-id="2d9bb-132">IIS</span><span class="sxs-lookup"><span data-stu-id="2d9bb-132">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="2d9bb-133">Servizio Windows</span><span class="sxs-lookup"><span data-stu-id="2d9bb-133">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="2d9bb-134">Configurare un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="2d9bb-134">Set up a reverse proxy</span></span>

<span data-ttu-id="2d9bb-135">Se l'app usa il server [Kestrel](xref:fundamentals/servers/kestrel) è possibile usare come server proxy inverso [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) o [IIS](xref:host-and-deploy/iis/index).</span><span class="sxs-lookup"><span data-stu-id="2d9bb-135">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="2d9bb-136">Il server proxy inverso riceve le richieste HTTP da Internet e le inoltra a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-136">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

::: moniker-end 

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="2d9bb-137">Entrambe le configurazioni &mdash;con o senza un server proxy inverso&mdash; sono configurazioni di hosting supportate.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-137">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="2d9bb-138">Per altre informazioni, vedere [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy) (Quando usare Kestrel con un proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="2d9bb-138">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy).</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"
<span data-ttu-id="2d9bb-139">Entrambe le configurazioni &mdash;con o senza un server proxy inverso&mdash; sono configurazioni di hosting supportate.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-139">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="2d9bb-140">Per altre informazioni, vedere [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy) (Quando usare Kestrel con un proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="2d9bb-140">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="2d9bb-141">Scenari con server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="2d9bb-141">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="2d9bb-142">Potrebbero essere necessari interventi di configurazione aggiuntivi per le app ospitate dietro a server proxy e a servizi di bilanciamento del carico.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-142">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="2d9bb-143">Senza alcuna configurazione aggiuntiva, un'app potrebbe non avere accesso allo schema (HTTP/HTTPS) e all'indirizzo IP remoto di origine di una richiesta.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-143">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="2d9bb-144">Per altre informazioni, vedere [Configurare ASP.NET Core per l'utilizzo di server proxy e servizi di bilanciamento del carico](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="2d9bb-144">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="2d9bb-145">Usare Visual Studio e MSBuild per automatizzare le distribuzioni</span><span class="sxs-lookup"><span data-stu-id="2d9bb-145">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="2d9bb-146">In molti casi la distribuzione richiede attività aggiuntive oltre alla copia dell'output da [dotnet publish](/dotnet/core/tools/dotnet-publish) a un server.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-146">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="2d9bb-147">Ad esempio è possibile che nella cartella *publish* siano necessari file aggiuntivi o vengano esclusi uno o più file.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-147">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="2d9bb-148">Visual Studio USA [MSBuild](/visualstudio/msbuild/msbuild) per la distribuzione Web e MSBuild può essere personalizzato per eseguire molte altre attività durante la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-148">Visual Studio uses [MSBuild](/visualstudio/msbuild/msbuild) for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="2d9bb-149">Per altre informazioni, vedere <xref:host-and-deploy/visual-studio-publish-profiles> e il libro [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) (Uso di MSBuild e Team Foundation Build).</span><span class="sxs-lookup"><span data-stu-id="2d9bb-149">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="2d9bb-150">Mediante la [funzionalità Pubblica sito Web](xref:tutorials/publish-to-azure-webapp-using-vs) o il [supporto di Git incorporato](xref:host-and-deploy/azure-apps/azure-continuous-deployment) è possibile eseguire direttamente la distribuzione di app da Visual Studio al Servizio app di Azure.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-150">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="2d9bb-151">Azure DevOps Services supporta la [distribuzione continua al Servizio app di Azure](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="2d9bb-151">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="2d9bb-152">Per altre informazioni, vedere [DevOps con ASP.NET Core e Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="2d9bb-152">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="2d9bb-153">Pubblicazione in Azure</span><span class="sxs-lookup"><span data-stu-id="2d9bb-153">Publish to Azure</span></span>

<span data-ttu-id="2d9bb-154">Vedere <xref:tutorials/publish-to-azure-webapp-using-vs> per istruzioni su come pubblicare un'app in Azure usando Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-154">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="2d9bb-155">Un altro esempio è disponibile in [Creare un'app Web ASP.NET Core in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="2d9bb-155">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="2d9bb-156">Pubblicare con MSDeploy in Windows</span><span class="sxs-lookup"><span data-stu-id="2d9bb-156">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="2d9bb-157">Vedere <xref:host-and-deploy/visual-studio-publish-profiles> per istruzioni su come pubblicare un'app con un profilo di pubblicazione di Visual Studio, ad esempio da un prompt dei comandi di Windows usando il comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild).</span><span class="sxs-lookup"><span data-stu-id="2d9bb-157">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="2d9bb-158">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="2d9bb-158">Internet Information Services (IIS)</span></span>

<span data-ttu-id="2d9bb-159">Per le distribuzioni in Internet Information Services (IIS) con la configurazione fornita dal file *web.config* , vedere gli articoli in <xref:host-and-deploy/iis/index> .</span><span class="sxs-lookup"><span data-stu-id="2d9bb-159">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="2d9bb-160">Hosting in una Web farm</span><span class="sxs-lookup"><span data-stu-id="2d9bb-160">Host in a web farm</span></span>

<span data-ttu-id="2d9bb-161">Per informazioni sulla configurazione per ospitare app ASP.NET Core in un ambiente Web farm (ad esempio, distribuzione di più istanze di un'app per la scalabilità), vedere <xref:host-and-deploy/web-farm>.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-161">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="2d9bb-162">Host in Docker</span><span class="sxs-lookup"><span data-stu-id="2d9bb-162">Host on Docker</span></span>

<span data-ttu-id="2d9bb-163">Per altre informazioni, vedere <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-163">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="perform-health-checks"></a><span data-ttu-id="2d9bb-164">Eseguire controlli di integrità</span><span class="sxs-lookup"><span data-stu-id="2d9bb-164">Perform health checks</span></span>

<span data-ttu-id="2d9bb-165">Usare il middleware di controllo integrità per eseguire controlli di integrità su un'app e le relative dipendenze.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-165">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="2d9bb-166">Per altre informazioni, vedere <xref:host-and-deploy/health-checks>.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-166">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2d9bb-167">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2d9bb-167">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="2d9bb-168">Hosting di ASP.NET</span><span class="sxs-lookup"><span data-stu-id="2d9bb-168">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="2d9bb-169">In generale, per distribuire un'app ASP.NET Core in un ambiente host:</span><span class="sxs-lookup"><span data-stu-id="2d9bb-169">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="2d9bb-170">Distribuire l'app pubblicata in una cartella nel server di hosting.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-170">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="2d9bb-171">Configurare un gestore processi che avvia l'app quando arrivano richieste e la riavvia quando si blocca o quando il server viene riavviato.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-171">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="2d9bb-172">Per la configurazione di un proxy inverso, configurare un proxy inverso per inoltrare le richieste all'app.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-172">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="2d9bb-173">Pubblicare in una cartella</span><span class="sxs-lookup"><span data-stu-id="2d9bb-173">Publish to a folder</span></span>

<span data-ttu-id="2d9bb-174">Il comando [dotnet publish](/dotnet/core/tools/dotnet-publish) compila il codice dell'app e copia il file necessario per eseguire l'app in una cartella *publish*.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-174">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="2d9bb-175">Quando si esegue la distribuzione da Visual Studio, il passaggio `dotnet publish` viene eseguito automaticamente prima della copia dei file nella destinazione di distribuzione.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-175">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="2d9bb-176">Contenuto cartelle</span><span class="sxs-lookup"><span data-stu-id="2d9bb-176">Folder contents</span></span>

<span data-ttu-id="2d9bb-177">La cartella *publish* contiene uno o più file di assembly di app, le dipendenze e facoltativamente il runtime di .NET.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-177">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="2d9bb-178">È possibile pubblicare un'app .NET Core come *distribuzione indipendente* o come *distribuzione dipendente dal framework*.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-178">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="2d9bb-179">Se l'app è indipendente, i file di assembly che contengono il runtime .NET sono inclusi nella cartella *publish*.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-179">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="2d9bb-180">Se l'app è dipendente dal framework, i file di runtime .NET non sono inclusi, perché l'app contiene un riferimento a una versione di .NET installata nel server.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-180">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="2d9bb-181">Il modello di distribuzione predefinito è il modello dipendente dal framework.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-181">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="2d9bb-182">Per altre informazioni, vedere [Distribuzione di applicazioni .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="2d9bb-182">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="2d9bb-183">Oltre ai file con estensione *EXE* e *DLL* la cartella *publish* di un'app ASP.NET Core contiene in genere i file di configurazione, gli asset statici e le visualizzazioni MVC.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-183">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="2d9bb-184">Per altre informazioni, vedere <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-184">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="2d9bb-185">Configurare un gestore processi</span><span class="sxs-lookup"><span data-stu-id="2d9bb-185">Set up a process manager</span></span>

<span data-ttu-id="2d9bb-186">Un'app ASP.NET Core è un'app console che deve essere avviata all'avvio di un server e riavviata in caso di arresto anomalo del server stesso.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-186">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="2d9bb-187">Per automatizzare le operazioni di avvio e riavvio, deve essere presente un gestore processi.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-187">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="2d9bb-188">I gestori processi più comuni per ASP.NET Core sono:</span><span class="sxs-lookup"><span data-stu-id="2d9bb-188">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="2d9bb-189">Linux</span><span class="sxs-lookup"><span data-stu-id="2d9bb-189">Linux</span></span>
  * [<span data-ttu-id="2d9bb-190">Nginx</span><span class="sxs-lookup"><span data-stu-id="2d9bb-190">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="2d9bb-191">Apache</span><span class="sxs-lookup"><span data-stu-id="2d9bb-191">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="2d9bb-192">Windows</span><span class="sxs-lookup"><span data-stu-id="2d9bb-192">Windows</span></span>
  * [<span data-ttu-id="2d9bb-193">IIS</span><span class="sxs-lookup"><span data-stu-id="2d9bb-193">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="2d9bb-194">Servizio Windows</span><span class="sxs-lookup"><span data-stu-id="2d9bb-194">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="2d9bb-195">Configurare un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="2d9bb-195">Set up a reverse proxy</span></span>

<span data-ttu-id="2d9bb-196">Se l'app usa il server [Kestrel](xref:fundamentals/servers/kestrel) è possibile usare come server proxy inverso [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) o [IIS](xref:host-and-deploy/iis/index).</span><span class="sxs-lookup"><span data-stu-id="2d9bb-196">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="2d9bb-197">Il server proxy inverso riceve le richieste HTTP da Internet e le inoltra a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-197">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="2d9bb-198">Entrambe le configurazioni &mdash;con o senza un server proxy inverso&mdash; sono configurazioni di hosting supportate.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-198">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="2d9bb-199">Per altre informazioni, vedere [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy) (Quando usare Kestrel con un proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="2d9bb-199">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="2d9bb-200">Scenari con server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="2d9bb-200">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="2d9bb-201">Potrebbero essere necessari interventi di configurazione aggiuntivi per le app ospitate dietro a server proxy e a servizi di bilanciamento del carico.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-201">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="2d9bb-202">Senza alcuna configurazione aggiuntiva, un'app potrebbe non avere accesso allo schema (HTTP/HTTPS) e all'indirizzo IP remoto di origine di una richiesta.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-202">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="2d9bb-203">Per altre informazioni, vedere [Configurare ASP.NET Core per l'utilizzo di server proxy e servizi di bilanciamento del carico](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="2d9bb-203">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="2d9bb-204">Usare Visual Studio e MSBuild per automatizzare le distribuzioni</span><span class="sxs-lookup"><span data-stu-id="2d9bb-204">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="2d9bb-205">In molti casi la distribuzione richiede attività aggiuntive oltre alla copia dell'output da [dotnet publish](/dotnet/core/tools/dotnet-publish) a un server.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-205">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="2d9bb-206">Ad esempio è possibile che nella cartella *publish* siano necessari file aggiuntivi o vengano esclusi uno o più file.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-206">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="2d9bb-207">Per la distribuzione Web, Visual Studio usa MSBuild, che può essere personalizzato per eseguire molte altre attività durante la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-207">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="2d9bb-208">Per altre informazioni, vedere <xref:host-and-deploy/visual-studio-publish-profiles> e il libro [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) (Uso di MSBuild e Team Foundation Build).</span><span class="sxs-lookup"><span data-stu-id="2d9bb-208">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="2d9bb-209">Mediante la [funzionalità Pubblica sito Web](xref:tutorials/publish-to-azure-webapp-using-vs) o il [supporto di Git incorporato](xref:host-and-deploy/azure-apps/azure-continuous-deployment) è possibile eseguire direttamente la distribuzione di app da Visual Studio al Servizio app di Azure.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-209">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="2d9bb-210">Azure DevOps Services supporta la [distribuzione continua al Servizio app di Azure](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="2d9bb-210">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="2d9bb-211">Per altre informazioni, vedere [DevOps con ASP.NET Core e Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="2d9bb-211">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="2d9bb-212">Pubblicazione in Azure</span><span class="sxs-lookup"><span data-stu-id="2d9bb-212">Publish to Azure</span></span>

<span data-ttu-id="2d9bb-213">Vedere <xref:tutorials/publish-to-azure-webapp-using-vs> per istruzioni su come pubblicare un'app in Azure usando Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-213">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="2d9bb-214">Un altro esempio è disponibile in [Creare un'app Web ASP.NET Core in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="2d9bb-214">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="2d9bb-215">Pubblicare con MSDeploy in Windows</span><span class="sxs-lookup"><span data-stu-id="2d9bb-215">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="2d9bb-216">Vedere <xref:host-and-deploy/visual-studio-publish-profiles> per istruzioni su come pubblicare un'app con un profilo di pubblicazione di Visual Studio, ad esempio da un prompt dei comandi di Windows usando il comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild).</span><span class="sxs-lookup"><span data-stu-id="2d9bb-216">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="2d9bb-217">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="2d9bb-217">Internet Information Services (IIS)</span></span>

<span data-ttu-id="2d9bb-218">Per le distribuzioni in Internet Information Services (IIS) con la configurazione fornita dal file *web.config* , vedere gli articoli in <xref:host-and-deploy/iis/index> .</span><span class="sxs-lookup"><span data-stu-id="2d9bb-218">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="2d9bb-219">Hosting in una Web farm</span><span class="sxs-lookup"><span data-stu-id="2d9bb-219">Host in a web farm</span></span>

<span data-ttu-id="2d9bb-220">Per informazioni sulla configurazione per ospitare app ASP.NET Core in un ambiente Web farm (ad esempio, distribuzione di più istanze di un'app per la scalabilità), vedere <xref:host-and-deploy/web-farm>.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-220">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="2d9bb-221">Host in Docker</span><span class="sxs-lookup"><span data-stu-id="2d9bb-221">Host on Docker</span></span>

<span data-ttu-id="2d9bb-222">Per altre informazioni, vedere <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="2d9bb-222">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2d9bb-223">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2d9bb-223">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="2d9bb-224">Hosting di ASP.NET</span><span class="sxs-lookup"><span data-stu-id="2d9bb-224">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
