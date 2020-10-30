---
title: Hosting di ASP.NET Core in Linux con Nginx
author: rick-anderson
description: Informazioni su come configurare Nginx come proxy inverso in Ubuntu 16.04 per inoltrare il traffico HTTP a un'app Web ASP.NET Core in esecuzione su Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
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
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 916bb1f761ce99b2296c84e1653e55fffa04f83c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057687"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a><span data-ttu-id="5e932-103">Hosting di ASP.NET Core in Linux con Nginx</span><span class="sxs-lookup"><span data-stu-id="5e932-103">Host ASP.NET Core on Linux with Nginx</span></span>

<span data-ttu-id="5e932-104">Di [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="5e932-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="5e932-105">Questa guida descrive come configurare un ambiente ASP.NET Core pronto per la produzione in un server Ubuntu 16.04.</span><span class="sxs-lookup"><span data-stu-id="5e932-105">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 server.</span></span> <span data-ttu-id="5e932-106">Queste istruzioni si applicano probabilmente anche alle versioni più recenti di Ubuntu, sebbene non siano state testate con le versioni più recenti.</span><span class="sxs-lookup"><span data-stu-id="5e932-106">These instructions likely work with newer versions of Ubuntu, but the instructions haven't been tested with newer versions.</span></span>

<span data-ttu-id="5e932-107">Per informazioni su altre distribuzioni Linux supportate da ASP.NET Core, vedere [Prerequisiti per .NET Core in Linux](/dotnet/core/linux-prerequisites).</span><span class="sxs-lookup"><span data-stu-id="5e932-107">For information on other Linux distributions supported by ASP.NET Core, see [Prerequisites for .NET Core on Linux](/dotnet/core/linux-prerequisites).</span></span>

> [!NOTE]
> <span data-ttu-id="5e932-108">Per Ubuntu 14.04, è consigliabile *supervisord* come soluzione per il monitoraggio del processo Kestrel.</span><span class="sxs-lookup"><span data-stu-id="5e932-108">For Ubuntu 14.04, *supervisord* is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="5e932-109">*systemd* non è disponibile in Ubuntu 14.04.</span><span class="sxs-lookup"><span data-stu-id="5e932-109">*systemd* isn't available on Ubuntu 14.04.</span></span> <span data-ttu-id="5e932-110">Per le istruzioni per Ubuntu 14.04, vedere la [versione precedente di questo argomento](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span><span class="sxs-lookup"><span data-stu-id="5e932-110">For Ubuntu 14.04 instructions, see the [previous version of this topic](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span></span>

<span data-ttu-id="5e932-111">In questa guida:</span><span class="sxs-lookup"><span data-stu-id="5e932-111">This guide:</span></span>

* <span data-ttu-id="5e932-112">Posizionare un'app ASP.NET Core esistente dietro un server proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="5e932-112">Places an existing ASP.NET Core app behind a reverse proxy server.</span></span>
* <span data-ttu-id="5e932-113">Configurare il server proxy inverso per l'inoltro delle richieste al server Web Kestrel.</span><span class="sxs-lookup"><span data-stu-id="5e932-113">Sets up the reverse proxy server to forward requests to the Kestrel web server.</span></span>
* <span data-ttu-id="5e932-114">Verificare che l'app Web venga eseguita all'avvio come daemon.</span><span class="sxs-lookup"><span data-stu-id="5e932-114">Ensures the web app runs on startup as a daemon.</span></span>
* <span data-ttu-id="5e932-115">Configurare uno strumento di gestione del processo per consentire il riavvio dell'app Web.</span><span class="sxs-lookup"><span data-stu-id="5e932-115">Configures a process management tool to help restart the web app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5e932-116">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="5e932-116">Prerequisites</span></span>

1. <span data-ttu-id="5e932-117">Accedere a un server Ubuntu 16.04 con un account utente standard con privilegio sudo.</span><span class="sxs-lookup"><span data-stu-id="5e932-117">Access to an Ubuntu 16.04 server with a standard user account with sudo privilege.</span></span>
1. <span data-ttu-id="5e932-118">Installare il runtime .NET Core nel server.</span><span class="sxs-lookup"><span data-stu-id="5e932-118">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="5e932-119">Visitare la [pagina di download di .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="5e932-119">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="5e932-120">Selezionare la versione più recente di .NET Core non in anteprima.</span><span class="sxs-lookup"><span data-stu-id="5e932-120">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="5e932-121">Scaricare la versione più recente del runtime non di anteprima nella tabella in **Run Apps-Runtime** .</span><span class="sxs-lookup"><span data-stu-id="5e932-121">Download the latest non-preview runtime in the table under **Run apps - Runtime** .</span></span>
   1. <span data-ttu-id="5e932-122">Selezionare il collegamento **istruzioni di gestione pacchetti** Linux e seguire le istruzioni di Ubuntu per la versione di Ubuntu.</span><span class="sxs-lookup"><span data-stu-id="5e932-122">Select the Linux **Package manager instructions** link and follow the Ubuntu instructions for your version of Ubuntu.</span></span>
1. <span data-ttu-id="5e932-123">Un'app ASP.NET Core esistente.</span><span class="sxs-lookup"><span data-stu-id="5e932-123">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="5e932-124">In qualsiasi momento in futuro dopo l'aggiornamento del Framework condiviso, riavviare le app ASP.NET Core ospitate dal server.</span><span class="sxs-lookup"><span data-stu-id="5e932-124">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="5e932-125">Pubblicare e copiare l'app</span><span class="sxs-lookup"><span data-stu-id="5e932-125">Publish and copy over the app</span></span>

<span data-ttu-id="5e932-126">Configurare l'app per una [distribuzione dipendente dal framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="5e932-126">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="5e932-127">Se l'app viene eseguita in locale e non è configurata per connessioni sicure (HTTPS), adottare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="5e932-127">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="5e932-128">Configurare l'app per la gestione di connessioni locali sicure.</span><span class="sxs-lookup"><span data-stu-id="5e932-128">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="5e932-129">Per altre informazioni, vedere la sezione [Configurazione HTTPS](#https-configuration).</span><span class="sxs-lookup"><span data-stu-id="5e932-129">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="5e932-130">Rimuovere `https://localhost:5001` (se presente) dalla proprietà `applicationUrl` nel file *Properties/launchSettings.json* .</span><span class="sxs-lookup"><span data-stu-id="5e932-130">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="5e932-131">Eseguire [dotnet publish](/dotnet/core/tools/dotnet-publish) dall'ambiente di sviluppo per creare il pacchetto di un'app in una directory (ad esempio, *bin/Release/&lt;target_framework_moniker&gt;/publish* ) che può essere eseguita nel server:</span><span class="sxs-lookup"><span data-stu-id="5e932-131">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish* ) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="5e932-132">È anche possibile pubblicare l'app come [distribuzione completa](/dotnet/core/deploying/#self-contained-deployments-scd) se si preferisce non mantenere il runtime .NET Core nel server.</span><span class="sxs-lookup"><span data-stu-id="5e932-132">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="5e932-133">Copiare l'app ASP.NET Core sul server usando uno strumento integrato nel flusso di lavoro dell'organizzazione, ad esempio SCP o FTP.</span><span class="sxs-lookup"><span data-stu-id="5e932-133">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="5e932-134">Le app Web si trovano in genere nella directory *var* , ad esempio, *var/www/helloapp* .</span><span class="sxs-lookup"><span data-stu-id="5e932-134">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp* ).</span></span>

> [!NOTE]
> <span data-ttu-id="5e932-135">In uno scenario di distribuzione di produzione un flusso di lavoro di integrazione continua esegue le operazioni di pubblicazione dell'app e di copia degli asset nel server.</span><span class="sxs-lookup"><span data-stu-id="5e932-135">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

<span data-ttu-id="5e932-136">Eseguire il test dell'app:</span><span class="sxs-lookup"><span data-stu-id="5e932-136">Test the app:</span></span>

1. <span data-ttu-id="5e932-137">Dalla riga di comando, eseguire l'app: `dotnet <app_assembly>.dll`.</span><span class="sxs-lookup"><span data-stu-id="5e932-137">From the command line, run the app: `dotnet <app_assembly>.dll`.</span></span>
1. <span data-ttu-id="5e932-138">In un browser passare a `http://<serveraddress>:<port>` per verificare se l'app funziona in Linux in locale.</span><span class="sxs-lookup"><span data-stu-id="5e932-138">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux locally.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="5e932-139">Configurare un server proxy inverso</span><span class="sxs-lookup"><span data-stu-id="5e932-139">Configure a reverse proxy server</span></span>

<span data-ttu-id="5e932-140">Un proxy inverso è una configurazione comune per la gestione delle app Web dinamiche.</span><span class="sxs-lookup"><span data-stu-id="5e932-140">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="5e932-141">Un proxy inverso termina la richiesta HTTP e la inoltra all'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5e932-141">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core app.</span></span>

### <a name="use-a-reverse-proxy-server"></a><span data-ttu-id="5e932-142">Usare un server proxy inverso</span><span class="sxs-lookup"><span data-stu-id="5e932-142">Use a reverse proxy server</span></span>

<span data-ttu-id="5e932-143">Kestrel funziona perfettamente per la gestione del contenuto dinamico da ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5e932-143">Kestrel is great for serving dynamic content from ASP.NET Core.</span></span> <span data-ttu-id="5e932-144">Tuttavia, le funzionalità di gestione Web sono meno avanzate rispetto a quelle offerte da server come IIS, Apache o Nginx.</span><span class="sxs-lookup"><span data-stu-id="5e932-144">However, the web serving capabilities aren't as feature rich as servers such as IIS, Apache, or Nginx.</span></span> <span data-ttu-id="5e932-145">Un server proxy inverso è in grado di eseguire l'offload di attività come la gestione del contenuto statico, la memorizzazione nella cache delle richieste, la compressione delle richieste e la terminazione HTTPS dal server HTTP.</span><span class="sxs-lookup"><span data-stu-id="5e932-145">A reverse proxy server can offload work such as serving static content, caching requests, compressing requests, and HTTPS termination from the HTTP server.</span></span> <span data-ttu-id="5e932-146">Un server proxy inverso può risiedere in un computer dedicato o essere distribuito insieme a un server HTTP.</span><span class="sxs-lookup"><span data-stu-id="5e932-146">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="5e932-147">Ai fini di questa guida viene usata una singola istanza di Nginx.</span><span class="sxs-lookup"><span data-stu-id="5e932-147">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="5e932-148">Viene eseguito sullo stesso server, insieme al server HTTP.</span><span class="sxs-lookup"><span data-stu-id="5e932-148">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="5e932-149">In base ai requisiti, è possibile scegliere una configurazione diversa.</span><span class="sxs-lookup"><span data-stu-id="5e932-149">Based on requirements, a different setup may be chosen.</span></span>

<span data-ttu-id="5e932-150">Poiché le richieste vengono inviate dal proxy inverso, usare il [middleware delle intestazioni inoltro](xref:host-and-deploy/proxy-load-balancer) dal pacchetto [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) .</span><span class="sxs-lookup"><span data-stu-id="5e932-150">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="5e932-151">Il middleware aggiorna `Request.Scheme` usando l'intestazione `X-Forwarded-Proto`, in modo che gli URI di reindirizzamento e altri criteri di sicurezza funzionino correttamente.</span><span class="sxs-lookup"><span data-stu-id="5e932-151">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>


[!INCLUDE[](~/includes/ForwardedHeaders.md)]

<span data-ttu-id="5e932-152">Richiamare il <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodo all'inizio di `Startup.Configure` prima di chiamare altro middleware.</span><span class="sxs-lookup"><span data-stu-id="5e932-152">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="5e932-153">Configurare il middleware per l'inoltro delle intestazioni `X-Forwarded-For` e `X-Forwarded-Proto`:</span><span class="sxs-lookup"><span data-stu-id="5e932-153">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="5e932-154">Se non vengono specificate opzioni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> per il middleware, le intestazioni predefinite per l'inoltro sono `None`.</span><span class="sxs-lookup"><span data-stu-id="5e932-154">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="5e932-155">I proxy in esecuzione su indirizzi di loopback (127.0.0.0/8, [::1]), incluso l'indirizzo localhost standard (127.0.0.1), sono considerati attendibili per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="5e932-155">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="5e932-156">Se le richieste tra Internet e il server Web vengono gestite anche da altri proxy o reti attendibili all'interno dell'organizzazione, aggiungerli all'elenco di <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> o <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span><span class="sxs-lookup"><span data-stu-id="5e932-156">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="5e932-157">L'esempio seguente aggiunge un server proxy attendibile all'indirizzo IP 10.0.0.100 nel middleware delle intestazioni inoltrate `KnownProxies` in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5e932-157">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="5e932-158">Per altre informazioni, vedere <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="5e932-158">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-nginx"></a><span data-ttu-id="5e932-159">Installare Nginx</span><span class="sxs-lookup"><span data-stu-id="5e932-159">Install Nginx</span></span>

<span data-ttu-id="5e932-160">Usare `apt-get` per installare Nginx.</span><span class="sxs-lookup"><span data-stu-id="5e932-160">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="5e932-161">Il programma di installazione crea uno script di inizializzazione *systemd* che esegue Nginx come daemon all'avvio del sistema.</span><span class="sxs-lookup"><span data-stu-id="5e932-161">The installer creates a *systemd* init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="5e932-162">Seguire le istruzioni di installazione per Ubuntu riportate nell'articolo [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages) (Nginx: pacchetti Debian/Ubuntu ufficiali).</span><span class="sxs-lookup"><span data-stu-id="5e932-162">Follow the installation instructions for Ubuntu at [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span></span>

> [!NOTE]
> <span data-ttu-id="5e932-163">Se sono richiesti moduli Nginx facoltativi, potrebbe essere necessario compilare Nginx dall'origine.</span><span class="sxs-lookup"><span data-stu-id="5e932-163">If optional Nginx modules are required, building Nginx from source might be required.</span></span>

<span data-ttu-id="5e932-164">Poiché Nginx è stato installato per la prima volta, avviarlo in modo esplicito eseguendo:</span><span class="sxs-lookup"><span data-stu-id="5e932-164">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="5e932-165">Verificare che un browser visualizzi la pagina di destinazione predefinita per Nginx.</span><span class="sxs-lookup"><span data-stu-id="5e932-165">Verify a browser displays the default landing page for Nginx.</span></span> <span data-ttu-id="5e932-166">La pagina di destinazione è raggiungibile all'indirizzo `http://<server_IP_address>/index.nginx-debian.html`.</span><span class="sxs-lookup"><span data-stu-id="5e932-166">The landing page is reachable at `http://<server_IP_address>/index.nginx-debian.html`.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="5e932-167">Configurare Nginx</span><span class="sxs-lookup"><span data-stu-id="5e932-167">Configure Nginx</span></span>

<span data-ttu-id="5e932-168">Per configurare Nginx come proxy inverso per inoltrare le richieste all'app ASP.NET Core, modificare */etc/nginx/sites-available/default* .</span><span class="sxs-lookup"><span data-stu-id="5e932-168">To configure Nginx as a reverse proxy to forward requests to your ASP.NET Core app, modify */etc/nginx/sites-available/default* .</span></span> <span data-ttu-id="5e932-169">Aprirlo in un editor di testo e sostituire il contenuto con quanto segue:</span><span class="sxs-lookup"><span data-stu-id="5e932-169">Open it in a text editor, and replace the contents with the following:</span></span>

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

<span data-ttu-id="5e932-170">Se l'app è un' :::no-loc(SignalR)::: :::no-loc(Blazor Server)::: app o <xref:signalr/scale#linux-with-nginx> , vedere <xref:blazor/host-and-deploy/server#linux-with-nginx> rispettivamente e per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="5e932-170">If the app is a :::no-loc(SignalR)::: or :::no-loc(Blazor Server)::: app see <xref:signalr/scale#linux-with-nginx> and <xref:blazor/host-and-deploy/server#linux-with-nginx> respectively for more information.</span></span>

<span data-ttu-id="5e932-171">Se nessun `server_name` corrisponde, Nginx usa il server predefinito.</span><span class="sxs-lookup"><span data-stu-id="5e932-171">When no `server_name` matches, Nginx uses the default server.</span></span> <span data-ttu-id="5e932-172">Se non è definito alcun server predefinito, il primo server nel file di configurazione è il server predefinito.</span><span class="sxs-lookup"><span data-stu-id="5e932-172">If no default server is defined, the first server in the configuration file is the default server.</span></span> <span data-ttu-id="5e932-173">Come procedura consigliata, aggiungere un server predefinito specifico che restituisce un codice di stato 444 nel file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="5e932-173">As a best practice, add a specific default server which returns a status code of 444 in your configuration file.</span></span> <span data-ttu-id="5e932-174">Un esempio di configurazione del server predefinito è il seguente:</span><span class="sxs-lookup"><span data-stu-id="5e932-174">A default server configuration example is:</span></span>

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

<span data-ttu-id="5e932-175">Con il file di configurazione precedente e il server predefinito, Nginx accetta il traffico pubblico sulla porta 80 con l'intestazione host `example.com` o `*.example.com`.</span><span class="sxs-lookup"><span data-stu-id="5e932-175">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="5e932-176">Le richieste che non corrispondono a questi host non verranno inoltrate a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="5e932-176">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="5e932-177">Nginx inoltra a Kestrel le richieste corrispondenti a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="5e932-177">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="5e932-178">Per altre informazioni, vedere [How nginx processes a request](https://nginx.org/docs/http/request_processing.html) (Elaborazione di una richiesta in nginx).</span><span class="sxs-lookup"><span data-stu-id="5e932-178">See [How nginx processes a request](https://nginx.org/docs/http/request_processing.html) for more information.</span></span> <span data-ttu-id="5e932-179">Per cambiare porta/IP di Kestrel, vedere [Kestrel: Configurazione dell'endpoint](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="5e932-179">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="5e932-180">Se non si specifica una [direttiva server_name](https://nginx.org/docs/http/server_names.html) corretta, l'app può risultare esposta a vulnerabilità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="5e932-180">Failure to specify a proper [server_name directive](https://nginx.org/docs/http/server_names.html) exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="5e932-181">L'associazione con caratteri jolly del sottodominio (ad esempio, `*.example.com`) non costituisce un rischio per la sicurezza se viene controllato l'intero dominio padre (a differenza di `*.com`, che è vulnerabile).</span><span class="sxs-lookup"><span data-stu-id="5e932-181">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="5e932-182">Vedere la [sezione 5.4 di RFC7230](https://tools.ietf.org/html/rfc7230#section-5.4) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="5e932-182">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="5e932-183">Una volta stabilita la configurazione di Nginx, eseguire `sudo nginx -t` per verificare la sintassi dei file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="5e932-183">Once the Nginx configuration is established, run `sudo nginx -t` to verify the syntax of the configuration files.</span></span> <span data-ttu-id="5e932-184">Se il test dei file di configurazione ha esito positivo, è possibile forzare Nginx ad applicare le modifiche eseguendo `sudo nginx -s reload`.</span><span class="sxs-lookup"><span data-stu-id="5e932-184">If the configuration file test is successful, force Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

<span data-ttu-id="5e932-185">Per eseguire direttamente l'app nel server:</span><span class="sxs-lookup"><span data-stu-id="5e932-185">To directly run the app on the server:</span></span>

1. <span data-ttu-id="5e932-186">Passare alla directory dell'app.</span><span class="sxs-lookup"><span data-stu-id="5e932-186">Navigate to the app's directory.</span></span>
1. <span data-ttu-id="5e932-187">Eseguire l'app: `dotnet <app_assembly.dll>`, dove `app_assembly.dll` è il nome di file di assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="5e932-187">Run the app: `dotnet <app_assembly.dll>`, where `app_assembly.dll` is the assembly file name of the app.</span></span>

<span data-ttu-id="5e932-188">Se l'app viene eseguita nel server ma non risponde tramite Internet, controllare il firewall del server e verificare che la porta 80 sia aperta.</span><span class="sxs-lookup"><span data-stu-id="5e932-188">If the app runs on the server but fails to respond over the Internet, check the server's firewall and confirm that port 80 is open.</span></span> <span data-ttu-id="5e932-189">Se si usa una macchina virtuale Ubuntu di Azure, aggiungere una regola del gruppo di sicurezza di rete (NSG) che abiliti il traffico in ingresso della porta 80.</span><span class="sxs-lookup"><span data-stu-id="5e932-189">If using an Azure Ubuntu VM, add a Network Security Group (NSG) rule that enables inbound port 80 traffic.</span></span> <span data-ttu-id="5e932-190">Non è necessario abilitare una regola per il traffico in uscita della porta 80, poiché il traffico in uscita viene autorizzato automaticamente quando viene abilitata la regola in ingresso.</span><span class="sxs-lookup"><span data-stu-id="5e932-190">There's no need to enable an outbound port 80 rule, as the outbound traffic is automatically granted when the inbound rule is enabled.</span></span>

<span data-ttu-id="5e932-191">Dopo aver eseguito il test dell'app, arrestare l'app con `Ctrl+C` al prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="5e932-191">When done testing the app, shut the app down with `Ctrl+C` at the command prompt.</span></span>

## <a name="monitor-the-app"></a><span data-ttu-id="5e932-192">Monitorare l'app</span><span class="sxs-lookup"><span data-stu-id="5e932-192">Monitor the app</span></span>

<span data-ttu-id="5e932-193">Il server è configurato in modo da inoltrare le richieste eseguite a `http://<serveraddress>:80` all'app ASP.NET Core eseguita in Kestrel all'indirizzo `http://127.0.0.1:5000`.</span><span class="sxs-lookup"><span data-stu-id="5e932-193">The server is setup to forward requests made to `http://<serveraddress>:80` on to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="5e932-194">Tuttavia, Nginx non è configurato per la gestione del processo Kestrel.</span><span class="sxs-lookup"><span data-stu-id="5e932-194">However, Nginx isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="5e932-195">È possibile usare *systemd* e creare un file del servizio per avviare e monitorare l'app Web sottostante.</span><span class="sxs-lookup"><span data-stu-id="5e932-195">*systemd* can be used to create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="5e932-196">*systemd* è un sistema di inizializzazione che offre molte funzionalità potenti per l'avvio, l'arresto e la gestione dei processi.</span><span class="sxs-lookup"><span data-stu-id="5e932-196">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="5e932-197">Creare il file del servizio</span><span class="sxs-lookup"><span data-stu-id="5e932-197">Create the service file</span></span>

<span data-ttu-id="5e932-198">Creare il file di definizione del servizio:</span><span class="sxs-lookup"><span data-stu-id="5e932-198">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="5e932-199">Di seguito è riportato un esempio di file del servizio per l'app:</span><span class="sxs-lookup"><span data-stu-id="5e932-199">The following is an example service file for the app:</span></span>

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="5e932-200">Nell'esempio precedente, l'utente che gestisce il servizio viene specificato dall' `User` opzione.</span><span class="sxs-lookup"><span data-stu-id="5e932-200">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="5e932-201">L'utente ( `www-data` ) deve esistere e avere la proprietà appropriata dei file dell'app.</span><span class="sxs-lookup"><span data-stu-id="5e932-201">The user (`www-data`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="5e932-202">Usare `TimeoutStopSec` per configurare il tempo di attesa prima che l'app si arresti dopo aver ricevuto il segnale di interrupt iniziale.</span><span class="sxs-lookup"><span data-stu-id="5e932-202">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="5e932-203">Se l'app non si arresta in questo periodo, viene emesso il comando SIGKILL per terminare l'app.</span><span class="sxs-lookup"><span data-stu-id="5e932-203">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="5e932-204">Specificare il valore in secondi senza unità di misura (ad esempio, `150`), un valore per l'intervallo di tempo (ad esempio, `2min 30s`) o `infinity` per disabilitare il timeout.</span><span class="sxs-lookup"><span data-stu-id="5e932-204">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="5e932-205">Per impostazione predefinita, il valore di `TimeoutStopSec` viene impostato sul valore di `DefaultTimeoutStopSec` nel file di configurazione del sistema di gestione ( *systemd-system.conf* , *system.conf.d* , *systemd-user.conf* , *user.conf.d* ).</span><span class="sxs-lookup"><span data-stu-id="5e932-205">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file ( *systemd-system.conf* , *system.conf.d* , *systemd-user.conf* , *user.conf.d* ).</span></span> <span data-ttu-id="5e932-206">Il timeout predefinito per la maggior parte delle distribuzioni è di 90 secondi.</span><span class="sxs-lookup"><span data-stu-id="5e932-206">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="5e932-207">Linux dispone di un file system con distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="5e932-207">Linux has a case-sensitive file system.</span></span> <span data-ttu-id="5e932-208">L'impostazione di ASPNETCORE_ENVIRONMENT su "Production" determina la ricerca del file di configurazione *appsettings.Production.json* , non di *appsettings.production.json* .</span><span class="sxs-lookup"><span data-stu-id="5e932-208">Setting ASPNETCORE_ENVIRONMENT to "Production" results in searching for the configuration file *appsettings.Production.json* , not *appsettings.production.json* .</span></span>

<span data-ttu-id="5e932-209">Per alcuni valori (ad esempio, le stringhe di connessione SQL) è necessario usare caratteri di escape, in modo da consentire ai provider di configurazione di leggere le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="5e932-209">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="5e932-210">Usare il comando seguente per generare un valore con caratteri di escape corretti per l'uso nel file di configurazione:</span><span class="sxs-lookup"><span data-stu-id="5e932-210">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5e932-211">I due punti (`:`) separatori non sono supportati nei nomi delle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="5e932-211">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="5e932-212">Usare un doppio carattere di sottolineatura (`__`) al posto dei due punti.</span><span class="sxs-lookup"><span data-stu-id="5e932-212">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="5e932-213">Il [provider di configurazione delle variabili di ambiente](xref:fundamentals/configuration/index#environment-variables) converte le doppie sottolineature in due punti quando le variabili di ambiente vengono lette nella configurazione.</span><span class="sxs-lookup"><span data-stu-id="5e932-213">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="5e932-214">Nell'esempio seguente la chiave della stringa di connessione `ConnectionStrings:DefaultConnection` è impostata nel file di definizione del servizio come `ConnectionStrings__DefaultConnection`:</span><span class="sxs-lookup"><span data-stu-id="5e932-214">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5e932-215">I due punti (`:`) separatori non sono supportati nei nomi delle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="5e932-215">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="5e932-216">Usare un doppio carattere di sottolineatura (`__`) al posto dei due punti.</span><span class="sxs-lookup"><span data-stu-id="5e932-216">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="5e932-217">Il [provider di configurazione delle variabili di ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converte le doppie sottolineature in due punti quando le variabili di ambiente vengono lette nella configurazione.</span><span class="sxs-lookup"><span data-stu-id="5e932-217">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="5e932-218">Nell'esempio seguente la chiave della stringa di connessione `ConnectionStrings:DefaultConnection` è impostata nel file di definizione del servizio come `ConnectionStrings__DefaultConnection`:</span><span class="sxs-lookup"><span data-stu-id="5e932-218">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="5e932-219">Salvare il file e abilitare il servizio.</span><span class="sxs-lookup"><span data-stu-id="5e932-219">Save the file and enable the service.</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="5e932-220">Avviare il servizio e verificare che sia in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="5e932-220">Start the service and verify that it's running.</span></span>

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="5e932-221">Con il proxy inverso configurato e Kestrel gestito tramite systemd, l'app Web è completamente configurata ed è possibile accedervi da un browser nel computer locale all'indirizzo `http://localhost`.</span><span class="sxs-lookup"><span data-stu-id="5e932-221">With the reverse proxy configured and Kestrel managed through systemd, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="5e932-222">È anche possibile accedervi da un computer remoto, escludendo eventuali firewall che potrebbero impedirlo.</span><span class="sxs-lookup"><span data-stu-id="5e932-222">It's also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="5e932-223">Se si esaminano le intestazioni di risposta, l'intestazione `Server` indica che l'app ASP.NET Core è gestita da Kestrel.</span><span class="sxs-lookup"><span data-stu-id="5e932-223">Inspecting the response headers, the `Server` header shows the ASP.NET Core app being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="5e932-224">Visualizzare i log</span><span class="sxs-lookup"><span data-stu-id="5e932-224">View logs</span></span>

<span data-ttu-id="5e932-225">Poiché l'app Web che usa Kestrel viene gestita con `systemd`, tutti gli eventi e i processi vengono registrati in un giornale di registrazione centralizzato.</span><span class="sxs-lookup"><span data-stu-id="5e932-225">Since the web app using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="5e932-226">Tuttavia, questo giornale include tutte le voci per tutti i servizi e i processi gestiti da `systemd`.</span><span class="sxs-lookup"><span data-stu-id="5e932-226">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="5e932-227">Per visualizzare le voci specifiche di `kestrel-helloapp.service`, usare il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="5e932-227">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="5e932-228">Per filtrare ulteriormente, opzioni come `--since today`, `--until 1 hour ago` o una combinazione delle stesse consentono di ridurre la quantità di voci restituite.</span><span class="sxs-lookup"><span data-stu-id="5e932-228">For further filtering, time options such as `--since today`, `--until 1 hour ago` or a combination of these can reduce the amount of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="5e932-229">Protezione dei dati</span><span class="sxs-lookup"><span data-stu-id="5e932-229">Data protection</span></span>

<span data-ttu-id="5e932-230">Lo [stack di protezione dei dati ASP.NET Core](xref:security/data-protection/introduction) viene usato da diversi [middleware](xref:fundamentals/middleware/index)di ASP.NET Core, tra cui il middleware di autenticazione (ad esempio, :::no-loc(cookie)::: middleware) e le protezioni di richiesta tra siti (CSRF).</span><span class="sxs-lookup"><span data-stu-id="5e932-230">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, :::no-loc(cookie)::: middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="5e932-231">Anche se le DPAPI (Data Protection API) non vengono chiamate dal codice dell'utente, è consigliabile configurare la protezione dati per la creazione di un [archivio di chiavi](xref:security/data-protection/implementation/key-management) crittografiche permanente.</span><span class="sxs-lookup"><span data-stu-id="5e932-231">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="5e932-232">Se non si configura la protezione dei dati, le chiavi vengono mantenute in memoria ed eliminate al riavvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="5e932-232">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="5e932-233">Se il gruppo di chiavi viene archiviato in memoria quando l'app viene riavviata:</span><span class="sxs-lookup"><span data-stu-id="5e932-233">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="5e932-234">Tutti i :::no-loc(cookie)::: token di autenticazione basati su vengono invalidati.</span><span class="sxs-lookup"><span data-stu-id="5e932-234">All :::no-loc(cookie):::-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="5e932-235">Gli utenti devono ripetere l'accesso alla richiesta successiva.</span><span class="sxs-lookup"><span data-stu-id="5e932-235">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="5e932-236">Tutti i dati protetti con il gruppo di chiavi non possono più essere decrittografati.</span><span class="sxs-lookup"><span data-stu-id="5e932-236">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="5e932-237">Questo può includere [i token CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) e [ASP.NET Core TempData MVC :::no-loc(cookie)::: ](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="5e932-237">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData :::no-loc(cookie):::s](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="5e932-238">Per configurare la protezione dei dati in modo da rendere persistente il gruppo di chiavi e crittografarlo, vedere:</span><span class="sxs-lookup"><span data-stu-id="5e932-238">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a><span data-ttu-id="5e932-239">Campi di intestazione della richiesta di grandi dimensioni</span><span class="sxs-lookup"><span data-stu-id="5e932-239">Long request header fields</span></span>

<span data-ttu-id="5e932-240">Le impostazioni predefinite del server proxy limitano in genere i campi di intestazione della richiesta a 4 K o 8 K a seconda della piattaforma.</span><span class="sxs-lookup"><span data-stu-id="5e932-240">Proxy server default settings typically limit request header fields to 4 K or 8 K depending on the platform.</span></span> <span data-ttu-id="5e932-241">Un'app può richiedere campi più lunghi del valore predefinito, ad esempio le app che usano [Azure Active Directory](https://azure.microsoft.com/services/active-directory/).</span><span class="sxs-lookup"><span data-stu-id="5e932-241">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="5e932-242">Se sono necessari campi più lunghi, le impostazioni predefinite del server proxy richiedono la regolazione.</span><span class="sxs-lookup"><span data-stu-id="5e932-242">If longer fields are required, the proxy server's default settings require adjustment.</span></span> <span data-ttu-id="5e932-243">I valori da applicare dipendono dallo scenario.</span><span class="sxs-lookup"><span data-stu-id="5e932-243">The values to apply depend on the scenario.</span></span> <span data-ttu-id="5e932-244">Per altre informazioni, vedere la documentazione del server.</span><span class="sxs-lookup"><span data-stu-id="5e932-244">For more information, see your server's documentation.</span></span>

* [<span data-ttu-id="5e932-245">proxy_buffer_size</span><span class="sxs-lookup"><span data-stu-id="5e932-245">proxy_buffer_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [<span data-ttu-id="5e932-246">proxy_buffers</span><span class="sxs-lookup"><span data-stu-id="5e932-246">proxy_buffers</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [<span data-ttu-id="5e932-247">proxy_busy_buffers_size</span><span class="sxs-lookup"><span data-stu-id="5e932-247">proxy_busy_buffers_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [<span data-ttu-id="5e932-248">large_client_header_buffers</span><span class="sxs-lookup"><span data-stu-id="5e932-248">large_client_header_buffers</span></span>](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> <span data-ttu-id="5e932-249">Aumentare i valori predefiniti dei buffer proxy solo se necessario.</span><span class="sxs-lookup"><span data-stu-id="5e932-249">Don't increase the default values of proxy buffers unless necessary.</span></span> <span data-ttu-id="5e932-250">Se si aumentano questi valori, si aumenta il rischio di sovraccarico del buffer (overflow) e di attacchi Denial of Service (DoS) da parte di utenti malintenzionati.</span><span class="sxs-lookup"><span data-stu-id="5e932-250">Increasing these values increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="secure-the-app"></a><span data-ttu-id="5e932-251">Proteggere l'app</span><span class="sxs-lookup"><span data-stu-id="5e932-251">Secure the app</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="5e932-252">Abilitare AppArmor</span><span class="sxs-lookup"><span data-stu-id="5e932-252">Enable AppArmor</span></span>

<span data-ttu-id="5e932-253">Linux Security Modules (LSM) è un framework che fa parte del kernel Linux a partire da Linux 2.6.</span><span class="sxs-lookup"><span data-stu-id="5e932-253">Linux Security Modules (LSM) is a framework that's part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="5e932-254">LSM supporta diverse implementazioni di moduli di protezione.</span><span class="sxs-lookup"><span data-stu-id="5e932-254">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="5e932-255">[AppArmor](https://wiki.ubuntu.com/AppArmor) è un modulo LSM che implementa un sistema di controllo di accesso obbligatorio che consente di circoscrivere il programma a un set limitato di risorse.</span><span class="sxs-lookup"><span data-stu-id="5e932-255">[AppArmor](https://wiki.ubuntu.com/AppArmor) is a LSM that implements a Mandatory Access Control system which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="5e932-256">Verificare che AppArmor sia abilitato e configurato correttamente.</span><span class="sxs-lookup"><span data-stu-id="5e932-256">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configure-the-firewall"></a><span data-ttu-id="5e932-257">Configurare il firewall</span><span class="sxs-lookup"><span data-stu-id="5e932-257">Configure the firewall</span></span>

<span data-ttu-id="5e932-258">Chiudere tutte le porte esterne che non sono in uso.</span><span class="sxs-lookup"><span data-stu-id="5e932-258">Close off all external ports that are not in use.</span></span> <span data-ttu-id="5e932-259">Un firewall semplice (ufw) fornisce un front-end per `iptables` fornendo un'interfaccia della riga di comando per la configurazione del firewall.</span><span class="sxs-lookup"><span data-stu-id="5e932-259">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a CLI for configuring the firewall.</span></span>

> [!WARNING]
> <span data-ttu-id="5e932-260">Se non è configurato correttamente, un firewall impedisce l'accesso all'intero sistema.</span><span class="sxs-lookup"><span data-stu-id="5e932-260">A firewall will prevent access to the whole system if not configured correctly.</span></span> <span data-ttu-id="5e932-261">Se non si specifica la porta SSH corretta, non sarà possibile accedere al sistema se si usa SSH per la connessione.</span><span class="sxs-lookup"><span data-stu-id="5e932-261">Failure to specify the correct SSH port will effectively lock you out of the system if you are using SSH to connect to it.</span></span> <span data-ttu-id="5e932-262">Il numero di porta predefinito è 22.</span><span class="sxs-lookup"><span data-stu-id="5e932-262">The default port is 22.</span></span> <span data-ttu-id="5e932-263">Per altre informazioni, vedere l'[introduzione a ufw](https://help.ubuntu.com/community/UFW) e il [manuale](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span><span class="sxs-lookup"><span data-stu-id="5e932-263">For more information, see the [introduction to ufw](https://help.ubuntu.com/community/UFW) and the [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span></span>

<span data-ttu-id="5e932-264">Installare `ufw` e configurarlo per consentire il traffico su tutte le porte necessarie.</span><span class="sxs-lookup"><span data-stu-id="5e932-264">Install `ufw` and configure it to allow traffic on any ports needed.</span></span>

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a><span data-ttu-id="5e932-265">Proteggere Nginx</span><span class="sxs-lookup"><span data-stu-id="5e932-265">Secure Nginx</span></span>

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="5e932-266">Modificare il nome di risposta Nginx</span><span class="sxs-lookup"><span data-stu-id="5e932-266">Change the Nginx response name</span></span>

<span data-ttu-id="5e932-267">Modificare *src/http/ngx_http_header_filter_module.c* :</span><span class="sxs-lookup"><span data-stu-id="5e932-267">Edit *src/http/ngx_http_header_filter_module.c* :</span></span>

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a><span data-ttu-id="5e932-268">Configurare le opzioni</span><span class="sxs-lookup"><span data-stu-id="5e932-268">Configure options</span></span>

<span data-ttu-id="5e932-269">Configurare il server con moduli aggiuntivi obbligatori.</span><span class="sxs-lookup"><span data-stu-id="5e932-269">Configure the server with additional required modules.</span></span> <span data-ttu-id="5e932-270">Può essere utile usare un firewall per app Web, ad esempio [ModSecurity](https://www.modsecurity.org/), per la protezione avanzata dell'app.</span><span class="sxs-lookup"><span data-stu-id="5e932-270">Consider using a web app firewall, such as [ModSecurity](https://www.modsecurity.org/), to harden the app.</span></span>

#### <a name="https-configuration"></a><span data-ttu-id="5e932-271">Configurazione HTTPS</span><span class="sxs-lookup"><span data-stu-id="5e932-271">HTTPS configuration</span></span>

<span data-ttu-id="5e932-272">**Configurare l'app per connessioni locali sicure (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="5e932-272">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="5e932-273">Il comando [dotnet run](/dotnet/core/tools/dotnet-run) usa il file *Properties/launchSettings.json* dell'app, che configura l'app per l'ascolto negli URL specificati dalla proprietà `applicationUrl` , ad esempio `https://localhost:5001;http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="5e932-273">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="5e932-274">Configurare l'app per l'uso di un certificato nello sviluppo per il comando `dotnet run` o l'ambiente di sviluppo (F5 o CTRL+F5 in Visual Studio Code) usando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="5e932-274">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="5e932-275">[Sostituire il certificato predefinito della configurazione](xref:fundamentals/servers/kestrel#configuration) ( *consigliato* )</span><span class="sxs-lookup"><span data-stu-id="5e932-275">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) ( *Recommended* )</span></span>
* [<span data-ttu-id="5e932-276">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="5e932-276">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="5e932-277">**Configurare il proxy inverso per connessioni client sicure (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="5e932-277">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

* <span data-ttu-id="5e932-278">Configurare il server in modo che sia in ascolto del traffico HTTPS sulla porta `443` specificando un certificato valido emesso da un'autorità di certificazione attendibile.</span><span class="sxs-lookup"><span data-stu-id="5e932-278">Configure the server to listen to HTTPS traffic on port `443` by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="5e932-279">Rafforzare la protezione adottando alcune delle procedure descritte nel file */etc/nginx/nginx.conf* che segue.</span><span class="sxs-lookup"><span data-stu-id="5e932-279">Harden the security by employing some of the practices depicted in the following */etc/nginx/nginx.conf* file.</span></span> <span data-ttu-id="5e932-280">Gli esempi includono la scelta di una crittografia più complessa e il reindirizzamento di tutto il traffico su HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5e932-280">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

* <span data-ttu-id="5e932-281">L'aggiunta di un'intestazione `HTTP Strict-Transport-Security` (HSTS) garantisce che tutte le richieste successive vengano effettuate dal client via HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5e932-281">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span>

* <span data-ttu-id="5e932-282">Se HTTPS sarà disabilitato in futuro, usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="5e932-282">If HTTPS will be disabled in the future, use one of the following approaches:</span></span>

  * <span data-ttu-id="5e932-283">Non aggiungere l'intestazione HSTS.</span><span class="sxs-lookup"><span data-stu-id="5e932-283">Don't add the HSTS header.</span></span>
  * <span data-ttu-id="5e932-284">Scegliere un `max-age` valore breve.</span><span class="sxs-lookup"><span data-stu-id="5e932-284">Choose a short `max-age` value.</span></span>

<span data-ttu-id="5e932-285">Aggiungere il file di configurazione */etc/nginx/proxy.conf* :</span><span class="sxs-lookup"><span data-stu-id="5e932-285">Add the */etc/nginx/proxy.conf* configuration file:</span></span>

[!code-nginx[](linux-nginx/proxy.conf)]

<span data-ttu-id="5e932-286">Aggiungere il file di configurazione */etc/nginx/proxy.conf* .</span><span class="sxs-lookup"><span data-stu-id="5e932-286">Edit the */etc/nginx/nginx.conf* configuration file.</span></span> <span data-ttu-id="5e932-287">L'esempio contiene entrambe le sezioni `http` e `server` in un unico file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="5e932-287">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

> [!NOTE]
> <span data-ttu-id="5e932-288">:::no-loc(Blazor WebAssembly)::: le app richiedono un `burst` valore di parametro maggiore per contenere il numero maggiore di richieste effettuate da un'app.</span><span class="sxs-lookup"><span data-stu-id="5e932-288">:::no-loc(Blazor WebAssembly)::: apps require a larger `burst` parameter value to accommodate the larger number of requests made by an app.</span></span> <span data-ttu-id="5e932-289">Per altre informazioni, vedere <xref:blazor/host-and-deploy/webassembly#nginx>.</span><span class="sxs-lookup"><span data-stu-id="5e932-289">For more information, see <xref:blazor/host-and-deploy/webassembly#nginx>.</span></span>

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="5e932-290">Proteggere Nginx dal clickjacking</span><span class="sxs-lookup"><span data-stu-id="5e932-290">Secure Nginx from clickjacking</span></span>

<span data-ttu-id="5e932-291">Il [clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), anche noto come *attacco UI Redress* , è un attacco dannoso in cui un visitatore di un sito Web viene indotto a fare clic su un collegamento o un pulsante in una pagina diversa da quella che sta attualmente visualizzando.</span><span class="sxs-lookup"><span data-stu-id="5e932-291">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack* , is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="5e932-292">Usare `X-FRAME-OPTIONS` per proteggere il sito.</span><span class="sxs-lookup"><span data-stu-id="5e932-292">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="5e932-293">Per contrastare gli attacchi di clickjacking:</span><span class="sxs-lookup"><span data-stu-id="5e932-293">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="5e932-294">Modificare il file *nginx.conf* :</span><span class="sxs-lookup"><span data-stu-id="5e932-294">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="5e932-295">Aggiungere la riga `add_header X-Frame-Options "SAMEORIGIN";`.</span><span class="sxs-lookup"><span data-stu-id="5e932-295">Add the line `add_header X-Frame-Options "SAMEORIGIN";`.</span></span>
1. <span data-ttu-id="5e932-296">Salvare il file.</span><span class="sxs-lookup"><span data-stu-id="5e932-296">Save the file.</span></span>
1. <span data-ttu-id="5e932-297">Riavviare Nginx.</span><span class="sxs-lookup"><span data-stu-id="5e932-297">Restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="5e932-298">Analisi del tipo MIME</span><span class="sxs-lookup"><span data-stu-id="5e932-298">MIME-type sniffing</span></span>

<span data-ttu-id="5e932-299">Questa intestazione impedisce alla maggior parte dei browser di dedurre dall'analisi del tipo MIME un tipo di contenuto diverso da quello dichiarato, poiché l'intestazione indica al browser di non eseguire l'override del tipo di contenuto della risposta.</span><span class="sxs-lookup"><span data-stu-id="5e932-299">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="5e932-300">Con l'opzione `nosniff`, se il server indica che il contenuto è "text/html", il browser ne esegue il rendering come "text/html".</span><span class="sxs-lookup"><span data-stu-id="5e932-300">With the `nosniff` option, if the server says the content is "text/html", the browser renders it as "text/html".</span></span>

<span data-ttu-id="5e932-301">Modificare il file *nginx.conf* :</span><span class="sxs-lookup"><span data-stu-id="5e932-301">Edit the *nginx.conf* file:</span></span>

```bash
sudo nano /etc/nginx/nginx.conf
```

<span data-ttu-id="5e932-302">Aggiungere la riga `add_header X-Content-Type-Options "nosniff";` e salvare il file, quindi riavviare Nginx.</span><span class="sxs-lookup"><span data-stu-id="5e932-302">Add the line `add_header X-Content-Type-Options "nosniff";` and save the file, then restart Nginx.</span></span>

## <a name="additional-nginx-suggestions"></a><span data-ttu-id="5e932-303">Suggerimenti nginx aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="5e932-303">Additional Nginx suggestions</span></span>

<span data-ttu-id="5e932-304">Dopo aver aggiornato il Framework condiviso sul server, riavviare le app ASP.NET Core ospitate dal server.</span><span class="sxs-lookup"><span data-stu-id="5e932-304">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5e932-305">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="5e932-305">Additional resources</span></span>

* [<span data-ttu-id="5e932-306">Prerequisiti per .NET Core in Linux</span><span class="sxs-lookup"><span data-stu-id="5e932-306">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* <span data-ttu-id="5e932-307">[Nginx: Binary Releases: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages) (Nginx: Versioni binarie: Pacchetti ufficiali Debian/Ubuntu)</span><span class="sxs-lookup"><span data-stu-id="5e932-307">[Nginx: Binary Releases: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)</span></span>
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* <span data-ttu-id="5e932-308">[NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX: Uso dell'intestazione Forwarded)</span><span class="sxs-lookup"><span data-stu-id="5e932-308">[NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)</span></span>
