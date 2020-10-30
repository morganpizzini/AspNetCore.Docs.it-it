---
title: Hosting di ASP.NET Core in Linux con Apache
author: rick-anderson
description: Informazioni su come configurare Apache come server proxy inverso in CentOS per reindirizzare il traffico HTTP a un'app Web ASP.NET Core in esecuzione su Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 04/10/2020
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
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 0bae3f888a1b7a3c2860b85754779189c636d86f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057700"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a><span data-ttu-id="81a2c-103">Hosting di ASP.NET Core in Linux con Apache</span><span class="sxs-lookup"><span data-stu-id="81a2c-103">Host ASP.NET Core on Linux with Apache</span></span>

<span data-ttu-id="81a2c-104">Di [Shayne Boyer](https://github.com/spboyer)</span><span class="sxs-lookup"><span data-stu-id="81a2c-104">By [Shayne Boyer](https://github.com/spboyer)</span></span>

<span data-ttu-id="81a2c-105">Questa guida fornisce informazioni su come configurare [Apache](https://httpd.apache.org/) come server proxy inverso in [CentOS 7](https://www.centos.org/) per reindirizzare il traffico HTTP a un'app Web ASP.NET Core in esecuzione su server [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="81a2c-105">Using this guide, learn how to set up [Apache](https://httpd.apache.org/) as a reverse proxy server on [CentOS 7](https://www.centos.org/) to redirect HTTP traffic to an ASP.NET Core web app running on [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="81a2c-106">L'[estensione mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) e i moduli correlati creano il proxy inverso del server.</span><span class="sxs-lookup"><span data-stu-id="81a2c-106">The [mod_proxy extension](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) and related modules create the server's reverse proxy.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="81a2c-107">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="81a2c-107">Prerequisites</span></span>

* <span data-ttu-id="81a2c-108">Server che esegue CentOS 7 con un account utente standard con privilegio sudo.</span><span class="sxs-lookup"><span data-stu-id="81a2c-108">Server running CentOS 7 with a standard user account with sudo privilege.</span></span>
* <span data-ttu-id="81a2c-109">Installare il runtime .NET Core nel server.</span><span class="sxs-lookup"><span data-stu-id="81a2c-109">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="81a2c-110">Visitare la [pagina di download di .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="81a2c-110">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="81a2c-111">Selezionare la versione più recente di .NET Core non in anteprima.</span><span class="sxs-lookup"><span data-stu-id="81a2c-111">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="81a2c-112">Scaricare la versione più recente del runtime non di anteprima nella tabella in **Run Apps-Runtime** .</span><span class="sxs-lookup"><span data-stu-id="81a2c-112">Download the latest non-preview runtime in the table under **Run apps - Runtime** .</span></span>
   1. <span data-ttu-id="81a2c-113">Selezionare il collegamento **istruzioni di gestione pacchetti** Linux e seguire le istruzioni CentOS.</span><span class="sxs-lookup"><span data-stu-id="81a2c-113">Select the Linux **Package manager instructions** link and follow the CentOS instructions.</span></span>
* <span data-ttu-id="81a2c-114">Un'app ASP.NET Core esistente.</span><span class="sxs-lookup"><span data-stu-id="81a2c-114">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="81a2c-115">In qualsiasi momento in futuro dopo l'aggiornamento del Framework condiviso, riavviare le app ASP.NET Core ospitate dal server.</span><span class="sxs-lookup"><span data-stu-id="81a2c-115">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="81a2c-116">Pubblicare e copiare l'app</span><span class="sxs-lookup"><span data-stu-id="81a2c-116">Publish and copy over the app</span></span>

<span data-ttu-id="81a2c-117">Configurare l'app per una [distribuzione dipendente dal framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="81a2c-117">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="81a2c-118">Se l'app viene eseguita in locale e non è configurata per connessioni sicure (HTTPS), adottare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="81a2c-118">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="81a2c-119">Configurare l'app per la gestione di connessioni locali sicure.</span><span class="sxs-lookup"><span data-stu-id="81a2c-119">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="81a2c-120">Per altre informazioni, vedere la sezione [Configurazione HTTPS](#https-configuration).</span><span class="sxs-lookup"><span data-stu-id="81a2c-120">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="81a2c-121">Rimuovere `https://localhost:5001` (se presente) dalla proprietà `applicationUrl` nel file *Properties/launchSettings.json* .</span><span class="sxs-lookup"><span data-stu-id="81a2c-121">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="81a2c-122">Eseguire [dotnet publish](/dotnet/core/tools/dotnet-publish) dall'ambiente di sviluppo per creare il pacchetto di un'app in una directory (ad esempio, *bin/Release/&lt;target_framework_moniker&gt;/publish* ) che può essere eseguita nel server:</span><span class="sxs-lookup"><span data-stu-id="81a2c-122">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish* ) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="81a2c-123">È anche possibile pubblicare l'app come [distribuzione completa](/dotnet/core/deploying/#self-contained-deployments-scd) se si preferisce non mantenere il runtime .NET Core nel server.</span><span class="sxs-lookup"><span data-stu-id="81a2c-123">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="81a2c-124">Copiare l'app ASP.NET Core sul server usando uno strumento integrato nel flusso di lavoro dell'organizzazione, ad esempio SCP o FTP.</span><span class="sxs-lookup"><span data-stu-id="81a2c-124">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="81a2c-125">Le app Web si trovano in genere nella directory *var* , ad esempio, *var/www/helloapp* .</span><span class="sxs-lookup"><span data-stu-id="81a2c-125">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp* ).</span></span>

> [!NOTE]
> <span data-ttu-id="81a2c-126">In uno scenario di distribuzione di produzione un flusso di lavoro di integrazione continua esegue le operazioni di pubblicazione dell'app e di copia degli asset nel server.</span><span class="sxs-lookup"><span data-stu-id="81a2c-126">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

## <a name="configure-a-proxy-server"></a><span data-ttu-id="81a2c-127">Configurazione di un server proxy</span><span class="sxs-lookup"><span data-stu-id="81a2c-127">Configure a proxy server</span></span>

<span data-ttu-id="81a2c-128">Un proxy inverso è una configurazione comune per la gestione delle app Web dinamiche.</span><span class="sxs-lookup"><span data-stu-id="81a2c-128">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="81a2c-129">Il proxy inverso termina la richiesta HTTP e la inoltra all'app ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="81a2c-129">The reverse proxy terminates the HTTP request and forwards it to the ASP.NET app.</span></span>

<span data-ttu-id="81a2c-130">Un server proxy inoltra le richieste del client a un altro server anziché evaderle da solo.</span><span class="sxs-lookup"><span data-stu-id="81a2c-130">A proxy server is one which forwards client requests to another server instead of fulfilling requests itself.</span></span> <span data-ttu-id="81a2c-131">Un proxy inverso inoltra a una destinazione fissa, in genere per conto di client non autorizzati.</span><span class="sxs-lookup"><span data-stu-id="81a2c-131">A reverse proxy forwards to a fixed destination, typically on behalf of arbitrary clients.</span></span> <span data-ttu-id="81a2c-132">In questa guida Apache viene configurato come proxy inverso in esecuzione nello stesso server usato da Kestrel per gestire l'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="81a2c-132">In this guide, Apache is configured as the reverse proxy running on the same server that Kestrel is serving the ASP.NET Core app.</span></span>

<span data-ttu-id="81a2c-133">Poiché le richieste vengono inviate dal proxy inverso, usare il [middleware delle intestazioni inoltro](xref:host-and-deploy/proxy-load-balancer) dal pacchetto [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) .</span><span class="sxs-lookup"><span data-stu-id="81a2c-133">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="81a2c-134">Il middleware aggiorna `Request.Scheme` usando l'intestazione `X-Forwarded-Proto`, in modo che gli URI di reindirizzamento e altri criteri di sicurezza funzionino correttamente.</span><span class="sxs-lookup"><span data-stu-id="81a2c-134">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="81a2c-135">Qualsiasi componente che dipende dallo schema, ad esempio l'autenticazione, la generazione di collegamenti, i reindirizzamenti e la georilevazione, deve essere inserito dopo aver richiamato il middleware delle intestazioni inoltrate.</span><span class="sxs-lookup"><span data-stu-id="81a2c-135">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

<span data-ttu-id="81a2c-136">Richiamare il <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodo all'inizio di `Startup.Configure` prima di chiamare altro middleware.</span><span class="sxs-lookup"><span data-stu-id="81a2c-136">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="81a2c-137">Configurare il middleware per l'inoltro delle intestazioni `X-Forwarded-For` e `X-Forwarded-Proto`:</span><span class="sxs-lookup"><span data-stu-id="81a2c-137">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="81a2c-138">Se non vengono specificate opzioni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> per il middleware, le intestazioni predefinite per l'inoltro sono `None`.</span><span class="sxs-lookup"><span data-stu-id="81a2c-138">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="81a2c-139">I proxy in esecuzione su indirizzi di loopback (127.0.0.0/8, [::1]), incluso l'indirizzo localhost standard (127.0.0.1), sono considerati attendibili per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="81a2c-139">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="81a2c-140">Se le richieste tra Internet e il server Web vengono gestite anche da altri proxy o reti attendibili all'interno dell'organizzazione, aggiungerli all'elenco di <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> o <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span><span class="sxs-lookup"><span data-stu-id="81a2c-140">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="81a2c-141">L'esempio seguente aggiunge un server proxy attendibile all'indirizzo IP 10.0.0.100 nel middleware delle intestazioni inoltrate `KnownProxies` in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="81a2c-141">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="81a2c-142">Per altre informazioni, vedere <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="81a2c-142">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-apache"></a><span data-ttu-id="81a2c-143">Installare Apache</span><span class="sxs-lookup"><span data-stu-id="81a2c-143">Install Apache</span></span>

<span data-ttu-id="81a2c-144">Aggiornare i pacchetti CentOS alle versioni stabili più recenti:</span><span class="sxs-lookup"><span data-stu-id="81a2c-144">Update CentOS packages to their latest stable versions:</span></span>

```bash
sudo yum update -y
```

<span data-ttu-id="81a2c-145">Installare il server Web Apache in CentOS con un singolo comando `yum`:</span><span class="sxs-lookup"><span data-stu-id="81a2c-145">Install the Apache web server on CentOS with a single `yum` command:</span></span>

```bash
sudo yum -y install httpd mod_ssl
```

<span data-ttu-id="81a2c-146">Esempio di output dopo l'esecuzione del comando:</span><span class="sxs-lookup"><span data-stu-id="81a2c-146">Sample output after running the command:</span></span>

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> <span data-ttu-id="81a2c-147">In questo esempio, l'output rispecchia httpd.86_64 poiché la versione CentOS 7 è a 64 bit.</span><span class="sxs-lookup"><span data-stu-id="81a2c-147">In this example, the output reflects httpd.86_64 since the CentOS 7 version is 64 bit.</span></span> <span data-ttu-id="81a2c-148">Per verificare dove è installato Apache, eseguire `whereis httpd` da un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="81a2c-148">To verify where Apache is installed, run `whereis httpd` from a command prompt.</span></span>

### <a name="configure-apache"></a><span data-ttu-id="81a2c-149">Configurare Apache</span><span class="sxs-lookup"><span data-stu-id="81a2c-149">Configure Apache</span></span>

<span data-ttu-id="81a2c-150">I file di configurazione per Apache si trovano all'interno della directory `/etc/httpd/conf.d/`.</span><span class="sxs-lookup"><span data-stu-id="81a2c-150">Configuration files for Apache are located within the `/etc/httpd/conf.d/` directory.</span></span> <span data-ttu-id="81a2c-151">Qualsiasi file con l'estensione *.conf* viene elaborato in ordine alfabetico, in aggiunta ai file di configurazione dei moduli in `/etc/httpd/conf.modules.d/`, che contiene tutti i file di configurazione necessari per caricare i moduli.</span><span class="sxs-lookup"><span data-stu-id="81a2c-151">Any file with the *.conf* extension is processed in alphabetical order in addition to the module configuration files in `/etc/httpd/conf.modules.d/`, which contains any configuration files necessary to load modules.</span></span>

<span data-ttu-id="81a2c-152">Creare un file di configurazione denominato *helloapp.conf* , per l'app:</span><span class="sxs-lookup"><span data-stu-id="81a2c-152">Create a configuration file, named *helloapp.conf* , for the app:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

<span data-ttu-id="81a2c-153">Il blocco `VirtualHost` può comparire più volte, in uno o più file su un server.</span><span class="sxs-lookup"><span data-stu-id="81a2c-153">The `VirtualHost` block can appear multiple times, in one or more files on a server.</span></span> <span data-ttu-id="81a2c-154">Nel file di configurazione precedente Apache accetta il traffico pubblico sulla porta 80.</span><span class="sxs-lookup"><span data-stu-id="81a2c-154">In the preceding configuration file, Apache accepts public traffic on port 80.</span></span> <span data-ttu-id="81a2c-155">Il dominio `www.example.com` viene gestito e l'alias `*.example.com` viene risolto nello stesso sito.</span><span class="sxs-lookup"><span data-stu-id="81a2c-155">The domain `www.example.com` is being served, and the `*.example.com` alias resolves to the same website.</span></span> <span data-ttu-id="81a2c-156">Per altre informazioni, vedere [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html) (Supporto degli host virtuali basati sul nome).</span><span class="sxs-lookup"><span data-stu-id="81a2c-156">See [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html) for more information.</span></span> <span data-ttu-id="81a2c-157">Le richieste vengono trasmesse tramite proxy alla radice sulla porta 5000 del server all'indirizzo 127.0.0.1.</span><span class="sxs-lookup"><span data-stu-id="81a2c-157">Requests are proxied at the root to port 5000 of the server at 127.0.0.1.</span></span> <span data-ttu-id="81a2c-158">Per la comunicazione bidirezionale, sono necessari `ProxyPass` e `ProxyPassReverse`.</span><span class="sxs-lookup"><span data-stu-id="81a2c-158">For bi-directional communication, `ProxyPass` and `ProxyPassReverse` are required.</span></span> <span data-ttu-id="81a2c-159">Per cambiare porta/IP di Kestrel, vedere [Kestrel: Configurazione dell'endpoint](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="81a2c-159">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="81a2c-160">Se non si specifica una [direttiva ServerName](https://httpd.apache.org/docs/current/mod/core.html#servername) corretta nel blocco **VirtualHost** , l'app può risultare esposta a vulnerabilità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="81a2c-160">Failure to specify a proper [ServerName directive](https://httpd.apache.org/docs/current/mod/core.html#servername) in the **VirtualHost** block exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="81a2c-161">L'associazione con caratteri jolly del sottodominio (ad esempio, `*.example.com`) non costituisce un rischio per la sicurezza se viene controllato l'intero dominio padre (a differenza di `*.com`, che è vulnerabile).</span><span class="sxs-lookup"><span data-stu-id="81a2c-161">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="81a2c-162">Vedere la [sezione 5.4 di RFC7230](https://tools.ietf.org/html/rfc7230#section-5.4) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="81a2c-162">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="81a2c-163">La registrazione può essere configurata per ogni `VirtualHost` usando le direttive `ErrorLog` e `CustomLog`.</span><span class="sxs-lookup"><span data-stu-id="81a2c-163">Logging can be configured per `VirtualHost` using `ErrorLog` and `CustomLog` directives.</span></span> <span data-ttu-id="81a2c-164">`ErrorLog` è la posizione in cui il server registra gli errori e `CustomLog` imposta il nome del file e il formato del file di log.</span><span class="sxs-lookup"><span data-stu-id="81a2c-164">`ErrorLog` is the location where the server logs errors, and `CustomLog` sets the filename and format of log file.</span></span> <span data-ttu-id="81a2c-165">In questo caso, si tratta della posizione in cui vengono registrate le informazioni sulla richiesta.</span><span class="sxs-lookup"><span data-stu-id="81a2c-165">In this case, this is where request information is logged.</span></span> <span data-ttu-id="81a2c-166">È presente una riga per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="81a2c-166">There's one line for each request.</span></span>

<span data-ttu-id="81a2c-167">Salvare il file e testare la configurazione.</span><span class="sxs-lookup"><span data-stu-id="81a2c-167">Save the file and test the configuration.</span></span> <span data-ttu-id="81a2c-168">Se tutto ciò viene superato, la risposta deve essere `Syntax [OK]`.</span><span class="sxs-lookup"><span data-stu-id="81a2c-168">If everything passes, the response should be `Syntax [OK]`.</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="81a2c-169">Riavviare Apache:</span><span class="sxs-lookup"><span data-stu-id="81a2c-169">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a><span data-ttu-id="81a2c-170">Monitorare l'app</span><span class="sxs-lookup"><span data-stu-id="81a2c-170">Monitor the app</span></span>

<span data-ttu-id="81a2c-171">Apache è ora configurato in modo da inoltrare le richieste eseguite a `http://localhost:80` all'app ASP.NET Core eseguita in Kestrel all'indirizzo `http://127.0.0.1:5000`.</span><span class="sxs-lookup"><span data-stu-id="81a2c-171">Apache is now setup to forward requests made to `http://localhost:80` to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="81a2c-172">Tuttavia, Apache non è configurato per la gestione del processo Kestrel.</span><span class="sxs-lookup"><span data-stu-id="81a2c-172">However, Apache isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="81a2c-173">Usare *systemd* e creare un file del servizio per avviare e monitorare l'app Web sottostante.</span><span class="sxs-lookup"><span data-stu-id="81a2c-173">Use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="81a2c-174">*systemd* è un sistema di inizializzazione che offre molte funzionalità potenti per l'avvio, l'arresto e la gestione dei processi.</span><span class="sxs-lookup"><span data-stu-id="81a2c-174">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span>

### <a name="create-the-service-file"></a><span data-ttu-id="81a2c-175">Creare il file del servizio</span><span class="sxs-lookup"><span data-stu-id="81a2c-175">Create the service file</span></span>

<span data-ttu-id="81a2c-176">Creare il file di definizione del servizio:</span><span class="sxs-lookup"><span data-stu-id="81a2c-176">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="81a2c-177">Un esempio di file del servizio per l'app:</span><span class="sxs-lookup"><span data-stu-id="81a2c-177">An example service file for the app:</span></span>

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="81a2c-178">Nell'esempio precedente, l'utente che gestisce il servizio viene specificato dall' `User` opzione.</span><span class="sxs-lookup"><span data-stu-id="81a2c-178">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="81a2c-179">L'utente ( `apache` ) deve esistere e avere la proprietà appropriata dei file dell'app.</span><span class="sxs-lookup"><span data-stu-id="81a2c-179">The user (`apache`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="81a2c-180">Usare `TimeoutStopSec` per configurare il tempo di attesa prima che l'app si arresti dopo aver ricevuto il segnale di interrupt iniziale.</span><span class="sxs-lookup"><span data-stu-id="81a2c-180">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="81a2c-181">Se l'app non si arresta in questo periodo, viene emesso il comando SIGKILL per terminare l'app.</span><span class="sxs-lookup"><span data-stu-id="81a2c-181">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="81a2c-182">Specificare il valore in secondi senza unità di misura (ad esempio, `150`), un valore per l'intervallo di tempo (ad esempio, `2min 30s`) o `infinity` per disabilitare il timeout.</span><span class="sxs-lookup"><span data-stu-id="81a2c-182">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="81a2c-183">Per impostazione predefinita, il valore di `TimeoutStopSec` viene impostato sul valore di `DefaultTimeoutStopSec` nel file di configurazione del sistema di gestione ( *systemd-system.conf* , *system.conf.d* , *systemd-user.conf* , *user.conf.d* ).</span><span class="sxs-lookup"><span data-stu-id="81a2c-183">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file ( *systemd-system.conf* , *system.conf.d* , *systemd-user.conf* , *user.conf.d* ).</span></span> <span data-ttu-id="81a2c-184">Il timeout predefinito per la maggior parte delle distribuzioni è di 90 secondi.</span><span class="sxs-lookup"><span data-stu-id="81a2c-184">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="81a2c-185">Per alcuni valori (ad esempio, le stringhe di connessione SQL) è necessario usare caratteri di escape, in modo da consentire ai provider di configurazione di leggere le variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="81a2c-185">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="81a2c-186">Usare il comando seguente per generare un valore con caratteri di escape corretti per l'uso nel file di configurazione:</span><span class="sxs-lookup"><span data-stu-id="81a2c-186">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="81a2c-187">I due punti (`:`) separatori non sono supportati nei nomi delle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="81a2c-187">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="81a2c-188">Usare un doppio carattere di sottolineatura (`__`) al posto dei due punti.</span><span class="sxs-lookup"><span data-stu-id="81a2c-188">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="81a2c-189">Il [provider di configurazione delle variabili di ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converte le doppie sottolineature in due punti quando le variabili di ambiente vengono lette nella configurazione.</span><span class="sxs-lookup"><span data-stu-id="81a2c-189">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="81a2c-190">Nell'esempio seguente la chiave della stringa di connessione `ConnectionStrings:DefaultConnection` è impostata nel file di definizione del servizio come `ConnectionStrings__DefaultConnection`:</span><span class="sxs-lookup"><span data-stu-id="81a2c-190">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="81a2c-191">I due punti (`:`) separatori non sono supportati nei nomi delle variabili di ambiente.</span><span class="sxs-lookup"><span data-stu-id="81a2c-191">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="81a2c-192">Usare un doppio carattere di sottolineatura (`__`) al posto dei due punti.</span><span class="sxs-lookup"><span data-stu-id="81a2c-192">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="81a2c-193">Il [provider di configurazione delle variabili di ambiente](xref:fundamentals/configuration/index#environment-variables) converte le doppie sottolineature in due punti quando le variabili di ambiente vengono lette nella configurazione.</span><span class="sxs-lookup"><span data-stu-id="81a2c-193">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="81a2c-194">Nell'esempio seguente la chiave della stringa di connessione `ConnectionStrings:DefaultConnection` è impostata nel file di definizione del servizio come `ConnectionStrings__DefaultConnection`:</span><span class="sxs-lookup"><span data-stu-id="81a2c-194">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="81a2c-195">Salvare il file e abilitare il servizio:</span><span class="sxs-lookup"><span data-stu-id="81a2c-195">Save the file and enable the service:</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="81a2c-196">Avviare il servizio e verificare che sia in esecuzione:</span><span class="sxs-lookup"><span data-stu-id="81a2c-196">Start the service and verify that it's running:</span></span>

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="81a2c-197">Con il proxy inverso configurato e Kestrel gestito tramite *systemd* , l'app Web è completamente configurata ed è possibile accedervi da un browser nel computer locale all'indirizzo `http://localhost`.</span><span class="sxs-lookup"><span data-stu-id="81a2c-197">With the reverse proxy configured and Kestrel managed through *systemd* , the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="81a2c-198">Se si esaminano le intestazioni di risposta, l'intestazione **Server** indica che l'app ASP.NET Core è gestita da Kestrel:</span><span class="sxs-lookup"><span data-stu-id="81a2c-198">Inspecting the response headers, the **Server** header indicates that the ASP.NET Core app is served by Kestrel:</span></span>

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="81a2c-199">Visualizzare i log</span><span class="sxs-lookup"><span data-stu-id="81a2c-199">View logs</span></span>

<span data-ttu-id="81a2c-200">Poiché l'app Web che usa Kestrel viene gestita tramite *systemd* , gli eventi e i processi vengono registrati in un giornale di registrazione centralizzato.</span><span class="sxs-lookup"><span data-stu-id="81a2c-200">Since the web app using Kestrel is managed using *systemd* , events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="81a2c-201">Tuttavia, questo giornale include le voci per tutti i servizi e i processi gestiti da *systemd* .</span><span class="sxs-lookup"><span data-stu-id="81a2c-201">However, this journal includes entries for all of the services and processes managed by *systemd* .</span></span> <span data-ttu-id="81a2c-202">Per visualizzare le voci specifiche di `kestrel-helloapp.service`, usare il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="81a2c-202">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="81a2c-203">Per il filtro di data e ora, specificare le opzioni di tempo con il comando.</span><span class="sxs-lookup"><span data-stu-id="81a2c-203">For time filtering, specify time options with the command.</span></span> <span data-ttu-id="81a2c-204">Ad esempio, usare `--since today` per filtrare in base al giorno corrente o `--until 1 hour ago` per visualizzare le voci dell'ora precedente.</span><span class="sxs-lookup"><span data-stu-id="81a2c-204">For example, use `--since today` to filter for the current day or `--until 1 hour ago` to see the previous hour's entries.</span></span> <span data-ttu-id="81a2c-205">Per altre informazioni, vedere la [pagina di manuale per journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span><span class="sxs-lookup"><span data-stu-id="81a2c-205">For more information, see the [man page for journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="81a2c-206">Protezione dei dati</span><span class="sxs-lookup"><span data-stu-id="81a2c-206">Data protection</span></span>

<span data-ttu-id="81a2c-207">Lo [stack di protezione dei dati ASP.NET Core](xref:security/data-protection/introduction) viene usato da diversi [middleware](xref:fundamentals/middleware/index)di ASP.NET Core, tra cui il middleware di autenticazione (ad esempio, :::no-loc(cookie)::: middleware) e le protezioni di richiesta tra siti (CSRF).</span><span class="sxs-lookup"><span data-stu-id="81a2c-207">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, :::no-loc(cookie)::: middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="81a2c-208">Anche se le DPAPI (Data Protection API) non vengono chiamate dal codice dell'utente, è consigliabile configurare la protezione dati per la creazione di un [archivio di chiavi](xref:security/data-protection/implementation/key-management) crittografiche permanente.</span><span class="sxs-lookup"><span data-stu-id="81a2c-208">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="81a2c-209">Se non si configura la protezione dei dati, le chiavi vengono mantenute in memoria ed eliminate al riavvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="81a2c-209">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="81a2c-210">Se il gruppo di chiavi viene archiviato in memoria quando l'app viene riavviata:</span><span class="sxs-lookup"><span data-stu-id="81a2c-210">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="81a2c-211">Tutti i :::no-loc(cookie)::: token di autenticazione basati su vengono invalidati.</span><span class="sxs-lookup"><span data-stu-id="81a2c-211">All :::no-loc(cookie):::-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="81a2c-212">Gli utenti devono ripetere l'accesso alla richiesta successiva.</span><span class="sxs-lookup"><span data-stu-id="81a2c-212">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="81a2c-213">Tutti i dati protetti con il gruppo di chiavi non possono più essere decrittografati.</span><span class="sxs-lookup"><span data-stu-id="81a2c-213">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="81a2c-214">Questo può includere [i token CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) e [ASP.NET Core TempData MVC :::no-loc(cookie)::: ](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="81a2c-214">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData :::no-loc(cookie):::s](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="81a2c-215">Per configurare la protezione dei dati in modo da rendere persistente il gruppo di chiavi e crittografarlo, vedere:</span><span class="sxs-lookup"><span data-stu-id="81a2c-215">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a><span data-ttu-id="81a2c-216">Proteggere l'app</span><span class="sxs-lookup"><span data-stu-id="81a2c-216">Secure the app</span></span>

### <a name="configure-firewall"></a><span data-ttu-id="81a2c-217">Configurare il firewall</span><span class="sxs-lookup"><span data-stu-id="81a2c-217">Configure firewall</span></span>

<span data-ttu-id="81a2c-218">*Firewalld* è un daemon dinamico per gestire il firewall con il supporto per le aree di rete.</span><span class="sxs-lookup"><span data-stu-id="81a2c-218">*Firewalld* is a dynamic daemon to manage the firewall with support for network zones.</span></span> <span data-ttu-id="81a2c-219">È comunque possibile usare iptables per gestire le porte e il filtro dei pacchetti.</span><span class="sxs-lookup"><span data-stu-id="81a2c-219">Ports and packet filtering can still be managed by iptables.</span></span> <span data-ttu-id="81a2c-220">*Firewalld* dovrebbe essere installato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="81a2c-220">*Firewalld* should be installed by default.</span></span> <span data-ttu-id="81a2c-221">È possibile usare `yum` per installare il pacchetto o verificare che sia installato.</span><span class="sxs-lookup"><span data-stu-id="81a2c-221">`yum` can be used to install the package or verify it's installed.</span></span>

```bash
sudo yum install firewalld -y
```

<span data-ttu-id="81a2c-222">Usare `firewalld` per aprire solo le porte necessarie per l'app.</span><span class="sxs-lookup"><span data-stu-id="81a2c-222">Use `firewalld` to open only the ports needed for the app.</span></span> <span data-ttu-id="81a2c-223">In questo caso vengono usate le porte 80 e 443.</span><span class="sxs-lookup"><span data-stu-id="81a2c-223">In this case, port 80 and 443 are used.</span></span> <span data-ttu-id="81a2c-224">I comandi seguenti impostano in modo permanente le porte 80 e 443 per l'apertura:</span><span class="sxs-lookup"><span data-stu-id="81a2c-224">The following commands permanently set ports 80 and 443 to open:</span></span>

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

<span data-ttu-id="81a2c-225">Ricaricare le impostazioni del firewall.</span><span class="sxs-lookup"><span data-stu-id="81a2c-225">Reload the firewall settings.</span></span> <span data-ttu-id="81a2c-226">Verificare i servizi e le porte disponibili nell'area predefinita.</span><span class="sxs-lookup"><span data-stu-id="81a2c-226">Check the available services and ports in the default zone.</span></span> <span data-ttu-id="81a2c-227">Le opzioni sono disponibili controllando `firewall-cmd -h`.</span><span class="sxs-lookup"><span data-stu-id="81a2c-227">Options are available by inspecting `firewall-cmd -h`.</span></span>

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="https-configuration"></a><span data-ttu-id="81a2c-228">Configurazione HTTPS</span><span class="sxs-lookup"><span data-stu-id="81a2c-228">HTTPS configuration</span></span>

<span data-ttu-id="81a2c-229">**Configurare l'app per connessioni locali sicure (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="81a2c-229">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="81a2c-230">Il comando [dotnet run](/dotnet/core/tools/dotnet-run) usa il file *Properties/launchSettings.json* dell'app, che configura l'app per l'ascolto negli URL specificati dalla proprietà `applicationUrl` , ad esempio `https://localhost:5001;http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="81a2c-230">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="81a2c-231">Configurare l'app per l'uso di un certificato nello sviluppo per il comando `dotnet run` o l'ambiente di sviluppo (F5 o CTRL+F5 in Visual Studio Code) usando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="81a2c-231">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="81a2c-232">[Sostituire il certificato predefinito della configurazione](xref:fundamentals/servers/kestrel#configuration) ( *consigliato* )</span><span class="sxs-lookup"><span data-stu-id="81a2c-232">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) ( *Recommended* )</span></span>
* [<span data-ttu-id="81a2c-233">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="81a2c-233">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="81a2c-234">**Configurare il proxy inverso per connessioni client sicure (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="81a2c-234">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

<span data-ttu-id="81a2c-235">Per configurare Apache per HTTPS, viene usato il modulo *mod_ssl* .</span><span class="sxs-lookup"><span data-stu-id="81a2c-235">To configure Apache for HTTPS, the *mod_ssl* module is used.</span></span> <span data-ttu-id="81a2c-236">Durante l'installazione del modulo *httpd* , è stato installato anche il modulo *mod_ssl* .</span><span class="sxs-lookup"><span data-stu-id="81a2c-236">When the *httpd* module was installed, the *mod_ssl* module was also installed.</span></span> <span data-ttu-id="81a2c-237">Se non è stato installato, usare `yum` per aggiungerlo alla configurazione.</span><span class="sxs-lookup"><span data-stu-id="81a2c-237">If it wasn't installed, use `yum` to add it to the configuration.</span></span>

```bash
sudo yum install mod_ssl
```

<span data-ttu-id="81a2c-238">Per imporre HTTPS, installare il modulo `mod_rewrite` per abilitare la riscrittura degli URL:</span><span class="sxs-lookup"><span data-stu-id="81a2c-238">To enforce HTTPS, install the `mod_rewrite` module to enable URL rewriting:</span></span>

```bash
sudo yum install mod_rewrite
```

<span data-ttu-id="81a2c-239">Modificare il file *helloapp.conf* per abilitare la riscrittura degli URL e proteggere la comunicazione sulla porta 443:</span><span class="sxs-lookup"><span data-stu-id="81a2c-239">Modify the *helloapp.conf* file to enable URL rewriting and secure communication on port 443:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> <span data-ttu-id="81a2c-240">Questo esempio usa un certificato generato localmente.</span><span class="sxs-lookup"><span data-stu-id="81a2c-240">This example is using a locally-generated certificate.</span></span> <span data-ttu-id="81a2c-241">**SSLCertificateFile** deve essere il file del certificato primario per il nome di dominio.</span><span class="sxs-lookup"><span data-stu-id="81a2c-241">**SSLCertificateFile** should be the primary certificate file for the domain name.</span></span> <span data-ttu-id="81a2c-242">**SSLCertificateKeyFile** deve essere il file di chiave generato quando viene creato il CSR.</span><span class="sxs-lookup"><span data-stu-id="81a2c-242">**SSLCertificateKeyFile** should be the key file generated when CSR is created.</span></span> <span data-ttu-id="81a2c-243">**SSLCertificateChainFile** deve essere il file di certificato intermedio (se presente) che è stato fornito dall'autorità di certificazione.</span><span class="sxs-lookup"><span data-stu-id="81a2c-243">**SSLCertificateChainFile** should be the intermediate certificate file (if any) that was supplied by the certificate authority.</span></span>

<span data-ttu-id="81a2c-244">Salvare il file e testare la configurazione:</span><span class="sxs-lookup"><span data-stu-id="81a2c-244">Save the file and test the configuration:</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="81a2c-245">Riavviare Apache:</span><span class="sxs-lookup"><span data-stu-id="81a2c-245">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a><span data-ttu-id="81a2c-246">Altri suggerimenti di Apache</span><span class="sxs-lookup"><span data-stu-id="81a2c-246">Additional Apache suggestions</span></span>

### <a name="restart-apps-with-shared-framework-updates"></a><span data-ttu-id="81a2c-247">Riavviare le app con gli aggiornamenti del Framework condiviso</span><span class="sxs-lookup"><span data-stu-id="81a2c-247">Restart apps with shared framework updates</span></span>

<span data-ttu-id="81a2c-248">Dopo aver aggiornato il Framework condiviso sul server, riavviare le app ASP.NET Core ospitate dal server.</span><span class="sxs-lookup"><span data-stu-id="81a2c-248">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

### <a name="additional-headers"></a><span data-ttu-id="81a2c-249">Intestazioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="81a2c-249">Additional headers</span></span>

<span data-ttu-id="81a2c-250">Al fine di garantire la protezione dagli attacchi dannosi, vi sono alcune intestazioni che devono essere modificate o aggiunte.</span><span class="sxs-lookup"><span data-stu-id="81a2c-250">In order to secure against malicious attacks, there are a few headers that should either be modified or added.</span></span> <span data-ttu-id="81a2c-251">Verificare che il modulo `mod_headers` sia installato:</span><span class="sxs-lookup"><span data-stu-id="81a2c-251">Ensure that the `mod_headers` module is installed:</span></span>

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a><span data-ttu-id="81a2c-252">Proteggere Apache dagli attacchi di clickjacking</span><span class="sxs-lookup"><span data-stu-id="81a2c-252">Secure Apache from clickjacking attacks</span></span>

<span data-ttu-id="81a2c-253">Il [clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), anche noto come *attacco UI Redress* , è un attacco dannoso in cui un visitatore di un sito Web viene indotto a fare clic su un collegamento o un pulsante in una pagina diversa da quella che sta attualmente visualizzando.</span><span class="sxs-lookup"><span data-stu-id="81a2c-253">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack* , is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="81a2c-254">Usare `X-FRAME-OPTIONS` per proteggere il sito.</span><span class="sxs-lookup"><span data-stu-id="81a2c-254">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="81a2c-255">Per contrastare gli attacchi di clickjacking:</span><span class="sxs-lookup"><span data-stu-id="81a2c-255">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="81a2c-256">Modificare il file *httpd.conf* :</span><span class="sxs-lookup"><span data-stu-id="81a2c-256">Edit the *httpd.conf* file:</span></span>

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   <span data-ttu-id="81a2c-257">Aggiungere la riga `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span><span class="sxs-lookup"><span data-stu-id="81a2c-257">Add the line `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span></span>
1. <span data-ttu-id="81a2c-258">Salvare il file.</span><span class="sxs-lookup"><span data-stu-id="81a2c-258">Save the file.</span></span>
1. <span data-ttu-id="81a2c-259">Riavviare Apache.</span><span class="sxs-lookup"><span data-stu-id="81a2c-259">Restart Apache.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="81a2c-260">Analisi del tipo MIME</span><span class="sxs-lookup"><span data-stu-id="81a2c-260">MIME-type sniffing</span></span>

<span data-ttu-id="81a2c-261">L'intestazione `X-Content-Type-Options` impedisce a Internet Explorer di eseguire l' *analisi del tipo MIME* , ovvero di determinare il `Content-Type` di un file dal contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="81a2c-261">The `X-Content-Type-Options` header prevents Internet Explorer from *MIME-sniffing* (determining a file's `Content-Type` from the file's content).</span></span> <span data-ttu-id="81a2c-262">Se il server imposta l'intestazione `Content-Type` su `text/html` con l'opzione `nosniff` impostata, Internet Explorer esegue il rendering del contenuto come `text/html`, indipendentemente dal contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="81a2c-262">If the server sets the `Content-Type` header to `text/html` with the `nosniff` option set, Internet Explorer renders the content as `text/html` regardless of the file's content.</span></span>

<span data-ttu-id="81a2c-263">Modificare il file *httpd.conf* :</span><span class="sxs-lookup"><span data-stu-id="81a2c-263">Edit the *httpd.conf* file:</span></span>

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="81a2c-264">Aggiungere la riga `Header set X-Content-Type-Options "nosniff"`.</span><span class="sxs-lookup"><span data-stu-id="81a2c-264">Add the line `Header set X-Content-Type-Options "nosniff"`.</span></span> <span data-ttu-id="81a2c-265">Salvare il file.</span><span class="sxs-lookup"><span data-stu-id="81a2c-265">Save the file.</span></span> <span data-ttu-id="81a2c-266">Riavviare Apache.</span><span class="sxs-lookup"><span data-stu-id="81a2c-266">Restart Apache.</span></span>

### <a name="load-balancing"></a><span data-ttu-id="81a2c-267">Bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="81a2c-267">Load Balancing</span></span>

<span data-ttu-id="81a2c-268">Questo esempio illustra come impostare e configurare Apache CentOS 7 e Kestrel nello stesso computer dell'istanza.</span><span class="sxs-lookup"><span data-stu-id="81a2c-268">This example shows how to setup and configure Apache on CentOS 7 and Kestrel on the same instance machine.</span></span> <span data-ttu-id="81a2c-269">Per evitare di avere un singolo punto di errore, l'uso di *mod_proxy_balancer* e la modifica di **VirtualHost** consentono la gestione di più istanze delle app Web dietro il server proxy di Apache.</span><span class="sxs-lookup"><span data-stu-id="81a2c-269">In order to not have a single point of failure; using *mod_proxy_balancer* and modifying the **VirtualHost** would allow for managing multiple instances of the web apps behind the Apache proxy server.</span></span>

```bash
sudo yum install mod_proxy_balancer
```

<span data-ttu-id="81a2c-270">Nel file di configurazione illustrato di seguito, viene configurata un'istanza aggiuntiva di `helloapp` da eseguire sulla porta 5001.</span><span class="sxs-lookup"><span data-stu-id="81a2c-270">In the configuration file shown below, an additional instance of the `helloapp` is set up to run on port 5001.</span></span> <span data-ttu-id="81a2c-271">La sezione *Proxy* è impostata con una configurazione del servizio di bilanciamento del carico, con due membri per il bilanciamento del carico di *byrequests* .</span><span class="sxs-lookup"><span data-stu-id="81a2c-271">The *Proxy* section is set with a balancer configuration with two members to load balance *byrequests* .</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a><span data-ttu-id="81a2c-272">Limiti di velocità</span><span class="sxs-lookup"><span data-stu-id="81a2c-272">Rate Limits</span></span>

<span data-ttu-id="81a2c-273">Usando *mod_ratelimit* , incluso nel modulo *httpd* , è possibile limitare la larghezza di banda del client:</span><span class="sxs-lookup"><span data-stu-id="81a2c-273">Using *mod_ratelimit* , which is included in the *httpd* module, the bandwidth of clients can be limited:</span></span>

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

<span data-ttu-id="81a2c-274">Il file di esempio limita la larghezza di banda a 600 KB al secondo nel percorso radice:</span><span class="sxs-lookup"><span data-stu-id="81a2c-274">The example file limits bandwidth as 600 KB/sec under the root location:</span></span>

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a><span data-ttu-id="81a2c-275">Campi di intestazione della richiesta di grandi dimensioni</span><span class="sxs-lookup"><span data-stu-id="81a2c-275">Long request header fields</span></span>

<span data-ttu-id="81a2c-276">Le impostazioni predefinite del server proxy limitano in genere i campi di intestazione della richiesta a 8.190 byte.</span><span class="sxs-lookup"><span data-stu-id="81a2c-276">Proxy server default settings typically limit request header fields to 8,190 bytes.</span></span> <span data-ttu-id="81a2c-277">Un'app può richiedere campi più lunghi del valore predefinito, ad esempio le app che usano [Azure Active Directory](https://azure.microsoft.com/services/active-directory/).</span><span class="sxs-lookup"><span data-stu-id="81a2c-277">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="81a2c-278">Se sono necessari campi più lunghi, la direttiva [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) del server proxy richiede la regolazione.</span><span class="sxs-lookup"><span data-stu-id="81a2c-278">If longer fields are required, the proxy server's [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) directive requires adjustment.</span></span> <span data-ttu-id="81a2c-279">Il valore da applicare dipende dallo scenario.</span><span class="sxs-lookup"><span data-stu-id="81a2c-279">The value to apply depends on the scenario.</span></span> <span data-ttu-id="81a2c-280">Per altre informazioni, vedere la documentazione del server.</span><span class="sxs-lookup"><span data-stu-id="81a2c-280">For more information, see your server's documentation.</span></span>

> [!WARNING]
> <span data-ttu-id="81a2c-281">Aumentare il valore predefinito di `LimitRequestFieldSize` solo se necessario.</span><span class="sxs-lookup"><span data-stu-id="81a2c-281">Don't increase the default value of `LimitRequestFieldSize` unless necessary.</span></span> <span data-ttu-id="81a2c-282">Se si aumenta il valore, si aumenta il rischio di sovraccarico del buffer (overflow) e di attacchi Denial of Service (DoS) da parte di utenti malintenzionati.</span><span class="sxs-lookup"><span data-stu-id="81a2c-282">Increasing the value increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="81a2c-283">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="81a2c-283">Additional resources</span></span>

* [<span data-ttu-id="81a2c-284">Prerequisiti per .NET Core in Linux</span><span class="sxs-lookup"><span data-stu-id="81a2c-284">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
