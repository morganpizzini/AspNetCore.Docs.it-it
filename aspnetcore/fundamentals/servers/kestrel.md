---
title: Implementazione del server Web Kestrel in ASP.NET Core
author: rick-anderson
description: Informazioni su Kestrel, il server Web multipiattaforma per ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
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
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 44558a0f2fdc61eb860223658f5bef1d0117ba87
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653946"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="baeb6-103">Implementazione del server Web Kestrel in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="baeb6-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="baeb6-104">Di [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) e [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="baeb6-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="baeb6-105">Kestrel è un [server Web per ASP.NET Core](xref:fundamentals/servers/index) multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="baeb6-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="baeb6-106">Kestrel è il server Web incluso per impostazione predefinita nei modelli di progetto di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="baeb6-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="baeb6-107">Kestrel supporta gli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="baeb6-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="baeb6-108">HTTPS</span></span>
* <span data-ttu-id="baeb6-109">Aggiornamento opaco usato per abilitare [WebSocket](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="baeb6-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="baeb6-110">Socket Unix ad alte prestazioni dietro Nginx</span><span class="sxs-lookup"><span data-stu-id="baeb6-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="baeb6-111">HTTP/2 (tranne che in macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="baeb6-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="baeb6-112">&dagger;HTTP/2 verrà supportato in macOS in una versione futura.</span><span class="sxs-lookup"><span data-stu-id="baeb6-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="baeb6-113">Kestrel è supportato in tutte le piattaforme e le versioni supportate da .NET Core.</span><span class="sxs-lookup"><span data-stu-id="baeb6-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="baeb6-114">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="baeb6-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="baeb6-115">Supporto HTTP/2</span><span class="sxs-lookup"><span data-stu-id="baeb6-115">HTTP/2 support</span></span>

<span data-ttu-id="baeb6-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) è disponibile per le app ASP.NET Core se vengono soddisfatti i requisiti di base seguenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="baeb6-117">Sistema operativo&dagger;</span><span class="sxs-lookup"><span data-stu-id="baeb6-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="baeb6-118">Windows Server 2016/Windows 10 o versioni successive&Dagger;</span><span class="sxs-lookup"><span data-stu-id="baeb6-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="baeb6-119">Linux con OpenSSL 1.0.2 o versioni successive (ad esempio, Ubuntu 16.04 o versioni successive)</span><span class="sxs-lookup"><span data-stu-id="baeb6-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="baeb6-120">Framework di destinazione: .NET Core 2.2 o versioni successive</span><span class="sxs-lookup"><span data-stu-id="baeb6-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="baeb6-121">Connessione [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="baeb6-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="baeb6-122">Connessione TLS 1.2 o successiva</span><span class="sxs-lookup"><span data-stu-id="baeb6-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="baeb6-123">&dagger;HTTP/2 verrà supportato in macOS in una versione futura.</span><span class="sxs-lookup"><span data-stu-id="baeb6-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="baeb6-124">&Dagger;Kestrel ha un supporto limitato per HTTP/2 in Windows Server 2012 R2 e Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="baeb6-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="baeb6-125">Il supporto è limitato perché l'elenco di suite di crittografia TLS supportate disponibili in questi sistemi operativi è limitato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="baeb6-126">Un certificato generato con un algoritmo ECDSA potrebbe essere necessario per proteggere le connessioni TLS.</span><span class="sxs-lookup"><span data-stu-id="baeb6-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="baeb6-127">Se viene stabilita una connessione HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) corrisponde a `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="baeb6-128">HTTP/2 è disabilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="baeb6-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="baeb6-129">Per altre informazioni sulla configurazione, vedere le sezioni [Opzioni Kestrel](#kestrel-options) e [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="baeb6-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="baeb6-130">Quando usare Kestrel con un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="baeb6-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="baeb6-131">È possibile usare Kestrel da solo o in combinazione con un *server proxy inverso*, ad esempio [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="baeb6-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="baeb6-132">Il server proxy inverso riceve le richieste HTTP dalla rete e le inoltra a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="baeb6-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="baeb6-133">Kestrel usato come server Web perimetrale (esposto a Internet):</span><span class="sxs-lookup"><span data-stu-id="baeb6-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel comunica direttamente con Internet senza un server proxy inverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="baeb6-135">Kestrel usato in una configurazione proxy inverso:</span><span class="sxs-lookup"><span data-stu-id="baeb6-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel comunica indirettamente con Internet attraverso un server proxy inverso, ad esempio IIS, Nginx o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="baeb6-137">La configurazione, con o senza un server proxy inverso, è una configurazione di hosting supportata.</span><span class="sxs-lookup"><span data-stu-id="baeb6-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="baeb6-138">Se viene utilizzato come server perimetrale in assenza di un server proxy inverso, Kestrel non supporta la condivisione tra più processi dell'IP e della porta.</span><span class="sxs-lookup"><span data-stu-id="baeb6-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="baeb6-139">Quando è configurato per restare in ascolto su una porta, Kestrel gestisce tutto il traffico per tale porta indipendentemente dalle intestazioni `Host` delle richieste.</span><span class="sxs-lookup"><span data-stu-id="baeb6-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="baeb6-140">Un proxy inverso che può condividere le porte può eseguire l'inoltro delle richieste a Kestrel su un unico IP e un'unica porta.</span><span class="sxs-lookup"><span data-stu-id="baeb6-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="baeb6-141">Anche se la presenza di un server proxy inverso non è necessaria, può risultare utile per i motivi seguenti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="baeb6-142">Un proxy inverso:</span><span class="sxs-lookup"><span data-stu-id="baeb6-142">A reverse proxy:</span></span>

* <span data-ttu-id="baeb6-143">Può limitare l'area della superficie di attacco pubblica esposta delle app ospitate.</span><span class="sxs-lookup"><span data-stu-id="baeb6-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="baeb6-144">Offre un livello aggiuntivo di configurazione e protezione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="baeb6-145">Potrebbe offrire un'integrazione migliore con l'infrastruttura esistente.</span><span class="sxs-lookup"><span data-stu-id="baeb6-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="baeb6-146">Semplifica il bilanciamento del carico e la configurazione di comunicazioni protette (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="baeb6-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="baeb6-147">Solo il server proxy inverso richiede un certificato X. 509 e il server è in grado di comunicare con i server dell'app nella rete interna tramite HTTP normale.</span><span class="sxs-lookup"><span data-stu-id="baeb6-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="baeb6-148">Una configurazione che prevede un proxy inverso richiede il [filtro host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="baeb6-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="baeb6-149">Gheppio nelle app ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="baeb6-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="baeb6-150">I modelli di progetto ASP.NET Core usano Kestrel per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="baeb6-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="baeb6-151">In *Program.cs*il <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> metodo chiama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="baeb6-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="baeb6-152">Per ulteriori informazioni sulla compilazione dell'host, vedere le sezioni configurare le impostazioni di *un host* e di un *generatore predefinito* di <xref:fundamentals/host/generic-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="baeb6-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="baeb6-153">Per fornire una configurazione aggiuntiva dopo la chiamata di `ConfigureWebHostDefaults`, usare `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="baeb6-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

## <a name="kestrel-options"></a><span data-ttu-id="baeb6-154">Opzioni Kestrel</span><span class="sxs-lookup"><span data-stu-id="baeb6-154">Kestrel options</span></span>

<span data-ttu-id="baeb6-155">Il server Web Kestrel dispone di opzioni di configurazione dei vincoli che risultano particolarmente utili nelle distribuzioni con connessione Internet.</span><span class="sxs-lookup"><span data-stu-id="baeb6-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="baeb6-156">Impostare i vincoli per la proprietà <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> della classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="baeb6-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="baeb6-157">La proprietà `Limits` contiene un'istanza della classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="baeb6-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="baeb6-158">Negli esempi seguenti viene usato lo spazio dei nomi <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="baeb6-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="baeb6-159">Negli esempi illustrati più avanti in questo articolo, le opzioni di gheppio sono configurate nel codice C#.</span><span class="sxs-lookup"><span data-stu-id="baeb6-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="baeb6-160">È inoltre possibile impostare le opzioni di Gheppio utilizzando un [provider di configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="baeb6-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="baeb6-161">Il [provider di configurazione file](xref:fundamentals/configuration/index#file-configuration-provider) , ad esempio, può caricare la configurazione di Gheppio da un *appsettings.jssu* o *appSettings. { File Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="baeb6-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <span data-ttu-id="baeb6-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> e la [configurazione dell'endpoint](#endpoint-configuration) sono configurabili dai provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="baeb6-163">La configurazione del gheppio rimanente deve essere configurata nel codice C#.</span><span class="sxs-lookup"><span data-stu-id="baeb6-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="baeb6-164">Usare **uno** degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="baeb6-165">Configurare gheppio in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="baeb6-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="baeb6-166">Inserire un'istanza di `IConfiguration` nella `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="baeb6-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="baeb6-167">Nell'esempio seguente si presuppone che la configurazione inserita venga assegnata alla `Configuration` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="baeb6-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="baeb6-168">In `Startup.ConfigureServices` caricare la `Kestrel` sezione della configurazione nella configurazione di Gheppio:</span><span class="sxs-lookup"><span data-stu-id="baeb6-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="baeb6-169">Configurare il gheppio durante la compilazione dell'host:</span><span class="sxs-lookup"><span data-stu-id="baeb6-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="baeb6-170">In *Program.cs*caricare la `Kestrel` sezione della configurazione nella configurazione di Gheppio:</span><span class="sxs-lookup"><span data-stu-id="baeb6-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="baeb6-171">Entrambi gli approcci precedenti funzionano con qualsiasi [provider di configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="baeb6-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="baeb6-172">Timeout keep-alive</span><span class="sxs-lookup"><span data-stu-id="baeb6-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="baeb6-173">Ottiene o imposta il [timeout keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="baeb6-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="baeb6-174">Il valore predefinito è 2 minuti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="baeb6-175">Numero massimo di connessioni client</span><span class="sxs-lookup"><span data-stu-id="baeb6-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="baeb6-176">È possibile impostare il numero massimo di connessioni TCP aperte simultaneamente per l'intera app con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="baeb6-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="baeb6-177">È previsto un limite separato per le connessioni che sono state aggiornate da HTTP o HTTPS a un altro protocollo (ad esempio su una richiesta WebSocket).</span><span class="sxs-lookup"><span data-stu-id="baeb6-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="baeb6-178">Dopo l'aggiornamento di una connessione, questa non viene conteggiata per il limite `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="baeb6-179">Per impostazione predefinita, il numero massimo di connessioni è illimitato (null).</span><span class="sxs-lookup"><span data-stu-id="baeb6-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="baeb6-180">Dimensione massima del corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="baeb6-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="baeb6-181">La dimensione massima predefinita del corpo della richiesta è pari a 30.000.000 di byte, equivalenti a circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="baeb6-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="baeb6-182">Il metodo consigliato per ignorare il limite in un'app ASP.NET Core MVC è l'uso dell'attributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> in un metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="baeb6-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="baeb6-183">L'esempio seguente illustra come configurare il vincolo per l'app in ogni richiesta:</span><span class="sxs-lookup"><span data-stu-id="baeb6-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="baeb6-184">Eseguire l'override dell'impostazione per una richiesta specifica nel middleware:</span><span class="sxs-lookup"><span data-stu-id="baeb6-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="baeb6-185">Viene generata un'eccezione se l'app configura il limite per una richiesta dopo che l'app ha iniziato a leggere la richiesta.</span><span class="sxs-lookup"><span data-stu-id="baeb6-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="baeb6-186">Una proprietà `IsReadOnly` indica se la proprietà `MaxRequestBodySize` è in stato di sola lettura e pertanto è troppo tardi per configurare il limite.</span><span class="sxs-lookup"><span data-stu-id="baeb6-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="baeb6-187">Quando un'app viene eseguita [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) dietro il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), il limite della dimensione del corpo della richiesta di Kestrel è disabilitato perché viene già impostato da IIS.</span><span class="sxs-lookup"><span data-stu-id="baeb6-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="baeb6-188">Velocità minima dei dati del corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="baeb6-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="baeb6-189">Kestrel controlla ogni secondo se i dati arrivano alla velocità in byte al secondo specificata.</span><span class="sxs-lookup"><span data-stu-id="baeb6-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="baeb6-190">Se la frequenza scende sotto il valore minimo, si è verificato il timeout della connessione. Il periodo di tolleranza è la quantità di tempo che il gheppio concede al client di aumentare la velocità di invio fino al minimo. la frequenza non viene controllata durante tale periodo di tempo.</span><span class="sxs-lookup"><span data-stu-id="baeb6-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="baeb6-191">Il periodo di prova consente di evitare l'interruzione di connessioni che inizialmente inviano i dati a velocità ridotta a causa dell'avvio lento del protocollo TCP.</span><span class="sxs-lookup"><span data-stu-id="baeb6-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="baeb6-192">La velocità minima predefinita è di 240 byte al secondo, con un periodo di tolleranza di 5 secondi.</span><span class="sxs-lookup"><span data-stu-id="baeb6-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="baeb6-193">Anche per la risposta è prevista una velocità minima.</span><span class="sxs-lookup"><span data-stu-id="baeb6-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="baeb6-194">Il codice per impostare il limite della richiesta e della risposta è identico e varia solo per `RequestBody` o `Response` nei nomi di proprietà e di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="baeb6-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="baeb6-195">L'esempio seguente visualizza come configurare la velocità minima dei dati in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="baeb6-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="baeb6-196">Ignorare i limiti di velocità minima per ogni richiesta nel middleware:</span><span class="sxs-lookup"><span data-stu-id="baeb6-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="baeb6-197">La funzionalità <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> a cui si fa riferimento nell'esempio precedente non è presente in `HttpContext.Features` per le richieste HTTP/2, perché la modifica dei limiti di velocità per ogni richiesta in genere non è supportata per HTTP/2 a causa del supporto del protocollo per il multiplexing delle richieste.</span><span class="sxs-lookup"><span data-stu-id="baeb6-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="baeb6-198">La funzionalità <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> è tuttavia ancora presente in `HttpContext.Features` per le richieste HTTP/2, perché il limite di velocità di lettura può comunque essere *disabilitato interamente* per ogni singola richiesta impostando `IHttpMinRequestBodyDataRateFeature.MinDataRate` su `null` anche per una richiesta HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="baeb6-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="baeb6-199">Il tentativo di leggere `IHttpMinRequestBodyDataRateFeature.MinDataRate` o il tentativo di impostazione di un valore diverso da `null` causerà la generazione di una `NotSupportedException` per una richiesta HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="baeb6-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="baeb6-200">I limiti di velocità a livello di server configurati tramite `KestrelServerOptions.Limits` vengono tuttavia applicati alle connessioni HTTP/1.x e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="baeb6-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="baeb6-201">Timeout delle intestazioni delle richieste</span><span class="sxs-lookup"><span data-stu-id="baeb6-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="baeb6-202">Ottiene o imposta la quantità massima di tempo che il server dedica alla ricezione delle intestazioni delle richieste.</span><span class="sxs-lookup"><span data-stu-id="baeb6-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="baeb6-203">Il valore predefinito è 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="baeb6-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="baeb6-204">Numero massimo di flussi per connessione</span><span class="sxs-lookup"><span data-stu-id="baeb6-204">Maximum streams per connection</span></span>

<span data-ttu-id="baeb6-205">`Http2.MaxStreamsPerConnection` limita il numero di flussi di richieste simultanee per ogni connessione HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="baeb6-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="baeb6-206">I flussi in eccesso vengono rifiutati.</span><span class="sxs-lookup"><span data-stu-id="baeb6-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="baeb6-207">Il valore predefinito è 100.</span><span class="sxs-lookup"><span data-stu-id="baeb6-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="baeb6-208">Dimensioni della tabella delle intestazioni</span><span class="sxs-lookup"><span data-stu-id="baeb6-208">Header table size</span></span>

<span data-ttu-id="baeb6-209">Il decodificatore HPACK decomprime le intestazioni HTTP per le connessioni HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="baeb6-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="baeb6-210">`Http2.HeaderTableSize` limita le dimensioni della tabella di compressione delle intestazioni usata dal decodificatore HPACK.</span><span class="sxs-lookup"><span data-stu-id="baeb6-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="baeb6-211">Il valore viene specificato in ottetti e deve essere maggiore di zero (0).</span><span class="sxs-lookup"><span data-stu-id="baeb6-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="baeb6-212">Il valore predefinito è 4096.</span><span class="sxs-lookup"><span data-stu-id="baeb6-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="baeb6-213">Dimensione massima del frame</span><span class="sxs-lookup"><span data-stu-id="baeb6-213">Maximum frame size</span></span>

<span data-ttu-id="baeb6-214">`Http2.MaxFrameSize` indica la dimensione massima consentita di un payload del frame di connessione HTTP/2 ricevuto o inviato dal server.</span><span class="sxs-lookup"><span data-stu-id="baeb6-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="baeb6-215">Il valore viene specificato in ottetti e deve essere compreso tra 2^14 (16.384) e 2^24-1 (16.777.215).</span><span class="sxs-lookup"><span data-stu-id="baeb6-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="baeb6-216">Il valore predefinito è 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="baeb6-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="baeb6-217">Dimensioni massime dell'intestazione della richiesta</span><span class="sxs-lookup"><span data-stu-id="baeb6-217">Maximum request header size</span></span>

<span data-ttu-id="baeb6-218">`Http2.MaxRequestHeaderFieldSize` indica le dimensioni massime consentite in ottetti di valori di intestazione di richiesta.</span><span class="sxs-lookup"><span data-stu-id="baeb6-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="baeb6-219">Questo limite si applica sia al nome che al valore nelle rappresentazioni compresse e non compresse.</span><span class="sxs-lookup"><span data-stu-id="baeb6-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="baeb6-220">Il valore deve essere maggiore di zero (0).</span><span class="sxs-lookup"><span data-stu-id="baeb6-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="baeb6-221">Il valore predefinito è 8.192.</span><span class="sxs-lookup"><span data-stu-id="baeb6-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="baeb6-222">Dimensioni della finestra di connessione iniziali</span><span class="sxs-lookup"><span data-stu-id="baeb6-222">Initial connection window size</span></span>

<span data-ttu-id="baeb6-223">`Http2.InitialConnectionWindowSize` indica il numero massimo di byte di dati del corpo della richiesta che il server memorizza nel buffer in una volta, aggregati in tutte le richieste (flussi), per ogni connessione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="baeb6-224">Le richieste sono anche limitate da `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="baeb6-225">Il valore deve essere maggiore di o uguale a 65.535 e minore di 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="baeb6-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="baeb6-226">Il valore predefinito è 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="baeb6-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="baeb6-227">Dimensioni della finestra di flusso iniziali</span><span class="sxs-lookup"><span data-stu-id="baeb6-227">Initial stream window size</span></span>

<span data-ttu-id="baeb6-228">`Http2.InitialStreamWindowSize` indica il numero massimo di byte di dati del corpo della richiesta che il server memorizza nel buffer in una volta per ogni richiesta (flusso).</span><span class="sxs-lookup"><span data-stu-id="baeb6-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="baeb6-229">Le richieste sono anche limitate da `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="baeb6-230">Il valore deve essere maggiore di o uguale a 65.535 e minore di 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="baeb6-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="baeb6-231">Il valore predefinito è 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="baeb6-231">The default value is 96 KB (98,304).</span></span>

### <a name="trailers"></a><span data-ttu-id="baeb6-232">Trailer</span><span class="sxs-lookup"><span data-stu-id="baeb6-232">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="baeb6-233">Reset</span><span class="sxs-lookup"><span data-stu-id="baeb6-233">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="baeb6-234">I/O sincrono</span><span class="sxs-lookup"><span data-stu-id="baeb6-234">Synchronous I/O</span></span>

<span data-ttu-id="baeb6-235"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> Controlla se è consentito l'I/O sincrono per la richiesta e la risposta.</span><span class="sxs-lookup"><span data-stu-id="baeb6-235"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="baeb6-236">Il valore predefinito è `false`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-236">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="baeb6-237">Un numero elevato di operazioni di I/O sincrone bloccanti può causare l'esaurimento del pool di thread, il che rende l'app non risponde.</span><span class="sxs-lookup"><span data-stu-id="baeb6-237">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="baeb6-238">Abilitare solo `AllowSynchronousIO` quando si usa una libreria che non supporta l'I/O asincrono.</span><span class="sxs-lookup"><span data-stu-id="baeb6-238">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="baeb6-239">L'esempio seguente abilita l'I/O sincrono:</span><span class="sxs-lookup"><span data-stu-id="baeb6-239">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="baeb6-240">Per informazioni su altre opzioni e limiti di Kestrel, vedere:</span><span class="sxs-lookup"><span data-stu-id="baeb6-240">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="baeb6-241">Configurazione dell'endpoint</span><span class="sxs-lookup"><span data-stu-id="baeb6-241">Endpoint configuration</span></span>

<span data-ttu-id="baeb6-242">Per impostazione predefinita, ASP.NET Core è associato a:</span><span class="sxs-lookup"><span data-stu-id="baeb6-242">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="baeb6-243">`https://localhost:5001` (quando è presente un certificato di sviluppo locale)</span><span class="sxs-lookup"><span data-stu-id="baeb6-243">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="baeb6-244">Specificare gli URL usando gli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-244">Specify URLs using the:</span></span>

* <span data-ttu-id="baeb6-245">La variabile di ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-245">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="baeb6-246">L'argomento della riga di comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-246">`--urls` command-line argument.</span></span>
* <span data-ttu-id="baeb6-247">La chiave di configurazione dell'host `urls`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-247">`urls` host configuration key.</span></span>
* <span data-ttu-id="baeb6-248">Il metodo di estensione `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-248">`UseUrls` extension method.</span></span>

<span data-ttu-id="baeb6-249">Il valore specificato usando i metodi seguenti può essere uno o più endpoint HTTP e HTTPS (HTTPS se è disponibile un certificato predefinito).</span><span class="sxs-lookup"><span data-stu-id="baeb6-249">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="baeb6-250">Configurare il valore come un elenco delimitato da punto e virgola (ad esempio, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="baeb6-250">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="baeb6-251">Per altre informazioni su questi approcci, vedere [URL del server](xref:fundamentals/host/web-host#server-urls) e [Override della configurazione](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="baeb6-251">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="baeb6-252">Viene creato un certificato di sviluppo nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-252">A development certificate is created:</span></span>

* <span data-ttu-id="baeb6-253">Quando viene installato [.NET Core SDK](/dotnet/core/sdk).</span><span class="sxs-lookup"><span data-stu-id="baeb6-253">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="baeb6-254">Quando per creare un certificato viene usato [lo strumento dev-certs](xref:aspnetcore-2.1#https).</span><span class="sxs-lookup"><span data-stu-id="baeb6-254">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="baeb6-255">Alcuni browser richiedono la concessione di autorizzazioni esplicite per considerare attendibile il certificato di sviluppo locale.</span><span class="sxs-lookup"><span data-stu-id="baeb6-255">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="baeb6-256">I modelli di progetto consentono di configurare le app per l'esecuzione su HTTPS per impostazione predefinita e includono il [reindirizzamento HTTPS e il supporto HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="baeb6-256">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="baeb6-257">Chiamare i metodi <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> oppure <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> su <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> per configurare le porte e i prefissi URL per Kestrel.</span><span class="sxs-lookup"><span data-stu-id="baeb6-257">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="baeb6-258">Anche `UseUrls`, l'argomento della riga di comando `--urls`, la chiave di configurazione dell'host `urls` e la variabile di ambiente `ASPNETCORE_URLS` funzionano, ma con le limitazioni indicate più avanti nella sezione (deve essere disponibile un certificato predefinito per la configurazione dell'endopoint HTTPS).</span><span class="sxs-lookup"><span data-stu-id="baeb6-258">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="baeb6-259">`KestrelServerOptions` configurazione</span><span class="sxs-lookup"><span data-stu-id="baeb6-259">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="baeb6-260">ConfigureEndpointDefaults (azione \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="baeb6-260">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="baeb6-261">Specifica un elemento di configurazione `Action` da eseguire per ogni endpoint specificato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-261">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="baeb6-262">Se si chiama `ConfigureEndpointDefaults` più volte, gli elementi `Action` precedenti vengono sostituiti con l'ultimo elemento `Action` specificato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-262">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="baeb6-263">Per gli endpoint creati chiamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **prima** della chiamata a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> non verranno applicati i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-263">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="baeb6-264">ConfigureHttpsDefaults (azione \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="baeb6-264">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="baeb6-265">Specifica un elemento di configurazione `Action` da eseguire per ogni endpoint HTTPS.</span><span class="sxs-lookup"><span data-stu-id="baeb6-265">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="baeb6-266">Se si chiama `ConfigureHttpsDefaults` più volte, gli elementi `Action` precedenti vengono sostituiti con l'ultimo elemento `Action` specificato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-266">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="baeb6-267">Per gli endpoint creati chiamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **prima** della chiamata a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> non verranno applicati i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-267">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="baeb6-268">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="baeb6-268">Configure(IConfiguration)</span></span>

<span data-ttu-id="baeb6-269">Crea un loader di configurazione per la configurazione di Kestrel che accetta un elemento <xref:Microsoft.Extensions.Configuration.IConfiguration> come input.</span><span class="sxs-lookup"><span data-stu-id="baeb6-269">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="baeb6-270">L'ambito della configurazione deve essere la sezione di configurazione per Kestrel.</span><span class="sxs-lookup"><span data-stu-id="baeb6-270">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="baeb6-271">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="baeb6-271">ListenOptions.UseHttps</span></span>

<span data-ttu-id="baeb6-272">Configura Kestrel per l'uso di HTTPS.</span><span class="sxs-lookup"><span data-stu-id="baeb6-272">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="baeb6-273">Estensioni `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="baeb6-273">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="baeb6-274">`UseHttps`: Configurare gheppio per l'uso di HTTPS con il certificato predefinito.</span><span class="sxs-lookup"><span data-stu-id="baeb6-274">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="baeb6-275">Genera un'eccezione se non è stato configurato alcun certificato predefinito.</span><span class="sxs-lookup"><span data-stu-id="baeb6-275">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="baeb6-276">Parametri `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="baeb6-276">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="baeb6-277">`filename` è il percorso e il nome file di un file di certificato, relativo alla directory che contiene i file di contenuto dell'app.</span><span class="sxs-lookup"><span data-stu-id="baeb6-277">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="baeb6-278">`password` è la password richiesta per accedere ai dati del certificato X.509.</span><span class="sxs-lookup"><span data-stu-id="baeb6-278">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="baeb6-279">`configureOptions` è un elemento `Action` per configurare `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-279">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="baeb6-280">Restituisce `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-280">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="baeb6-281">`storeName` è l'archivio certificati da cui caricare il certificato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-281">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="baeb6-282">`subject` è il nome dell'oggetto del certificato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-282">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="baeb6-283">`allowInvalid` indica se devono essere considerati i certificati non validi, come ad esempio i certificati autofirmati.</span><span class="sxs-lookup"><span data-stu-id="baeb6-283">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="baeb6-284">`location` è il percorso dell'archivio da cui caricare il certificato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-284">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="baeb6-285">`serverCertificate` è il certificato X.509.</span><span class="sxs-lookup"><span data-stu-id="baeb6-285">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="baeb6-286">Nell'ambiente di produzione, HTTPS deve essere configurato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="baeb6-286">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="baeb6-287">Come minimo, è necessario specificare un certificato predefinito.</span><span class="sxs-lookup"><span data-stu-id="baeb6-287">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="baeb6-288">Configurazioni supportate descritte in seguito:</span><span class="sxs-lookup"><span data-stu-id="baeb6-288">Supported configurations described next:</span></span>

* <span data-ttu-id="baeb6-289">Nessuna configurazione</span><span class="sxs-lookup"><span data-stu-id="baeb6-289">No configuration</span></span>
* <span data-ttu-id="baeb6-290">Sostituire il certificato predefinito della configurazione</span><span class="sxs-lookup"><span data-stu-id="baeb6-290">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="baeb6-291">Modificare le impostazioni predefinite nel codice</span><span class="sxs-lookup"><span data-stu-id="baeb6-291">Change the defaults in code</span></span>

<span data-ttu-id="baeb6-292">*Nessuna configurazione*</span><span class="sxs-lookup"><span data-stu-id="baeb6-292">*No configuration*</span></span>

<span data-ttu-id="baeb6-293">Kestrel è in ascolto su `http://localhost:5000` e `https://localhost:5001` (se è disponibile un certificato predefinito).</span><span class="sxs-lookup"><span data-stu-id="baeb6-293">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="baeb6-294">*Sostituire il certificato predefinito della configurazione*</span><span class="sxs-lookup"><span data-stu-id="baeb6-294">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="baeb6-295">`CreateDefaultBuilder` chiama `Configure(context.Configuration.GetSection("Kestrel"))` per impostazione predefinita per caricare la configurazione di Kestrel.</span><span class="sxs-lookup"><span data-stu-id="baeb6-295">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="baeb6-296">È disponibile per Kestrel uno schema di configurazione delle impostazioni delle app HTTPS predefinito.</span><span class="sxs-lookup"><span data-stu-id="baeb6-296">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="baeb6-297">Configurare più endpoint, inclusi gli URL e i certificati da usare, da un file su disco o da un archivio certificati.</span><span class="sxs-lookup"><span data-stu-id="baeb6-297">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="baeb6-298">Nel file *appsettings.json* di esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="baeb6-298">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="baeb6-299">Impostare **AllowInvalid** su `true` per consentire l'uso di certificati non validi, come ad esempio i certificati autofirmati.</span><span class="sxs-lookup"><span data-stu-id="baeb6-299">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="baeb6-300">Tutti gli endpoint HTTPS che non specificano un certificato (**HttpsDefaultCert** nell'esempio che segue) usano il certificato definito in **Certificates** > **Default** o il certificato di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="baeb6-300">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="baeb6-301">Un'alternativa all'uso di **Path** e **Password** per qualsiasi nodo del certificato consiste nello specificare il certificato usando i campi dell'archivio certificati.</span><span class="sxs-lookup"><span data-stu-id="baeb6-301">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="baeb6-302">Ad esempio, il certificato predefinito **Certificates**  >  **Default** può essere specificato come:</span><span class="sxs-lookup"><span data-stu-id="baeb6-302">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="baeb6-303">Note di schema:</span><span class="sxs-lookup"><span data-stu-id="baeb6-303">Schema notes:</span></span>

* <span data-ttu-id="baeb6-304">I nomi degli endpoint non applicano la distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="baeb6-304">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="baeb6-305">Ad esempio, sono validi sia `HTTPS` che `Https`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-305">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="baeb6-306">Il parametro `Url` è obbligatorio per ogni endpoint.</span><span class="sxs-lookup"><span data-stu-id="baeb6-306">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="baeb6-307">Il formato per questo parametro è uguale a quello del parametro di configurazione di primo livello `Urls`, con la differenza che si limita a un singolo valore.</span><span class="sxs-lookup"><span data-stu-id="baeb6-307">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="baeb6-308">Questi endpoint sostituiscono quelli definiti nella configurazione di primo livello `Urls`, non vi si aggiungono.</span><span class="sxs-lookup"><span data-stu-id="baeb6-308">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="baeb6-309">Gli endpoint definiti nel codice tramite `Listen` si aggiungono agli endpoint definiti nella sezione di configurazione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-309">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="baeb6-310">La sezione `Certificate` è facoltativa.</span><span class="sxs-lookup"><span data-stu-id="baeb6-310">The `Certificate` section is optional.</span></span> <span data-ttu-id="baeb6-311">Se la sezione `Certificate` non è specificata, vengono usati i valori predefiniti definiti negli scenari precedenti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-311">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="baeb6-312">Se non è disponibile alcun valore predefinito, il server genera un'eccezione e non viene avviato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-312">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="baeb6-313">La `Certificate` sezione supporta sia la password del **percorso** &ndash; **Password** che i certificati dell'archivio dell' **oggetto** &ndash; **Store** .</span><span class="sxs-lookup"><span data-stu-id="baeb6-313">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="baeb6-314">In questo modo è possibile definire un numero qualsiasi di endpoint, purché non provochino conflitti di porte.</span><span class="sxs-lookup"><span data-stu-id="baeb6-314">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="baeb6-315">`options.Configure(context.Configuration.GetSection("{SECTION}"))` restituisce un oggetto `KestrelConfigurationLoader` con un metodo `.Endpoint(string name, listenOptions => { })` che può essere usato per integrare le impostazioni dell'endpoint configurato:</span><span class="sxs-lookup"><span data-stu-id="baeb6-315">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="baeb6-316">`KestrelServerOptions.ConfigurationLoader` è possibile accedere direttamente a per continuare a scorrere sul caricatore esistente, ad esempio quello fornito da <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="baeb6-316">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="baeb6-317">La sezione di configurazione per ogni endpoint è disponibile nelle opzioni del `Endpoint` metodo in modo che sia possibile leggere le impostazioni personalizzate.</span><span class="sxs-lookup"><span data-stu-id="baeb6-317">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="baeb6-318">È possibile caricare più configurazioni chiamando ancora `options.Configure(context.Configuration.GetSection("{SECTION}"))` con un'altra sezione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-318">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="baeb6-319">Viene usata solo l'ultima configurazione, a meno che `Load` sia stato chiamato esplicitamente in istanze precedenti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-319">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="baeb6-320">Il metapacchetto non chiama `Load` in modo che la sezione di configurazione predefinita venga sostituita.</span><span class="sxs-lookup"><span data-stu-id="baeb6-320">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="baeb6-321">`KestrelConfigurationLoader` riflette la famiglia di API `Listen` da `KestrelServerOptions` all'overload di `Endpoint`, in modo che gli endpoint di codice e di configurazione possano essere configurati nella stessa posizione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-321">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="baeb6-322">Questi overload non usano nomi e usano solo le impostazioni predefinite della configurazione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-322">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="baeb6-323">*Modificare le impostazioni predefinite nel codice*</span><span class="sxs-lookup"><span data-stu-id="baeb6-323">*Change the defaults in code*</span></span>

<span data-ttu-id="baeb6-324">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` possono essere usati per modificare le impostazioni predefinite per `ListenOptions` e `HttpsConnectionAdapterOptions`, inclusa l'esecuzione dell'override del certificato predefinito specificato nello scenario precedente.</span><span class="sxs-lookup"><span data-stu-id="baeb6-324">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="baeb6-325">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devono essere chiamati prima che venga configurato qualsiasi endpoint.</span><span class="sxs-lookup"><span data-stu-id="baeb6-325">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="baeb6-326">*Supporto kestrel per SNI*</span><span class="sxs-lookup"><span data-stu-id="baeb6-326">*Kestrel support for SNI*</span></span>

<span data-ttu-id="baeb6-327">[L'Indicazione nome server (SNI)](https://tools.ietf.org/html/rfc6066#section-3) può essere usata per ospitare più domini sulla stessa porta e indirizzo IP.</span><span class="sxs-lookup"><span data-stu-id="baeb6-327">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="baeb6-328">Perché SNI funzioni è necessario che il client invii al server il nome host per la sessione protetta durante l'handshake TLS in modo che il server possa specificare il certificato corretto.</span><span class="sxs-lookup"><span data-stu-id="baeb6-328">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="baeb6-329">Il client usa il certificato specificato per la comunicazione crittografata con il server durante la sessione protetta che segue l'handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="baeb6-329">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="baeb6-330">Kestrel supporta SNI tramite il callback `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-330">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="baeb6-331">Il callback viene richiamato una volta per ogni connessione per consentire all'app di controllare il nome host e selezionare il certificato appropriato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-331">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="baeb6-332">Il supporto SNI richiede:</span><span class="sxs-lookup"><span data-stu-id="baeb6-332">SNI support requires:</span></span>

* <span data-ttu-id="baeb6-333">In esecuzione su Framework di destinazione `netcoreapp2.1` o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="baeb6-333">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="baeb6-334">In `net461` o versioni successive, il callback viene richiamato, ma `name` è sempre `null` .</span><span class="sxs-lookup"><span data-stu-id="baeb6-334">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="baeb6-335">L'elemento `name` è `null` anche se il client non specifica il parametro del nome host nell'handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="baeb6-335">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="baeb6-336">Esecuzione di tutti i siti Web nella stessa istanza di Kestrel.</span><span class="sxs-lookup"><span data-stu-id="baeb6-336">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="baeb6-337">Kestrel supporta la condivisione di un indirizzo IP e di una porta tra più istanze solo con un proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="baeb6-337">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a><span data-ttu-id="baeb6-338">Registrazione connessione</span><span class="sxs-lookup"><span data-stu-id="baeb6-338">Connection logging</span></span>

<span data-ttu-id="baeb6-339">Chiamare <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> per creare log a livello di debug per la comunicazione a livello di byte in una connessione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-339">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="baeb6-340">La registrazione delle connessioni è utile per la risoluzione dei problemi nella comunicazione di basso livello, ad esempio durante la crittografia TLS e dietro i proxy.</span><span class="sxs-lookup"><span data-stu-id="baeb6-340">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="baeb6-341">Se `UseConnectionLogging` viene inserito prima `UseHttps` , viene registrato il traffico crittografato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-341">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="baeb6-342">Se `UseConnectionLogging` viene inserito dopo `UseHttps` , viene registrato il traffico decrittografato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-342">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="baeb6-343">Associazione a un socket TCP</span><span class="sxs-lookup"><span data-stu-id="baeb6-343">Bind to a TCP socket</span></span>

<span data-ttu-id="baeb6-344">Il metodo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> esegue l'associazione a un socket TCP e un'espressione lambda per le opzioni consente di configurare un certificato X.509:</span><span class="sxs-lookup"><span data-stu-id="baeb6-344">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="baeb6-345">L'esempio configura HTTPS per un endpoint con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="baeb6-345">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="baeb6-346">Usare la stessa API per configurare altre impostazioni di Kestrel per endpoint specifici.</span><span class="sxs-lookup"><span data-stu-id="baeb6-346">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="baeb6-347">Associazione a un socket Unix</span><span class="sxs-lookup"><span data-stu-id="baeb6-347">Bind to a Unix socket</span></span>

<span data-ttu-id="baeb6-348">Impostare l'ascolto su un socket Unix con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> per migliorare le prestazioni con Nginx, come visualizzato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="baeb6-348">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="baeb6-349">Nel file di configurazione nginx impostare la `server`  >  `location`  >  `proxy_pass` voce su `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="baeb6-349">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="baeb6-350">`{KESTREL SOCKET}` nome del socket fornito a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (ad esempio, `kestrel-test.sock` nell'esempio precedente).</span><span class="sxs-lookup"><span data-stu-id="baeb6-350">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="baeb6-351">Verificare che il socket sia scrivibile da nginx (ad esempio, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="baeb6-351">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="baeb6-352">Porta 0</span><span class="sxs-lookup"><span data-stu-id="baeb6-352">Port 0</span></span>

<span data-ttu-id="baeb6-353">Quando si specifica il numero di porta `0`, Kestrel esegue l'associazione dinamica a una porta disponibile.</span><span class="sxs-lookup"><span data-stu-id="baeb6-353">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="baeb6-354">L'esempio seguente indica come determinare la porta alla quale Kestrel ha eseguito l'associazione in fase di esecuzione:</span><span class="sxs-lookup"><span data-stu-id="baeb6-354">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="baeb6-355">Quando si esegue l'app, l'output della finestra della console indica la porta dinamica in cui l'app può essere raggiunta:</span><span class="sxs-lookup"><span data-stu-id="baeb6-355">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="baeb6-356">Limitazioni</span><span class="sxs-lookup"><span data-stu-id="baeb6-356">Limitations</span></span>

<span data-ttu-id="baeb6-357">Configurare gli endpoint con i metodi seguenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-357">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="baeb6-358">L'argomento della riga di comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="baeb6-358">`--urls` command-line argument</span></span>
* <span data-ttu-id="baeb6-359">La chiave di configurazione dell'host `urls`</span><span class="sxs-lookup"><span data-stu-id="baeb6-359">`urls` host configuration key</span></span>
* <span data-ttu-id="baeb6-360">La variabile di ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="baeb6-360">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="baeb6-361">Questi metodi sono utili se si vuole che il codice funzioni con server diversi da Kestrel.</span><span class="sxs-lookup"><span data-stu-id="baeb6-361">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="baeb6-362">Tenere presenti, tuttavia, le limitazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-362">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="baeb6-363">HTTPS non può essere usato con questi approcci, a meno che non venga specificato un certificato predefinito nella configurazione dell'endpoint HTTPS (ad esempio, usando la configurazione `KestrelServerOptions` o un file di configurazione come illustrato in precedenza in questo argomento).</span><span class="sxs-lookup"><span data-stu-id="baeb6-363">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="baeb6-364">Quando sia l'approccio `Listen` che l'approccio `UseUrls` vengono usati contemporaneamente, gli endpoint `Listen` eseguono l'override degli endpoint `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-364">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="baeb6-365">Configurazione dell'endpoint IIS</span><span class="sxs-lookup"><span data-stu-id="baeb6-365">IIS endpoint configuration</span></span>

<span data-ttu-id="baeb6-366">Quando si usa IIS, le associazioni di URL per le associazioni di override di IIS vengono impostate mediante `Listen` o `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-366">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="baeb6-367">Per altre informazioni, vedere l'articolo [Introduzione al modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="baeb6-367">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="baeb6-368">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="baeb6-368">ListenOptions.Protocols</span></span>

<span data-ttu-id="baeb6-369">La proprietà `Protocols` stabilisce i protocolli HTTP (`HttpProtocols`) abilitati in un endpoint di connessione o per il server.</span><span class="sxs-lookup"><span data-stu-id="baeb6-369">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="baeb6-370">Assegnare un valore alla proprietà `Protocols` dall'enumerazione `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-370">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="baeb6-371">Valore di enumerazione `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="baeb6-371">`HttpProtocols` enum value</span></span> | <span data-ttu-id="baeb6-372">Protocollo di connessione consentito</span><span class="sxs-lookup"><span data-stu-id="baeb6-372">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="baeb6-373">Solo HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="baeb6-373">HTTP/1.1 only.</span></span> <span data-ttu-id="baeb6-374">Può essere usato con o senza TLS.</span><span class="sxs-lookup"><span data-stu-id="baeb6-374">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="baeb6-375">Solo HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="baeb6-375">HTTP/2 only.</span></span> <span data-ttu-id="baeb6-376">Può essere usato senza TLS solo se il client supporta una [modalità di conoscenza pregressa](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="baeb6-376">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="baeb6-377">HTTP/1.1 e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="baeb6-377">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="baeb6-378">HTTP/2 richiede che il client selezioni HTTP/2 nell'handshake TLS [(Application-Layer Protocol negotiation) ALPN](https://tools.ietf.org/html/rfc7301#section-3) in caso contrario, il valore predefinito per la connessione è HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="baeb6-378">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="baeb6-379">Il `ListenOptions.Protocols` valore predefinito per qualsiasi endpoint è `HttpProtocols.Http1AndHttp2` .</span><span class="sxs-lookup"><span data-stu-id="baeb6-379">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="baeb6-380">Restrizioni relative a TLS per HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="baeb6-380">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="baeb6-381">TLS versione 1.2 o successiva</span><span class="sxs-lookup"><span data-stu-id="baeb6-381">TLS version 1.2 or later</span></span>
* <span data-ttu-id="baeb6-382">Rinegoziazione disabilitata</span><span class="sxs-lookup"><span data-stu-id="baeb6-382">Renegotiation disabled</span></span>
* <span data-ttu-id="baeb6-383">Compressione disabilitata</span><span class="sxs-lookup"><span data-stu-id="baeb6-383">Compression disabled</span></span>
* <span data-ttu-id="baeb6-384">Dimensioni minime per lo scambio di chiavi temporanee:</span><span class="sxs-lookup"><span data-stu-id="baeb6-384">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="baeb6-385">Diffie-Hellman a curva ellittica (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : minimo 224 bit</span><span class="sxs-lookup"><span data-stu-id="baeb6-385">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="baeb6-386">Diffie-Hellman campo finito (DHE) &lbrack; `TLS12` &rbrack; : minimo 2048 bit</span><span class="sxs-lookup"><span data-stu-id="baeb6-386">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="baeb6-387">Il pacchetto di crittografia non è consentito.</span><span class="sxs-lookup"><span data-stu-id="baeb6-387">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="baeb6-388">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; con la curva ellittica P-256 &lbrack; `FIPS186` &rbrack; è supportata per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="baeb6-388">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="baeb6-389">Nell'esempio seguente sono consentite connessioni HTTP/1.1 e HTTP/2 sulla porta 8000.</span><span class="sxs-lookup"><span data-stu-id="baeb6-389">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="baeb6-390">Le connessioni sono protette da TLS con un certificato incluso:</span><span class="sxs-lookup"><span data-stu-id="baeb6-390">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="baeb6-391">Usare il middleware di connessione per filtrare gli handshake TLS in base alla connessione per le crittografie specifiche, se necessario.</span><span class="sxs-lookup"><span data-stu-id="baeb6-391">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="baeb6-392">L'esempio seguente genera un'eccezione <xref:System.NotSupportedException> per qualsiasi algoritmo crittografico non supportato dall'app.</span><span class="sxs-lookup"><span data-stu-id="baeb6-392">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="baeb6-393">In alternativa, definire e confrontare [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) con un elenco di pacchetti di crittografia accettabili.</span><span class="sxs-lookup"><span data-stu-id="baeb6-393">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="baeb6-394">Non viene utilizzata alcuna crittografia con un algoritmo di crittografia [CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) .</span><span class="sxs-lookup"><span data-stu-id="baeb6-394">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="baeb6-395">Il filtro della connessione può essere configurato anche tramite un' <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> espressione lambda:</span><span class="sxs-lookup"><span data-stu-id="baeb6-395">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="baeb6-396">In Linux <xref:System.Net.Security.CipherSuitesPolicy> è possibile usare per filtrare gli handshake TLS in base alla connessione:</span><span class="sxs-lookup"><span data-stu-id="baeb6-396">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="baeb6-397">*Impostare il protocollo dalla configurazione*</span><span class="sxs-lookup"><span data-stu-id="baeb6-397">*Set the protocol from configuration*</span></span>

<span data-ttu-id="baeb6-398">`CreateDefaultBuilder` chiama `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` per impostazione predefinita per caricare la configurazione di Kestrel.</span><span class="sxs-lookup"><span data-stu-id="baeb6-398">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="baeb6-399">Il *appsettings.js* seguente nell'esempio stabilisce http/1.1 come protocollo di connessione predefinito per tutti gli endpoint:</span><span class="sxs-lookup"><span data-stu-id="baeb6-399">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="baeb6-400">Nell' *appsettings.js* seguente viene stabilito il protocollo di connessione HTTP/1.1 per un endpoint specifico:</span><span class="sxs-lookup"><span data-stu-id="baeb6-400">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="baeb6-401">I protocolli specificati nei valori di override del codice sono impostati dalla configurazione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-401">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="baeb6-402">Configurazione del trasporto</span><span class="sxs-lookup"><span data-stu-id="baeb6-402">Transport configuration</span></span>

<span data-ttu-id="baeb6-403">Per i progetti che richiedono l'utilizzo di libuv ( <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> ):</span><span class="sxs-lookup"><span data-stu-id="baeb6-403">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="baeb6-404">Aggiungere una dipendenza per il pacchetto [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="baeb6-404">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="baeb6-405">Chiamare <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> su `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="baeb6-405">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateHostBuilder(args).Build().Run();
       }

       public static IHostBuilder CreateHostBuilder(string[] args) =>
           Host.CreateDefaultBuilder(args)
               .ConfigureWebHostDefaults(webBuilder =>
               {
                   webBuilder.UseLibuv();
                   webBuilder.UseStartup<Startup>();
               });
   }
   ```

### <a name="url-prefixes"></a><span data-ttu-id="baeb6-406">Prefissi URL</span><span class="sxs-lookup"><span data-stu-id="baeb6-406">URL prefixes</span></span>

<span data-ttu-id="baeb6-407">Se si usa `UseUrls`, l'argomento della riga di comando `--urls`, la chiave di configurazione dell'host `urls` o la variabile di ambiente `ASPNETCORE_URLS`, i prefissi URL possono avere uno dei formati seguenti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-407">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="baeb6-408">Sono validi solo i prefissi URL HTTP.</span><span class="sxs-lookup"><span data-stu-id="baeb6-408">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="baeb6-409">Kestrel non supporta HTTPS quando la configurazione delle associazioni URL viene eseguita con `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-409">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="baeb6-410">Indirizzo IPv4 con numero di porta</span><span class="sxs-lookup"><span data-stu-id="baeb6-410">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="baeb6-411">`0.0.0.0` è un caso speciale che esegue l'associazione a tutti gli indirizzi IPv4.</span><span class="sxs-lookup"><span data-stu-id="baeb6-411">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="baeb6-412">Indirizzo IPv6 con numero di porta</span><span class="sxs-lookup"><span data-stu-id="baeb6-412">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="baeb6-413">`[::]` è l'equivalente IPv6 di `0.0.0.0` per IPv4.</span><span class="sxs-lookup"><span data-stu-id="baeb6-413">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="baeb6-414">Nome host con numero di porta</span><span class="sxs-lookup"><span data-stu-id="baeb6-414">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="baeb6-415">I nomi host, `*` e `+` non sono casi particolari.</span><span class="sxs-lookup"><span data-stu-id="baeb6-415">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="baeb6-416">Tutto ciò che non è riconosciuto come un indirizzo IP o un elemento `localhost` valido esegue l'associazione a tutti gli IP IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="baeb6-416">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="baeb6-417">Per associare nomi host diversi ad app ASP.NET Core diverse sulla stessa porta, usare [HTTP.sys](xref:fundamentals/servers/httpsys) o un server proxy inverso come IIS, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="baeb6-417">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="baeb6-418">Una configurazione che prevede un proxy inverso richiede il [filtro host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="baeb6-418">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="baeb6-419">Nome host `localhost` con numero di porta o IP di loopback con numero di porta</span><span class="sxs-lookup"><span data-stu-id="baeb6-419">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="baeb6-420">Se è specificato `localhost`, Kestrel tenta l'associazione alle interfacce di loopback sia IPv4 che IPv6.</span><span class="sxs-lookup"><span data-stu-id="baeb6-420">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="baeb6-421">Se la porta richiesta è in uso da parte di un altro servizio in una delle due interfacce di loopback, Kestrel non viene avviato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-421">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="baeb6-422">Se una delle due interfacce di loopback non è disponibile per qualsiasi altro motivo (in genere perché IPv6 non è supportato), Kestrel registra un avviso.</span><span class="sxs-lookup"><span data-stu-id="baeb6-422">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="baeb6-423">Filtro host</span><span class="sxs-lookup"><span data-stu-id="baeb6-423">Host filtering</span></span>

<span data-ttu-id="baeb6-424">Mentre supporta la configurazione in base ai prefissi, ad esempio `http://example.com:5000`, Kestrel ignora quasi sempre il nome host.</span><span class="sxs-lookup"><span data-stu-id="baeb6-424">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="baeb6-425">L'host `localhost` è un caso speciale usato per l'associazione agli indirizzi di loopback.</span><span class="sxs-lookup"><span data-stu-id="baeb6-425">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="baeb6-426">Qualsiasi host che non sia un indirizzo IP esplicito esegue l'associazione a tutti gli indirizzi IP pubblici.</span><span class="sxs-lookup"><span data-stu-id="baeb6-426">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="baeb6-427">Le intestazioni `Host` non vengono convalidate.</span><span class="sxs-lookup"><span data-stu-id="baeb6-427">`Host` headers aren't validated.</span></span>

<span data-ttu-id="baeb6-428">Come soluzione alternativa, usare il middleware di filtro host.</span><span class="sxs-lookup"><span data-stu-id="baeb6-428">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="baeb6-429">Il middleware di filtro host viene fornito dal pacchetto [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , fornito in modo implicito per le app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="baeb6-429">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="baeb6-430">Il middleware viene aggiunto da <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, che chiama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="baeb6-430">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="baeb6-431">Per impostazione predefinita, il middleware di filtro host è disabilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="baeb6-431">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="baeb6-432">Per abilitare il middleware, definire una `AllowedHosts` chiave in *appsettings.jssu* / *appSettings. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="baeb6-432">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="baeb6-433">Il valore è un elenco con valori delimitati da punto e virgola di nomi host senza numeri di porta:</span><span class="sxs-lookup"><span data-stu-id="baeb6-433">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="baeb6-434">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="baeb6-434">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="baeb6-435">Il [middleware delle intestazioni inoltrate](xref:host-and-deploy/proxy-load-balancer) include anche un'opzione <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="baeb6-435">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="baeb6-436">Il middleware di intestazioni inoltrate e il middleware di filtro host hanno funzionalità simili per diversi scenari.</span><span class="sxs-lookup"><span data-stu-id="baeb6-436">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="baeb6-437">L'impostazione di `AllowedHosts` con il middleware delle intestazioni inoltrate è appropriato se l'intestazione `Host` non viene mantenuta durante l'inoltro delle richieste con un server proxy inverso o il bilanciamento del carico.</span><span class="sxs-lookup"><span data-stu-id="baeb6-437">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="baeb6-438">L'impostazione di `AllowedHosts` con il middleware del filtro host è appropriata se si usa Kestrel come server perimetrale rivolto al pubblico o se l'intestazione `Host` viene inoltrata direttamente.</span><span class="sxs-lookup"><span data-stu-id="baeb6-438">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="baeb6-439">Per altre informazioni sul middleware delle intestazioni inoltrate, vedere <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="baeb6-439">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="baeb6-440">Kestrel è un [server Web per ASP.NET Core](xref:fundamentals/servers/index) multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="baeb6-440">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="baeb6-441">Kestrel è il server Web incluso per impostazione predefinita nei modelli di progetto di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="baeb6-441">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="baeb6-442">Kestrel supporta gli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-442">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="baeb6-443">HTTPS</span><span class="sxs-lookup"><span data-stu-id="baeb6-443">HTTPS</span></span>
* <span data-ttu-id="baeb6-444">Aggiornamento opaco usato per abilitare [WebSocket](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="baeb6-444">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="baeb6-445">Socket Unix ad alte prestazioni dietro Nginx</span><span class="sxs-lookup"><span data-stu-id="baeb6-445">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="baeb6-446">HTTP/2 (tranne che in macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="baeb6-446">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="baeb6-447">&dagger;HTTP/2 verrà supportato in macOS in una versione futura.</span><span class="sxs-lookup"><span data-stu-id="baeb6-447">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="baeb6-448">Kestrel è supportato in tutte le piattaforme e le versioni supportate da .NET Core.</span><span class="sxs-lookup"><span data-stu-id="baeb6-448">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="baeb6-449">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="baeb6-449">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="baeb6-450">Supporto HTTP/2</span><span class="sxs-lookup"><span data-stu-id="baeb6-450">HTTP/2 support</span></span>

<span data-ttu-id="baeb6-451">[HTTP/2](https://httpwg.org/specs/rfc7540.html) è disponibile per le app ASP.NET Core se vengono soddisfatti i requisiti di base seguenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-451">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="baeb6-452">Sistema operativo&dagger;</span><span class="sxs-lookup"><span data-stu-id="baeb6-452">Operating system&dagger;</span></span>
  * <span data-ttu-id="baeb6-453">Windows Server 2016/Windows 10 o versioni successive&Dagger;</span><span class="sxs-lookup"><span data-stu-id="baeb6-453">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="baeb6-454">Linux con OpenSSL 1.0.2 o versioni successive (ad esempio, Ubuntu 16.04 o versioni successive)</span><span class="sxs-lookup"><span data-stu-id="baeb6-454">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="baeb6-455">Framework di destinazione: .NET Core 2.2 o versioni successive</span><span class="sxs-lookup"><span data-stu-id="baeb6-455">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="baeb6-456">Connessione [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="baeb6-456">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="baeb6-457">Connessione TLS 1.2 o successiva</span><span class="sxs-lookup"><span data-stu-id="baeb6-457">TLS 1.2 or later connection</span></span>

<span data-ttu-id="baeb6-458">&dagger;HTTP/2 verrà supportato in macOS in una versione futura.</span><span class="sxs-lookup"><span data-stu-id="baeb6-458">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="baeb6-459">&Dagger;Kestrel ha un supporto limitato per HTTP/2 in Windows Server 2012 R2 e Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="baeb6-459">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="baeb6-460">Il supporto è limitato perché l'elenco di suite di crittografia TLS supportate disponibili in questi sistemi operativi è limitato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-460">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="baeb6-461">Un certificato generato con un algoritmo ECDSA potrebbe essere necessario per proteggere le connessioni TLS.</span><span class="sxs-lookup"><span data-stu-id="baeb6-461">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="baeb6-462">Se viene stabilita una connessione HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) corrisponde a `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-462">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="baeb6-463">HTTP/2 è disabilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="baeb6-463">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="baeb6-464">Per altre informazioni sulla configurazione, vedere le sezioni [Opzioni Kestrel](#kestrel-options) e [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="baeb6-464">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="baeb6-465">Quando usare Kestrel con un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="baeb6-465">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="baeb6-466">È possibile usare Kestrel da solo o in combinazione con un *server proxy inverso*, ad esempio [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="baeb6-466">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="baeb6-467">Il server proxy inverso riceve le richieste HTTP dalla rete e le inoltra a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="baeb6-467">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="baeb6-468">Kestrel usato come server Web perimetrale (esposto a Internet):</span><span class="sxs-lookup"><span data-stu-id="baeb6-468">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel comunica direttamente con Internet senza un server proxy inverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="baeb6-470">Kestrel usato in una configurazione proxy inverso:</span><span class="sxs-lookup"><span data-stu-id="baeb6-470">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel comunica indirettamente con Internet attraverso un server proxy inverso, ad esempio IIS, Nginx o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="baeb6-472">La configurazione, con o senza un server proxy inverso, è una configurazione di hosting supportata.</span><span class="sxs-lookup"><span data-stu-id="baeb6-472">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="baeb6-473">Se viene utilizzato come server perimetrale in assenza di un server proxy inverso, Kestrel non supporta la condivisione tra più processi dell'IP e della porta.</span><span class="sxs-lookup"><span data-stu-id="baeb6-473">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="baeb6-474">Quando è configurato per restare in ascolto su una porta, Kestrel gestisce tutto il traffico per tale porta indipendentemente dalle intestazioni `Host` delle richieste.</span><span class="sxs-lookup"><span data-stu-id="baeb6-474">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="baeb6-475">Un proxy inverso che può condividere le porte può eseguire l'inoltro delle richieste a Kestrel su un unico IP e un'unica porta.</span><span class="sxs-lookup"><span data-stu-id="baeb6-475">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="baeb6-476">Anche se la presenza di un server proxy inverso non è necessaria, può risultare utile per i motivi seguenti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-476">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="baeb6-477">Un proxy inverso:</span><span class="sxs-lookup"><span data-stu-id="baeb6-477">A reverse proxy:</span></span>

* <span data-ttu-id="baeb6-478">Può limitare l'area della superficie di attacco pubblica esposta delle app ospitate.</span><span class="sxs-lookup"><span data-stu-id="baeb6-478">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="baeb6-479">Offre un livello aggiuntivo di configurazione e protezione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-479">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="baeb6-480">Potrebbe offrire un'integrazione migliore con l'infrastruttura esistente.</span><span class="sxs-lookup"><span data-stu-id="baeb6-480">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="baeb6-481">Semplifica il bilanciamento del carico e la configurazione di comunicazioni protette (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="baeb6-481">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="baeb6-482">Solo il server proxy inverso richiede un certificato X. 509 e il server è in grado di comunicare con i server dell'app nella rete interna tramite HTTP normale.</span><span class="sxs-lookup"><span data-stu-id="baeb6-482">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="baeb6-483">Una configurazione che prevede un proxy inverso richiede il [filtro host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="baeb6-483">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="baeb6-484">Come usare Kestrel nelle app ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="baeb6-484">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="baeb6-485">Il pacchetto [Microsoft. AspNetCore. Server. gheppio](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) è incluso nel [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="baeb6-485">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="baeb6-486">I modelli di progetto ASP.NET Core usano Kestrel per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="baeb6-486">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="baeb6-487">In *Program.cs* il codice del modello chiama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, che chiama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> in background.</span><span class="sxs-lookup"><span data-stu-id="baeb6-487">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="baeb6-488">Per ulteriori informazioni su `CreateDefaultBuilder` e sulla compilazione dell'host, vedere la sezione *configurare un host* di <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="baeb6-488">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="baeb6-489">Per fornire una configurazione aggiuntiva dopo la chiamata di `CreateDefaultBuilder`, usare `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="baeb6-489">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="baeb6-490">Se l'app non chiama `CreateDefaultBuilder` per configurare l'host, chiamare <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **prima** di chiamare `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="baeb6-490">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="baeb6-491">Opzioni Kestrel</span><span class="sxs-lookup"><span data-stu-id="baeb6-491">Kestrel options</span></span>

<span data-ttu-id="baeb6-492">Il server Web Kestrel dispone di opzioni di configurazione dei vincoli che risultano particolarmente utili nelle distribuzioni con connessione Internet.</span><span class="sxs-lookup"><span data-stu-id="baeb6-492">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="baeb6-493">Impostare i vincoli per la proprietà <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> della classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="baeb6-493">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="baeb6-494">La proprietà `Limits` contiene un'istanza della classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="baeb6-494">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="baeb6-495">Negli esempi seguenti viene usato lo spazio dei nomi <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="baeb6-495">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="baeb6-496">Le opzioni gheppio, che sono configurate nel codice C# negli esempi seguenti, possono essere impostate anche usando un [provider di configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="baeb6-496">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="baeb6-497">Il provider di configurazione file, ad esempio, può caricare la configurazione di Gheppio da un *appsettings.jssu* o *appSettings. { File Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="baeb6-497">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="baeb6-498">Usare **uno** degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-498">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="baeb6-499">Configurare gheppio in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="baeb6-499">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="baeb6-500">Inserire un'istanza di `IConfiguration` nella `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="baeb6-500">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="baeb6-501">Nell'esempio seguente si presuppone che la configurazione inserita venga assegnata alla `Configuration` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="baeb6-501">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="baeb6-502">In `Startup.ConfigureServices` caricare la `Kestrel` sezione della configurazione nella configurazione di Gheppio:</span><span class="sxs-lookup"><span data-stu-id="baeb6-502">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="baeb6-503">Configurare il gheppio durante la compilazione dell'host:</span><span class="sxs-lookup"><span data-stu-id="baeb6-503">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="baeb6-504">In *Program.cs*caricare la `Kestrel` sezione della configurazione nella configurazione di Gheppio:</span><span class="sxs-lookup"><span data-stu-id="baeb6-504">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="baeb6-505">Entrambi gli approcci precedenti funzionano con qualsiasi [provider di configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="baeb6-505">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="baeb6-506">Timeout keep-alive</span><span class="sxs-lookup"><span data-stu-id="baeb6-506">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="baeb6-507">Ottiene o imposta il [timeout keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="baeb6-507">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="baeb6-508">Il valore predefinito è 2 minuti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-508">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="baeb6-509">Numero massimo di connessioni client</span><span class="sxs-lookup"><span data-stu-id="baeb6-509">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="baeb6-510">È possibile impostare il numero massimo di connessioni TCP aperte simultaneamente per l'intera app con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="baeb6-510">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="baeb6-511">È previsto un limite separato per le connessioni che sono state aggiornate da HTTP o HTTPS a un altro protocollo (ad esempio su una richiesta WebSocket).</span><span class="sxs-lookup"><span data-stu-id="baeb6-511">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="baeb6-512">Dopo l'aggiornamento di una connessione, questa non viene conteggiata per il limite `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-512">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="baeb6-513">Per impostazione predefinita, il numero massimo di connessioni è illimitato (null).</span><span class="sxs-lookup"><span data-stu-id="baeb6-513">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="baeb6-514">Dimensione massima del corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="baeb6-514">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="baeb6-515">La dimensione massima predefinita del corpo della richiesta è pari a 30.000.000 di byte, equivalenti a circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="baeb6-515">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="baeb6-516">Il metodo consigliato per ignorare il limite in un'app ASP.NET Core MVC è l'uso dell'attributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> in un metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="baeb6-516">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="baeb6-517">L'esempio seguente illustra come configurare il vincolo per l'app in ogni richiesta:</span><span class="sxs-lookup"><span data-stu-id="baeb6-517">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="baeb6-518">Eseguire l'override dell'impostazione per una richiesta specifica nel middleware:</span><span class="sxs-lookup"><span data-stu-id="baeb6-518">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="baeb6-519">Viene generata un'eccezione se l'app configura il limite per una richiesta dopo che l'app ha iniziato a leggere la richiesta.</span><span class="sxs-lookup"><span data-stu-id="baeb6-519">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="baeb6-520">Una proprietà `IsReadOnly` indica se la proprietà `MaxRequestBodySize` è in stato di sola lettura e pertanto è troppo tardi per configurare il limite.</span><span class="sxs-lookup"><span data-stu-id="baeb6-520">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="baeb6-521">Quando un'app viene eseguita [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) dietro il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), il limite della dimensione del corpo della richiesta di Kestrel è disabilitato perché viene già impostato da IIS.</span><span class="sxs-lookup"><span data-stu-id="baeb6-521">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="baeb6-522">Velocità minima dei dati del corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="baeb6-522">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="baeb6-523">Kestrel controlla ogni secondo se i dati arrivano alla velocità in byte al secondo specificata.</span><span class="sxs-lookup"><span data-stu-id="baeb6-523">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="baeb6-524">Se la frequenza scende sotto il valore minimo, si è verificato il timeout della connessione. Il periodo di tolleranza è la quantità di tempo che il gheppio concede al client di aumentare la velocità di invio fino al minimo. la frequenza non viene controllata durante tale periodo di tempo.</span><span class="sxs-lookup"><span data-stu-id="baeb6-524">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="baeb6-525">Il periodo di prova consente di evitare l'interruzione di connessioni che inizialmente inviano i dati a velocità ridotta a causa dell'avvio lento del protocollo TCP.</span><span class="sxs-lookup"><span data-stu-id="baeb6-525">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="baeb6-526">La velocità minima predefinita è di 240 byte al secondo, con un periodo di tolleranza di 5 secondi.</span><span class="sxs-lookup"><span data-stu-id="baeb6-526">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="baeb6-527">Anche per la risposta è prevista una velocità minima.</span><span class="sxs-lookup"><span data-stu-id="baeb6-527">A minimum rate also applies to the response.</span></span> <span data-ttu-id="baeb6-528">Il codice per impostare il limite della richiesta e della risposta è identico e varia solo per `RequestBody` o `Response` nei nomi di proprietà e di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="baeb6-528">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="baeb6-529">L'esempio seguente visualizza come configurare la velocità minima dei dati in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="baeb6-529">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="baeb6-530">Ignorare i limiti di velocità minima per ogni richiesta nel middleware:</span><span class="sxs-lookup"><span data-stu-id="baeb6-530">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="baeb6-531">Nessuna delle due funzionalità relative alla velocità, a cui si fa riferimento nell'esempio precedente, è presente in `HttpContext.Features` per le richieste HTTP/2 perché la modifica dei limiti di velocità per ogni richiesta non è supportata per HTTP/2 a causa del supporto del protocollo per il multiplexing delle richieste.</span><span class="sxs-lookup"><span data-stu-id="baeb6-531">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="baeb6-532">I limiti di velocità a livello di server configurati tramite `KestrelServerOptions.Limits` vengono tuttavia applicati alle connessioni HTTP/1.x e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="baeb6-532">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="baeb6-533">Timeout delle intestazioni delle richieste</span><span class="sxs-lookup"><span data-stu-id="baeb6-533">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="baeb6-534">Ottiene o imposta la quantità massima di tempo che il server dedica alla ricezione delle intestazioni delle richieste.</span><span class="sxs-lookup"><span data-stu-id="baeb6-534">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="baeb6-535">Il valore predefinito è 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="baeb6-535">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="baeb6-536">Numero massimo di flussi per connessione</span><span class="sxs-lookup"><span data-stu-id="baeb6-536">Maximum streams per connection</span></span>

<span data-ttu-id="baeb6-537">`Http2.MaxStreamsPerConnection` limita il numero di flussi di richieste simultanee per ogni connessione HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="baeb6-537">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="baeb6-538">I flussi in eccesso vengono rifiutati.</span><span class="sxs-lookup"><span data-stu-id="baeb6-538">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="baeb6-539">Il valore predefinito è 100.</span><span class="sxs-lookup"><span data-stu-id="baeb6-539">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="baeb6-540">Dimensioni della tabella delle intestazioni</span><span class="sxs-lookup"><span data-stu-id="baeb6-540">Header table size</span></span>

<span data-ttu-id="baeb6-541">Il decodificatore HPACK decomprime le intestazioni HTTP per le connessioni HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="baeb6-541">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="baeb6-542">`Http2.HeaderTableSize` limita le dimensioni della tabella di compressione delle intestazioni usata dal decodificatore HPACK.</span><span class="sxs-lookup"><span data-stu-id="baeb6-542">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="baeb6-543">Il valore viene specificato in ottetti e deve essere maggiore di zero (0).</span><span class="sxs-lookup"><span data-stu-id="baeb6-543">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="baeb6-544">Il valore predefinito è 4096.</span><span class="sxs-lookup"><span data-stu-id="baeb6-544">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="baeb6-545">Dimensione massima del frame</span><span class="sxs-lookup"><span data-stu-id="baeb6-545">Maximum frame size</span></span>

<span data-ttu-id="baeb6-546">`Http2.MaxFrameSize` indica le dimensioni massime del payload del frame di connessione HTTP/2 da ricevere.</span><span class="sxs-lookup"><span data-stu-id="baeb6-546">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="baeb6-547">Il valore viene specificato in ottetti e deve essere compreso tra 2^14 (16.384) e 2^24-1 (16.777.215).</span><span class="sxs-lookup"><span data-stu-id="baeb6-547">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="baeb6-548">Il valore predefinito è 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="baeb6-548">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="baeb6-549">Dimensioni massime dell'intestazione della richiesta</span><span class="sxs-lookup"><span data-stu-id="baeb6-549">Maximum request header size</span></span>

<span data-ttu-id="baeb6-550">`Http2.MaxRequestHeaderFieldSize` indica le dimensioni massime consentite in ottetti di valori di intestazione di richiesta.</span><span class="sxs-lookup"><span data-stu-id="baeb6-550">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="baeb6-551">Questo limite si applica insieme sia al nome che al valore nelle rappresentazioni compresse e non compresse.</span><span class="sxs-lookup"><span data-stu-id="baeb6-551">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="baeb6-552">Il valore deve essere maggiore di zero (0).</span><span class="sxs-lookup"><span data-stu-id="baeb6-552">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="baeb6-553">Il valore predefinito è 8.192.</span><span class="sxs-lookup"><span data-stu-id="baeb6-553">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="baeb6-554">Dimensioni della finestra di connessione iniziali</span><span class="sxs-lookup"><span data-stu-id="baeb6-554">Initial connection window size</span></span>

<span data-ttu-id="baeb6-555">`Http2.InitialConnectionWindowSize` indica il numero massimo di byte di dati del corpo della richiesta che il server memorizza nel buffer in una volta, aggregati in tutte le richieste (flussi), per ogni connessione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-555">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="baeb6-556">Le richieste sono anche limitate da `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-556">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="baeb6-557">Il valore deve essere maggiore di o uguale a 65.535 e minore di 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="baeb6-557">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="baeb6-558">Il valore predefinito è 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="baeb6-558">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="baeb6-559">Dimensioni della finestra di flusso iniziali</span><span class="sxs-lookup"><span data-stu-id="baeb6-559">Initial stream window size</span></span>

<span data-ttu-id="baeb6-560">`Http2.InitialStreamWindowSize` indica il numero massimo di byte di dati del corpo della richiesta che il server memorizza nel buffer in una volta per ogni richiesta (flusso).</span><span class="sxs-lookup"><span data-stu-id="baeb6-560">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="baeb6-561">Le richieste sono anche limitate da `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-561">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="baeb6-562">Il valore deve essere maggiore di o uguale a 65.535 e minore di 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="baeb6-562">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="baeb6-563">Il valore predefinito è 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="baeb6-563">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="baeb6-564">I/O sincrono</span><span class="sxs-lookup"><span data-stu-id="baeb6-564">Synchronous I/O</span></span>

<span data-ttu-id="baeb6-565"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> Controlla se è consentito l'I/O sincrono per la richiesta e la risposta.</span><span class="sxs-lookup"><span data-stu-id="baeb6-565"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="baeb6-566">Il valore predefinito è `true`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-566">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="baeb6-567">Un numero elevato di operazioni di I/O sincrone bloccanti può causare l'esaurimento del pool di thread, il che rende l'app non risponde.</span><span class="sxs-lookup"><span data-stu-id="baeb6-567">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="baeb6-568">Abilitare solo `AllowSynchronousIO` quando si usa una libreria che non supporta l'I/O asincrono.</span><span class="sxs-lookup"><span data-stu-id="baeb6-568">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="baeb6-569">L'esempio seguente abilita l'I/O sincrono:</span><span class="sxs-lookup"><span data-stu-id="baeb6-569">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="baeb6-570">Per informazioni su altre opzioni e limiti di Kestrel, vedere:</span><span class="sxs-lookup"><span data-stu-id="baeb6-570">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="baeb6-571">Configurazione dell'endpoint</span><span class="sxs-lookup"><span data-stu-id="baeb6-571">Endpoint configuration</span></span>

<span data-ttu-id="baeb6-572">Per impostazione predefinita, ASP.NET Core è associato a:</span><span class="sxs-lookup"><span data-stu-id="baeb6-572">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="baeb6-573">`https://localhost:5001` (quando è presente un certificato di sviluppo locale)</span><span class="sxs-lookup"><span data-stu-id="baeb6-573">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="baeb6-574">Specificare gli URL usando gli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-574">Specify URLs using the:</span></span>

* <span data-ttu-id="baeb6-575">La variabile di ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-575">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="baeb6-576">L'argomento della riga di comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-576">`--urls` command-line argument.</span></span>
* <span data-ttu-id="baeb6-577">La chiave di configurazione dell'host `urls`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-577">`urls` host configuration key.</span></span>
* <span data-ttu-id="baeb6-578">Il metodo di estensione `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-578">`UseUrls` extension method.</span></span>

<span data-ttu-id="baeb6-579">Il valore specificato usando i metodi seguenti può essere uno o più endpoint HTTP e HTTPS (HTTPS se è disponibile un certificato predefinito).</span><span class="sxs-lookup"><span data-stu-id="baeb6-579">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="baeb6-580">Configurare il valore come un elenco delimitato da punto e virgola (ad esempio, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="baeb6-580">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="baeb6-581">Per altre informazioni su questi approcci, vedere [URL del server](xref:fundamentals/host/web-host#server-urls) e [Override della configurazione](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="baeb6-581">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="baeb6-582">Viene creato un certificato di sviluppo nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-582">A development certificate is created:</span></span>

* <span data-ttu-id="baeb6-583">Quando viene installato [.NET Core SDK](/dotnet/core/sdk).</span><span class="sxs-lookup"><span data-stu-id="baeb6-583">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="baeb6-584">Quando per creare un certificato viene usato [lo strumento dev-certs](xref:aspnetcore-2.1#https).</span><span class="sxs-lookup"><span data-stu-id="baeb6-584">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="baeb6-585">Alcuni browser richiedono la concessione di autorizzazioni esplicite per considerare attendibile il certificato di sviluppo locale.</span><span class="sxs-lookup"><span data-stu-id="baeb6-585">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="baeb6-586">I modelli di progetto consentono di configurare le app per l'esecuzione su HTTPS per impostazione predefinita e includono il [reindirizzamento HTTPS e il supporto HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="baeb6-586">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="baeb6-587">Chiamare i metodi <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> oppure <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> su <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> per configurare le porte e i prefissi URL per Kestrel.</span><span class="sxs-lookup"><span data-stu-id="baeb6-587">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="baeb6-588">Anche `UseUrls`, l'argomento della riga di comando `--urls`, la chiave di configurazione dell'host `urls` e la variabile di ambiente `ASPNETCORE_URLS` funzionano, ma con le limitazioni indicate più avanti nella sezione (deve essere disponibile un certificato predefinito per la configurazione dell'endopoint HTTPS).</span><span class="sxs-lookup"><span data-stu-id="baeb6-588">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="baeb6-589">`KestrelServerOptions` configurazione</span><span class="sxs-lookup"><span data-stu-id="baeb6-589">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="baeb6-590">ConfigureEndpointDefaults (azione \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="baeb6-590">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="baeb6-591">Specifica un elemento di configurazione `Action` da eseguire per ogni endpoint specificato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-591">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="baeb6-592">Se si chiama `ConfigureEndpointDefaults` più volte, gli elementi `Action` precedenti vengono sostituiti con l'ultimo elemento `Action` specificato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-592">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="baeb6-593">Per gli endpoint creati chiamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **prima** della chiamata a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> non verranno applicati i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-593">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="baeb6-594">ConfigureHttpsDefaults (azione \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="baeb6-594">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="baeb6-595">Specifica un elemento di configurazione `Action` da eseguire per ogni endpoint HTTPS.</span><span class="sxs-lookup"><span data-stu-id="baeb6-595">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="baeb6-596">Se si chiama `ConfigureHttpsDefaults` più volte, gli elementi `Action` precedenti vengono sostituiti con l'ultimo elemento `Action` specificato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-596">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="baeb6-597">Per gli endpoint creati chiamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **prima** della chiamata a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> non verranno applicati i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-597">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="baeb6-598">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="baeb6-598">Configure(IConfiguration)</span></span>

<span data-ttu-id="baeb6-599">Crea un loader di configurazione per la configurazione di Kestrel che accetta un elemento <xref:Microsoft.Extensions.Configuration.IConfiguration> come input.</span><span class="sxs-lookup"><span data-stu-id="baeb6-599">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="baeb6-600">L'ambito della configurazione deve essere la sezione di configurazione per Kestrel.</span><span class="sxs-lookup"><span data-stu-id="baeb6-600">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="baeb6-601">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="baeb6-601">ListenOptions.UseHttps</span></span>

<span data-ttu-id="baeb6-602">Configura Kestrel per l'uso di HTTPS.</span><span class="sxs-lookup"><span data-stu-id="baeb6-602">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="baeb6-603">Estensioni `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="baeb6-603">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="baeb6-604">`UseHttps`: Configurare gheppio per l'uso di HTTPS con il certificato predefinito.</span><span class="sxs-lookup"><span data-stu-id="baeb6-604">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="baeb6-605">Genera un'eccezione se non è stato configurato alcun certificato predefinito.</span><span class="sxs-lookup"><span data-stu-id="baeb6-605">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="baeb6-606">Parametri `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="baeb6-606">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="baeb6-607">`filename` è il percorso e il nome file di un file di certificato, relativo alla directory che contiene i file di contenuto dell'app.</span><span class="sxs-lookup"><span data-stu-id="baeb6-607">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="baeb6-608">`password` è la password richiesta per accedere ai dati del certificato X.509.</span><span class="sxs-lookup"><span data-stu-id="baeb6-608">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="baeb6-609">`configureOptions` è un elemento `Action` per configurare `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-609">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="baeb6-610">Restituisce `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-610">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="baeb6-611">`storeName` è l'archivio certificati da cui caricare il certificato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-611">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="baeb6-612">`subject` è il nome dell'oggetto del certificato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-612">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="baeb6-613">`allowInvalid` indica se devono essere considerati i certificati non validi, come ad esempio i certificati autofirmati.</span><span class="sxs-lookup"><span data-stu-id="baeb6-613">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="baeb6-614">`location` è il percorso dell'archivio da cui caricare il certificato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-614">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="baeb6-615">`serverCertificate` è il certificato X.509.</span><span class="sxs-lookup"><span data-stu-id="baeb6-615">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="baeb6-616">Nell'ambiente di produzione, HTTPS deve essere configurato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="baeb6-616">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="baeb6-617">Come minimo, è necessario specificare un certificato predefinito.</span><span class="sxs-lookup"><span data-stu-id="baeb6-617">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="baeb6-618">Configurazioni supportate descritte in seguito:</span><span class="sxs-lookup"><span data-stu-id="baeb6-618">Supported configurations described next:</span></span>

* <span data-ttu-id="baeb6-619">Nessuna configurazione</span><span class="sxs-lookup"><span data-stu-id="baeb6-619">No configuration</span></span>
* <span data-ttu-id="baeb6-620">Sostituire il certificato predefinito della configurazione</span><span class="sxs-lookup"><span data-stu-id="baeb6-620">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="baeb6-621">Modificare le impostazioni predefinite nel codice</span><span class="sxs-lookup"><span data-stu-id="baeb6-621">Change the defaults in code</span></span>

<span data-ttu-id="baeb6-622">*Nessuna configurazione*</span><span class="sxs-lookup"><span data-stu-id="baeb6-622">*No configuration*</span></span>

<span data-ttu-id="baeb6-623">Kestrel è in ascolto su `http://localhost:5000` e `https://localhost:5001` (se è disponibile un certificato predefinito).</span><span class="sxs-lookup"><span data-stu-id="baeb6-623">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="baeb6-624">*Sostituire il certificato predefinito della configurazione*</span><span class="sxs-lookup"><span data-stu-id="baeb6-624">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="baeb6-625">`CreateDefaultBuilder` chiama `Configure(context.Configuration.GetSection("Kestrel"))` per impostazione predefinita per caricare la configurazione di Kestrel.</span><span class="sxs-lookup"><span data-stu-id="baeb6-625">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="baeb6-626">È disponibile per Kestrel uno schema di configurazione delle impostazioni delle app HTTPS predefinito.</span><span class="sxs-lookup"><span data-stu-id="baeb6-626">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="baeb6-627">Configurare più endpoint, inclusi gli URL e i certificati da usare, da un file su disco o da un archivio certificati.</span><span class="sxs-lookup"><span data-stu-id="baeb6-627">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="baeb6-628">Nel file *appsettings.json* di esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="baeb6-628">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="baeb6-629">Impostare **AllowInvalid** su `true` per consentire l'uso di certificati non validi, come ad esempio i certificati autofirmati.</span><span class="sxs-lookup"><span data-stu-id="baeb6-629">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="baeb6-630">Tutti gli endpoint HTTPS che non specificano un certificato (**HttpsDefaultCert** nell'esempio che segue) usano il certificato definito in **Certificates** > **Default** o il certificato di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="baeb6-630">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="baeb6-631">Un'alternativa all'uso di **Path** e **Password** per qualsiasi nodo del certificato consiste nello specificare il certificato usando i campi dell'archivio certificati.</span><span class="sxs-lookup"><span data-stu-id="baeb6-631">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="baeb6-632">Ad esempio, il certificato predefinito **Certificates**  >  **Default** può essere specificato come:</span><span class="sxs-lookup"><span data-stu-id="baeb6-632">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="baeb6-633">Note di schema:</span><span class="sxs-lookup"><span data-stu-id="baeb6-633">Schema notes:</span></span>

* <span data-ttu-id="baeb6-634">I nomi degli endpoint non applicano la distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="baeb6-634">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="baeb6-635">Ad esempio, sono validi sia `HTTPS` che `Https`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-635">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="baeb6-636">Il parametro `Url` è obbligatorio per ogni endpoint.</span><span class="sxs-lookup"><span data-stu-id="baeb6-636">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="baeb6-637">Il formato per questo parametro è uguale a quello del parametro di configurazione di primo livello `Urls`, con la differenza che si limita a un singolo valore.</span><span class="sxs-lookup"><span data-stu-id="baeb6-637">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="baeb6-638">Questi endpoint sostituiscono quelli definiti nella configurazione di primo livello `Urls`, non vi si aggiungono.</span><span class="sxs-lookup"><span data-stu-id="baeb6-638">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="baeb6-639">Gli endpoint definiti nel codice tramite `Listen` si aggiungono agli endpoint definiti nella sezione di configurazione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-639">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="baeb6-640">La sezione `Certificate` è facoltativa.</span><span class="sxs-lookup"><span data-stu-id="baeb6-640">The `Certificate` section is optional.</span></span> <span data-ttu-id="baeb6-641">Se la sezione `Certificate` non è specificata, vengono usati i valori predefiniti definiti negli scenari precedenti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-641">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="baeb6-642">Se non è disponibile alcun valore predefinito, il server genera un'eccezione e non viene avviato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-642">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="baeb6-643">La `Certificate` sezione supporta sia la password del **percorso** &ndash; **Password** che i certificati dell'archivio dell' **oggetto** &ndash; **Store** .</span><span class="sxs-lookup"><span data-stu-id="baeb6-643">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="baeb6-644">In questo modo è possibile definire un numero qualsiasi di endpoint, purché non provochino conflitti di porte.</span><span class="sxs-lookup"><span data-stu-id="baeb6-644">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="baeb6-645">`options.Configure(context.Configuration.GetSection("{SECTION}"))` restituisce un oggetto `KestrelConfigurationLoader` con un metodo `.Endpoint(string name, listenOptions => { })` che può essere usato per integrare le impostazioni dell'endpoint configurato:</span><span class="sxs-lookup"><span data-stu-id="baeb6-645">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="baeb6-646">`KestrelServerOptions.ConfigurationLoader` è possibile accedere direttamente a per continuare a scorrere sul caricatore esistente, ad esempio quello fornito da <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="baeb6-646">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="baeb6-647">La sezione di configurazione per ogni endpoint è disponibile nelle opzioni del `Endpoint` metodo in modo che sia possibile leggere le impostazioni personalizzate.</span><span class="sxs-lookup"><span data-stu-id="baeb6-647">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="baeb6-648">È possibile caricare più configurazioni chiamando ancora `options.Configure(context.Configuration.GetSection("{SECTION}"))` con un'altra sezione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-648">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="baeb6-649">Viene usata solo l'ultima configurazione, a meno che `Load` sia stato chiamato esplicitamente in istanze precedenti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-649">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="baeb6-650">Il metapacchetto non chiama `Load` in modo che la sezione di configurazione predefinita venga sostituita.</span><span class="sxs-lookup"><span data-stu-id="baeb6-650">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="baeb6-651">`KestrelConfigurationLoader` riflette la famiglia di API `Listen` da `KestrelServerOptions` all'overload di `Endpoint`, in modo che gli endpoint di codice e di configurazione possano essere configurati nella stessa posizione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-651">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="baeb6-652">Questi overload non usano nomi e usano solo le impostazioni predefinite della configurazione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-652">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="baeb6-653">*Modificare le impostazioni predefinite nel codice*</span><span class="sxs-lookup"><span data-stu-id="baeb6-653">*Change the defaults in code*</span></span>

<span data-ttu-id="baeb6-654">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` possono essere usati per modificare le impostazioni predefinite per `ListenOptions` e `HttpsConnectionAdapterOptions`, inclusa l'esecuzione dell'override del certificato predefinito specificato nello scenario precedente.</span><span class="sxs-lookup"><span data-stu-id="baeb6-654">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="baeb6-655">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devono essere chiamati prima che venga configurato qualsiasi endpoint.</span><span class="sxs-lookup"><span data-stu-id="baeb6-655">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="baeb6-656">*Supporto kestrel per SNI*</span><span class="sxs-lookup"><span data-stu-id="baeb6-656">*Kestrel support for SNI*</span></span>

<span data-ttu-id="baeb6-657">[L'Indicazione nome server (SNI)](https://tools.ietf.org/html/rfc6066#section-3) può essere usata per ospitare più domini sulla stessa porta e indirizzo IP.</span><span class="sxs-lookup"><span data-stu-id="baeb6-657">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="baeb6-658">Perché SNI funzioni è necessario che il client invii al server il nome host per la sessione protetta durante l'handshake TLS in modo che il server possa specificare il certificato corretto.</span><span class="sxs-lookup"><span data-stu-id="baeb6-658">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="baeb6-659">Il client usa il certificato specificato per la comunicazione crittografata con il server durante la sessione protetta che segue l'handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="baeb6-659">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="baeb6-660">Kestrel supporta SNI tramite il callback `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-660">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="baeb6-661">Il callback viene richiamato una volta per ogni connessione per consentire all'app di controllare il nome host e selezionare il certificato appropriato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-661">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="baeb6-662">Il supporto SNI richiede:</span><span class="sxs-lookup"><span data-stu-id="baeb6-662">SNI support requires:</span></span>

* <span data-ttu-id="baeb6-663">In esecuzione su Framework di destinazione `netcoreapp2.1` o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="baeb6-663">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="baeb6-664">In `net461` o versioni successive, il callback viene richiamato, ma `name` è sempre `null` .</span><span class="sxs-lookup"><span data-stu-id="baeb6-664">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="baeb6-665">L'elemento `name` è `null` anche se il client non specifica il parametro del nome host nell'handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="baeb6-665">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="baeb6-666">Esecuzione di tutti i siti Web nella stessa istanza di Kestrel.</span><span class="sxs-lookup"><span data-stu-id="baeb6-666">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="baeb6-667">Kestrel supporta la condivisione di un indirizzo IP e di una porta tra più istanze solo con un proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="baeb6-667">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a><span data-ttu-id="baeb6-668">Registrazione connessione</span><span class="sxs-lookup"><span data-stu-id="baeb6-668">Connection logging</span></span>

<span data-ttu-id="baeb6-669">Chiamare <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> per creare log a livello di debug per la comunicazione a livello di byte in una connessione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-669">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="baeb6-670">La registrazione delle connessioni è utile per la risoluzione dei problemi nella comunicazione di basso livello, ad esempio durante la crittografia TLS e dietro i proxy.</span><span class="sxs-lookup"><span data-stu-id="baeb6-670">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="baeb6-671">Se `UseConnectionLogging` viene inserito prima `UseHttps` , viene registrato il traffico crittografato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-671">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="baeb6-672">Se `UseConnectionLogging` viene inserito dopo `UseHttps` , viene registrato il traffico decrittografato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-672">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="baeb6-673">Associazione a un socket TCP</span><span class="sxs-lookup"><span data-stu-id="baeb6-673">Bind to a TCP socket</span></span>

<span data-ttu-id="baeb6-674">Il metodo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> esegue l'associazione a un socket TCP e un'espressione lambda per le opzioni consente di configurare un certificato X.509:</span><span class="sxs-lookup"><span data-stu-id="baeb6-674">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="baeb6-675">L'esempio configura HTTPS per un endpoint con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="baeb6-675">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="baeb6-676">Usare la stessa API per configurare altre impostazioni di Kestrel per endpoint specifici.</span><span class="sxs-lookup"><span data-stu-id="baeb6-676">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="baeb6-677">Associazione a un socket Unix</span><span class="sxs-lookup"><span data-stu-id="baeb6-677">Bind to a Unix socket</span></span>

<span data-ttu-id="baeb6-678">Impostare l'ascolto su un socket Unix con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> per migliorare le prestazioni con Nginx, come visualizzato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="baeb6-678">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="baeb6-679">Nel file nginx confiuguration impostare la `server`  >  `location`  >  `proxy_pass` voce su `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="baeb6-679">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="baeb6-680">`{KESTREL SOCKET}` nome del socket fornito a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (ad esempio, `kestrel-test.sock` nell'esempio precedente).</span><span class="sxs-lookup"><span data-stu-id="baeb6-680">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="baeb6-681">Verificare che il socket sia scrivibile da nginx (ad esempio, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="baeb6-681">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="baeb6-682">Porta 0</span><span class="sxs-lookup"><span data-stu-id="baeb6-682">Port 0</span></span>

<span data-ttu-id="baeb6-683">Quando si specifica il numero di porta `0`, Kestrel esegue l'associazione dinamica a una porta disponibile.</span><span class="sxs-lookup"><span data-stu-id="baeb6-683">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="baeb6-684">L'esempio seguente indica come determinare la porta alla quale Kestrel ha eseguito l'associazione in fase di esecuzione:</span><span class="sxs-lookup"><span data-stu-id="baeb6-684">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="baeb6-685">Quando si esegue l'app, l'output della finestra della console indica la porta dinamica in cui l'app può essere raggiunta:</span><span class="sxs-lookup"><span data-stu-id="baeb6-685">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="baeb6-686">Limitazioni</span><span class="sxs-lookup"><span data-stu-id="baeb6-686">Limitations</span></span>

<span data-ttu-id="baeb6-687">Configurare gli endpoint con i metodi seguenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-687">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="baeb6-688">L'argomento della riga di comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="baeb6-688">`--urls` command-line argument</span></span>
* <span data-ttu-id="baeb6-689">La chiave di configurazione dell'host `urls`</span><span class="sxs-lookup"><span data-stu-id="baeb6-689">`urls` host configuration key</span></span>
* <span data-ttu-id="baeb6-690">La variabile di ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="baeb6-690">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="baeb6-691">Questi metodi sono utili se si vuole che il codice funzioni con server diversi da Kestrel.</span><span class="sxs-lookup"><span data-stu-id="baeb6-691">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="baeb6-692">Tenere presenti, tuttavia, le limitazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-692">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="baeb6-693">HTTPS non può essere usato con questi approcci, a meno che non venga specificato un certificato predefinito nella configurazione dell'endpoint HTTPS (ad esempio, usando la configurazione `KestrelServerOptions` o un file di configurazione come illustrato in precedenza in questo argomento).</span><span class="sxs-lookup"><span data-stu-id="baeb6-693">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="baeb6-694">Quando sia l'approccio `Listen` che l'approccio `UseUrls` vengono usati contemporaneamente, gli endpoint `Listen` eseguono l'override degli endpoint `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-694">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="baeb6-695">Configurazione dell'endpoint IIS</span><span class="sxs-lookup"><span data-stu-id="baeb6-695">IIS endpoint configuration</span></span>

<span data-ttu-id="baeb6-696">Quando si usa IIS, le associazioni di URL per le associazioni di override di IIS vengono impostate mediante `Listen` o `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-696">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="baeb6-697">Per altre informazioni, vedere l'articolo [Introduzione al modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="baeb6-697">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="baeb6-698">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="baeb6-698">ListenOptions.Protocols</span></span>

<span data-ttu-id="baeb6-699">La proprietà `Protocols` stabilisce i protocolli HTTP (`HttpProtocols`) abilitati in un endpoint di connessione o per il server.</span><span class="sxs-lookup"><span data-stu-id="baeb6-699">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="baeb6-700">Assegnare un valore alla proprietà `Protocols` dall'enumerazione `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-700">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="baeb6-701">Valore di enumerazione `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="baeb6-701">`HttpProtocols` enum value</span></span> | <span data-ttu-id="baeb6-702">Protocollo di connessione consentito</span><span class="sxs-lookup"><span data-stu-id="baeb6-702">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="baeb6-703">Solo HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="baeb6-703">HTTP/1.1 only.</span></span> <span data-ttu-id="baeb6-704">Può essere usato con o senza TLS.</span><span class="sxs-lookup"><span data-stu-id="baeb6-704">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="baeb6-705">Solo HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="baeb6-705">HTTP/2 only.</span></span> <span data-ttu-id="baeb6-706">Può essere usato senza TLS solo se il client supporta una [modalità di conoscenza pregressa](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="baeb6-706">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="baeb6-707">HTTP/1.1 e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="baeb6-707">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="baeb6-708">HTTP/2 richiede una connessione TLS e [ALPN (Application-Layer Protocol negotiation)](https://tools.ietf.org/html/rfc7301#section-3) ; in caso contrario, il valore predefinito per la connessione è HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="baeb6-708">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="baeb6-709">Il protocollo predefinito è HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="baeb6-709">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="baeb6-710">Restrizioni relative a TLS per HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="baeb6-710">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="baeb6-711">TLS versione 1.2 o successiva</span><span class="sxs-lookup"><span data-stu-id="baeb6-711">TLS version 1.2 or later</span></span>
* <span data-ttu-id="baeb6-712">Rinegoziazione disabilitata</span><span class="sxs-lookup"><span data-stu-id="baeb6-712">Renegotiation disabled</span></span>
* <span data-ttu-id="baeb6-713">Compressione disabilitata</span><span class="sxs-lookup"><span data-stu-id="baeb6-713">Compression disabled</span></span>
* <span data-ttu-id="baeb6-714">Dimensioni minime per lo scambio di chiavi temporanee:</span><span class="sxs-lookup"><span data-stu-id="baeb6-714">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="baeb6-715">Diffie-Hellman a curva ellittica (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : minimo 224 bit</span><span class="sxs-lookup"><span data-stu-id="baeb6-715">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="baeb6-716">Diffie-Hellman campo finito (DHE) &lbrack; `TLS12` &rbrack; : minimo 2048 bit</span><span class="sxs-lookup"><span data-stu-id="baeb6-716">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="baeb6-717">Pacchetto di crittografia non bloccato</span><span class="sxs-lookup"><span data-stu-id="baeb6-717">Cipher suite not blocked</span></span>

<span data-ttu-id="baeb6-718">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; con la curva ellittica P-256 &lbrack; `FIPS186` &rbrack; è supportata per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="baeb6-718">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="baeb6-719">Nell'esempio seguente sono consentite connessioni HTTP/1.1 e HTTP/2 sulla porta 8000.</span><span class="sxs-lookup"><span data-stu-id="baeb6-719">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="baeb6-720">Le connessioni sono protette da TLS con un certificato incluso:</span><span class="sxs-lookup"><span data-stu-id="baeb6-720">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="baeb6-721">Facoltativamente, creare un'implementazione `IConnectionAdapter` per filtrare gli handshake TLS in base alla connessione in caso di crittografie specifiche:</span><span class="sxs-lookup"><span data-stu-id="baeb6-721">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="baeb6-722">*Impostare il protocollo dalla configurazione*</span><span class="sxs-lookup"><span data-stu-id="baeb6-722">*Set the protocol from configuration*</span></span>

<span data-ttu-id="baeb6-723"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> chiama `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` per impostazione predefinita per caricare la configurazione di Kestrel.</span><span class="sxs-lookup"><span data-stu-id="baeb6-723"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="baeb6-724">Nell'esempio *appsettings.json* seguente viene stabilito un protocollo di connessione predefinito (HTTP/1.1 e HTTP/2) per tutti gli endpoint Kestrel:</span><span class="sxs-lookup"><span data-stu-id="baeb6-724">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="baeb6-725">Il file di configurazione di esempio seguente stabilisce un protocollo di connessione per un endpoint specifico:</span><span class="sxs-lookup"><span data-stu-id="baeb6-725">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="baeb6-726">I protocolli specificati nei valori di override del codice sono impostati dalla configurazione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-726">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="baeb6-727">Configurazione del trasporto</span><span class="sxs-lookup"><span data-stu-id="baeb6-727">Transport configuration</span></span>

<span data-ttu-id="baeb6-728">Con la versione ASP.NET Core 2.1, il trasporto predefinito di Kestrel non si basa più su Libuv, ma su socket gestiti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-728">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="baeb6-729">Si tratta di una modifica importante per le app ASP.NET 2.0 Core che vengono aggiornate alla versione 2.1, che chiamano <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> e dipendono da uno dei pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-729">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="baeb6-730">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (riferimento diretto al pacchetto)</span><span class="sxs-lookup"><span data-stu-id="baeb6-730">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="baeb6-731">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="baeb6-731">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="baeb6-732">Per i progetti che richiedono l'uso di libuv:</span><span class="sxs-lookup"><span data-stu-id="baeb6-732">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="baeb6-733">Aggiungere una dipendenza per il pacchetto [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="baeb6-733">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="baeb6-734">Chiamare <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="baeb6-734">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="baeb6-735">Prefissi URL</span><span class="sxs-lookup"><span data-stu-id="baeb6-735">URL prefixes</span></span>

<span data-ttu-id="baeb6-736">Se si usa `UseUrls`, l'argomento della riga di comando `--urls`, la chiave di configurazione dell'host `urls` o la variabile di ambiente `ASPNETCORE_URLS`, i prefissi URL possono avere uno dei formati seguenti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-736">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="baeb6-737">Sono validi solo i prefissi URL HTTP.</span><span class="sxs-lookup"><span data-stu-id="baeb6-737">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="baeb6-738">Kestrel non supporta HTTPS quando la configurazione delle associazioni URL viene eseguita con `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-738">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="baeb6-739">Indirizzo IPv4 con numero di porta</span><span class="sxs-lookup"><span data-stu-id="baeb6-739">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="baeb6-740">`0.0.0.0` è un caso speciale che esegue l'associazione a tutti gli indirizzi IPv4.</span><span class="sxs-lookup"><span data-stu-id="baeb6-740">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="baeb6-741">Indirizzo IPv6 con numero di porta</span><span class="sxs-lookup"><span data-stu-id="baeb6-741">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="baeb6-742">`[::]` è l'equivalente IPv6 di `0.0.0.0` per IPv4.</span><span class="sxs-lookup"><span data-stu-id="baeb6-742">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="baeb6-743">Nome host con numero di porta</span><span class="sxs-lookup"><span data-stu-id="baeb6-743">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="baeb6-744">I nomi host, `*` e `+` non sono casi particolari.</span><span class="sxs-lookup"><span data-stu-id="baeb6-744">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="baeb6-745">Tutto ciò che non è riconosciuto come un indirizzo IP o un elemento `localhost` valido esegue l'associazione a tutti gli IP IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="baeb6-745">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="baeb6-746">Per associare nomi host diversi ad app ASP.NET Core diverse sulla stessa porta, usare [HTTP.sys](xref:fundamentals/servers/httpsys) o un server proxy inverso come IIS, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="baeb6-746">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="baeb6-747">Una configurazione che prevede un proxy inverso richiede il [filtro host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="baeb6-747">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="baeb6-748">Nome host `localhost` con numero di porta o IP di loopback con numero di porta</span><span class="sxs-lookup"><span data-stu-id="baeb6-748">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="baeb6-749">Se è specificato `localhost`, Kestrel tenta l'associazione alle interfacce di loopback sia IPv4 che IPv6.</span><span class="sxs-lookup"><span data-stu-id="baeb6-749">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="baeb6-750">Se la porta richiesta è in uso da parte di un altro servizio in una delle due interfacce di loopback, Kestrel non viene avviato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-750">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="baeb6-751">Se una delle due interfacce di loopback non è disponibile per qualsiasi altro motivo (in genere perché IPv6 non è supportato), Kestrel registra un avviso.</span><span class="sxs-lookup"><span data-stu-id="baeb6-751">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="baeb6-752">Filtro host</span><span class="sxs-lookup"><span data-stu-id="baeb6-752">Host filtering</span></span>

<span data-ttu-id="baeb6-753">Mentre supporta la configurazione in base ai prefissi, ad esempio `http://example.com:5000`, Kestrel ignora quasi sempre il nome host.</span><span class="sxs-lookup"><span data-stu-id="baeb6-753">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="baeb6-754">L'host `localhost` è un caso speciale usato per l'associazione agli indirizzi di loopback.</span><span class="sxs-lookup"><span data-stu-id="baeb6-754">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="baeb6-755">Qualsiasi host che non sia un indirizzo IP esplicito esegue l'associazione a tutti gli indirizzi IP pubblici.</span><span class="sxs-lookup"><span data-stu-id="baeb6-755">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="baeb6-756">Le intestazioni `Host` non vengono convalidate.</span><span class="sxs-lookup"><span data-stu-id="baeb6-756">`Host` headers aren't validated.</span></span>

<span data-ttu-id="baeb6-757">Come soluzione alternativa, usare il middleware di filtro host.</span><span class="sxs-lookup"><span data-stu-id="baeb6-757">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="baeb6-758">Il middleware di filtro host viene fornito dal pacchetto [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , incluso nel [metapacchetto Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 o 2,2).</span><span class="sxs-lookup"><span data-stu-id="baeb6-758">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="baeb6-759">Il middleware viene aggiunto da <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, che chiama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="baeb6-759">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="baeb6-760">Per impostazione predefinita, il middleware di filtro host è disabilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="baeb6-760">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="baeb6-761">Per abilitare il middleware, definire una `AllowedHosts` chiave in *appsettings.jssu* / *appSettings. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="baeb6-761">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="baeb6-762">Il valore è un elenco con valori delimitati da punto e virgola di nomi host senza numeri di porta:</span><span class="sxs-lookup"><span data-stu-id="baeb6-762">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="baeb6-763">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="baeb6-763">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="baeb6-764">Il [middleware delle intestazioni inoltrate](xref:host-and-deploy/proxy-load-balancer) include anche un'opzione <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="baeb6-764">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="baeb6-765">Il middleware di intestazioni inoltrate e il middleware di filtro host hanno funzionalità simili per diversi scenari.</span><span class="sxs-lookup"><span data-stu-id="baeb6-765">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="baeb6-766">L'impostazione di `AllowedHosts` con il middleware delle intestazioni inoltrate è appropriato se l'intestazione `Host` non viene mantenuta durante l'inoltro delle richieste con un server proxy inverso o il bilanciamento del carico.</span><span class="sxs-lookup"><span data-stu-id="baeb6-766">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="baeb6-767">L'impostazione di `AllowedHosts` con il middleware del filtro host è appropriata se si usa Kestrel come server perimetrale rivolto al pubblico o se l'intestazione `Host` viene inoltrata direttamente.</span><span class="sxs-lookup"><span data-stu-id="baeb6-767">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="baeb6-768">Per altre informazioni sul middleware delle intestazioni inoltrate, vedere <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="baeb6-768">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="baeb6-769">Kestrel è un [server Web per ASP.NET Core](xref:fundamentals/servers/index) multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="baeb6-769">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="baeb6-770">Kestrel è il server Web incluso per impostazione predefinita nei modelli di progetto di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="baeb6-770">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="baeb6-771">Kestrel supporta gli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-771">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="baeb6-772">HTTPS</span><span class="sxs-lookup"><span data-stu-id="baeb6-772">HTTPS</span></span>
* <span data-ttu-id="baeb6-773">Aggiornamento opaco usato per abilitare [WebSocket](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="baeb6-773">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="baeb6-774">Socket Unix ad alte prestazioni dietro Nginx</span><span class="sxs-lookup"><span data-stu-id="baeb6-774">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="baeb6-775">Kestrel è supportato in tutte le piattaforme e le versioni supportate da .NET Core.</span><span class="sxs-lookup"><span data-stu-id="baeb6-775">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="baeb6-776">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="baeb6-776">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="baeb6-777">Quando usare Kestrel con un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="baeb6-777">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="baeb6-778">È possibile usare Kestrel da solo o in combinazione con un *server proxy inverso*, ad esempio [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="baeb6-778">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="baeb6-779">Il server proxy inverso riceve le richieste HTTP dalla rete e le inoltra a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="baeb6-779">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="baeb6-780">Kestrel usato come server Web perimetrale (esposto a Internet):</span><span class="sxs-lookup"><span data-stu-id="baeb6-780">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel comunica direttamente con Internet senza un server proxy inverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="baeb6-782">Kestrel usato in una configurazione proxy inverso:</span><span class="sxs-lookup"><span data-stu-id="baeb6-782">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel comunica indirettamente con Internet attraverso un server proxy inverso, ad esempio IIS, Nginx o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="baeb6-784">La configurazione, con o senza un server proxy inverso, è una configurazione di hosting supportata.</span><span class="sxs-lookup"><span data-stu-id="baeb6-784">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="baeb6-785">Se viene utilizzato come server perimetrale in assenza di un server proxy inverso, Kestrel non supporta la condivisione tra più processi dell'IP e della porta.</span><span class="sxs-lookup"><span data-stu-id="baeb6-785">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="baeb6-786">Quando è configurato per restare in ascolto su una porta, Kestrel gestisce tutto il traffico per tale porta indipendentemente dalle intestazioni `Host` delle richieste.</span><span class="sxs-lookup"><span data-stu-id="baeb6-786">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="baeb6-787">Un proxy inverso che può condividere le porte può eseguire l'inoltro delle richieste a Kestrel su un unico IP e un'unica porta.</span><span class="sxs-lookup"><span data-stu-id="baeb6-787">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="baeb6-788">Anche se la presenza di un server proxy inverso non è necessaria, può risultare utile per i motivi seguenti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-788">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="baeb6-789">Un proxy inverso:</span><span class="sxs-lookup"><span data-stu-id="baeb6-789">A reverse proxy:</span></span>

* <span data-ttu-id="baeb6-790">Può limitare l'area della superficie di attacco pubblica esposta delle app ospitate.</span><span class="sxs-lookup"><span data-stu-id="baeb6-790">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="baeb6-791">Offre un livello aggiuntivo di configurazione e protezione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-791">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="baeb6-792">Potrebbe offrire un'integrazione migliore con l'infrastruttura esistente.</span><span class="sxs-lookup"><span data-stu-id="baeb6-792">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="baeb6-793">Semplifica il bilanciamento del carico e la configurazione di comunicazioni protette (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="baeb6-793">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="baeb6-794">Solo il server proxy inverso richiede un certificato X. 509 e il server è in grado di comunicare con i server dell'app nella rete interna tramite HTTP normale.</span><span class="sxs-lookup"><span data-stu-id="baeb6-794">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="baeb6-795">Una configurazione che prevede un proxy inverso richiede il [filtro host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="baeb6-795">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="baeb6-796">Come usare Kestrel nelle app ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="baeb6-796">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="baeb6-797">Il pacchetto [Microsoft. AspNetCore. Server. gheppio](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) è incluso nel [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="baeb6-797">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="baeb6-798">I modelli di progetto ASP.NET Core usano Kestrel per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="baeb6-798">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="baeb6-799">In *Program.cs* il codice del modello chiama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, che chiama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> in background.</span><span class="sxs-lookup"><span data-stu-id="baeb6-799">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="baeb6-800">Per fornire una configurazione aggiuntiva dopo la chiamata di `CreateDefaultBuilder`, chiamare <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="baeb6-800">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="baeb6-801">Per ulteriori informazioni su `CreateDefaultBuilder` e sulla compilazione dell'host, vedere la sezione *configurare un host* di <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="baeb6-801">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="baeb6-802">Opzioni Kestrel</span><span class="sxs-lookup"><span data-stu-id="baeb6-802">Kestrel options</span></span>

<span data-ttu-id="baeb6-803">Il server Web Kestrel dispone di opzioni di configurazione dei vincoli che risultano particolarmente utili nelle distribuzioni con connessione Internet.</span><span class="sxs-lookup"><span data-stu-id="baeb6-803">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="baeb6-804">Impostare i vincoli per la proprietà <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> della classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="baeb6-804">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="baeb6-805">La proprietà `Limits` contiene un'istanza della classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="baeb6-805">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="baeb6-806">Negli esempi seguenti viene usato lo spazio dei nomi <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="baeb6-806">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="baeb6-807">Le opzioni gheppio, che sono configurate nel codice C# negli esempi seguenti, possono essere impostate anche usando un [provider di configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="baeb6-807">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="baeb6-808">Il provider di configurazione file, ad esempio, può caricare la configurazione di Gheppio da un *appsettings.jssu* o *appSettings. { File Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="baeb6-808">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="baeb6-809">Usare **uno** degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-809">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="baeb6-810">Configurare gheppio in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="baeb6-810">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="baeb6-811">Inserire un'istanza di `IConfiguration` nella `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="baeb6-811">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="baeb6-812">Nell'esempio seguente si presuppone che la configurazione inserita venga assegnata alla `Configuration` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="baeb6-812">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="baeb6-813">In `Startup.ConfigureServices` caricare la `Kestrel` sezione della configurazione nella configurazione di Gheppio:</span><span class="sxs-lookup"><span data-stu-id="baeb6-813">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="baeb6-814">Configurare il gheppio durante la compilazione dell'host:</span><span class="sxs-lookup"><span data-stu-id="baeb6-814">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="baeb6-815">In *Program.cs*caricare la `Kestrel` sezione della configurazione nella configurazione di Gheppio:</span><span class="sxs-lookup"><span data-stu-id="baeb6-815">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="baeb6-816">Entrambi gli approcci precedenti funzionano con qualsiasi [provider di configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="baeb6-816">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="baeb6-817">Timeout keep-alive</span><span class="sxs-lookup"><span data-stu-id="baeb6-817">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="baeb6-818">Ottiene o imposta il [timeout keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="baeb6-818">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="baeb6-819">Il valore predefinito è 2 minuti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-819">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="baeb6-820">Numero massimo di connessioni client</span><span class="sxs-lookup"><span data-stu-id="baeb6-820">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="baeb6-821">È possibile impostare il numero massimo di connessioni TCP aperte simultaneamente per l'intera app con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="baeb6-821">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="baeb6-822">È previsto un limite separato per le connessioni che sono state aggiornate da HTTP o HTTPS a un altro protocollo (ad esempio su una richiesta WebSocket).</span><span class="sxs-lookup"><span data-stu-id="baeb6-822">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="baeb6-823">Dopo l'aggiornamento di una connessione, questa non viene conteggiata per il limite `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-823">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="baeb6-824">Per impostazione predefinita, il numero massimo di connessioni è illimitato (null).</span><span class="sxs-lookup"><span data-stu-id="baeb6-824">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="baeb6-825">Dimensione massima del corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="baeb6-825">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="baeb6-826">La dimensione massima predefinita del corpo della richiesta è pari a 30.000.000 di byte, equivalenti a circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="baeb6-826">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="baeb6-827">Il metodo consigliato per ignorare il limite in un'app ASP.NET Core MVC è l'uso dell'attributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> in un metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="baeb6-827">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="baeb6-828">L'esempio seguente illustra come configurare il vincolo per l'app in ogni richiesta:</span><span class="sxs-lookup"><span data-stu-id="baeb6-828">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="baeb6-829">Eseguire l'override dell'impostazione per una richiesta specifica nel middleware:</span><span class="sxs-lookup"><span data-stu-id="baeb6-829">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="baeb6-830">Viene generata un'eccezione se l'app configura il limite per una richiesta dopo che l'app ha iniziato a leggere la richiesta.</span><span class="sxs-lookup"><span data-stu-id="baeb6-830">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="baeb6-831">Una proprietà `IsReadOnly` indica se la proprietà `MaxRequestBodySize` è in stato di sola lettura e pertanto è troppo tardi per configurare il limite.</span><span class="sxs-lookup"><span data-stu-id="baeb6-831">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="baeb6-832">Quando un'app viene eseguita [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) dietro il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), il limite della dimensione del corpo della richiesta di Kestrel è disabilitato perché viene già impostato da IIS.</span><span class="sxs-lookup"><span data-stu-id="baeb6-832">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="baeb6-833">Velocità minima dei dati del corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="baeb6-833">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="baeb6-834">Kestrel controlla ogni secondo se i dati arrivano alla velocità in byte al secondo specificata.</span><span class="sxs-lookup"><span data-stu-id="baeb6-834">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="baeb6-835">Se la frequenza scende sotto il valore minimo, si è verificato il timeout della connessione. Il periodo di tolleranza è la quantità di tempo che il gheppio concede al client di aumentare la velocità di invio fino al minimo. la frequenza non viene controllata durante tale periodo di tempo.</span><span class="sxs-lookup"><span data-stu-id="baeb6-835">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="baeb6-836">Il periodo di prova consente di evitare l'interruzione di connessioni che inizialmente inviano i dati a velocità ridotta a causa dell'avvio lento del protocollo TCP.</span><span class="sxs-lookup"><span data-stu-id="baeb6-836">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="baeb6-837">La velocità minima predefinita è di 240 byte al secondo, con un periodo di tolleranza di 5 secondi.</span><span class="sxs-lookup"><span data-stu-id="baeb6-837">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="baeb6-838">Anche per la risposta è prevista una velocità minima.</span><span class="sxs-lookup"><span data-stu-id="baeb6-838">A minimum rate also applies to the response.</span></span> <span data-ttu-id="baeb6-839">Il codice per impostare il limite della richiesta e della risposta è identico e varia solo per `RequestBody` o `Response` nei nomi di proprietà e di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="baeb6-839">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="baeb6-840">L'esempio seguente visualizza come configurare la velocità minima dei dati in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="baeb6-840">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="baeb6-841">Timeout delle intestazioni delle richieste</span><span class="sxs-lookup"><span data-stu-id="baeb6-841">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="baeb6-842">Ottiene o imposta la quantità massima di tempo che il server dedica alla ricezione delle intestazioni delle richieste.</span><span class="sxs-lookup"><span data-stu-id="baeb6-842">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="baeb6-843">Il valore predefinito è 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="baeb6-843">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="baeb6-844">I/O sincrono</span><span class="sxs-lookup"><span data-stu-id="baeb6-844">Synchronous I/O</span></span>

<span data-ttu-id="baeb6-845"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> Controlla se è consentito l'I/O sincrono per la richiesta e la risposta.</span><span class="sxs-lookup"><span data-stu-id="baeb6-845"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="baeb6-846">Il valore predefinito è `true`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-846">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="baeb6-847">Un numero elevato di operazioni di I/O sincrone bloccanti può causare l'esaurimento del pool di thread, il che rende l'app non risponde.</span><span class="sxs-lookup"><span data-stu-id="baeb6-847">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="baeb6-848">Abilitare solo `AllowSynchronousIO` quando si usa una libreria che non supporta l'I/O asincrono.</span><span class="sxs-lookup"><span data-stu-id="baeb6-848">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="baeb6-849">Nell'esempio seguente viene disabilitato l'I/O sincrono:</span><span class="sxs-lookup"><span data-stu-id="baeb6-849">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="baeb6-850">Per informazioni su altre opzioni e limiti di Kestrel, vedere:</span><span class="sxs-lookup"><span data-stu-id="baeb6-850">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="baeb6-851">Configurazione dell'endpoint</span><span class="sxs-lookup"><span data-stu-id="baeb6-851">Endpoint configuration</span></span>

<span data-ttu-id="baeb6-852">Per impostazione predefinita, ASP.NET Core è associato a:</span><span class="sxs-lookup"><span data-stu-id="baeb6-852">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="baeb6-853">`https://localhost:5001` (quando è presente un certificato di sviluppo locale)</span><span class="sxs-lookup"><span data-stu-id="baeb6-853">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="baeb6-854">Specificare gli URL usando gli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-854">Specify URLs using the:</span></span>

* <span data-ttu-id="baeb6-855">La variabile di ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-855">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="baeb6-856">L'argomento della riga di comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-856">`--urls` command-line argument.</span></span>
* <span data-ttu-id="baeb6-857">La chiave di configurazione dell'host `urls`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-857">`urls` host configuration key.</span></span>
* <span data-ttu-id="baeb6-858">Il metodo di estensione `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-858">`UseUrls` extension method.</span></span>

<span data-ttu-id="baeb6-859">Il valore specificato usando i metodi seguenti può essere uno o più endpoint HTTP e HTTPS (HTTPS se è disponibile un certificato predefinito).</span><span class="sxs-lookup"><span data-stu-id="baeb6-859">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="baeb6-860">Configurare il valore come un elenco delimitato da punto e virgola (ad esempio, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="baeb6-860">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="baeb6-861">Per altre informazioni su questi approcci, vedere [URL del server](xref:fundamentals/host/web-host#server-urls) e [Override della configurazione](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="baeb6-861">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="baeb6-862">Viene creato un certificato di sviluppo nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-862">A development certificate is created:</span></span>

* <span data-ttu-id="baeb6-863">Quando viene installato [.NET Core SDK](/dotnet/core/sdk).</span><span class="sxs-lookup"><span data-stu-id="baeb6-863">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="baeb6-864">Quando per creare un certificato viene usato [lo strumento dev-certs](xref:aspnetcore-2.1#https).</span><span class="sxs-lookup"><span data-stu-id="baeb6-864">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="baeb6-865">Alcuni browser richiedono la concessione di autorizzazioni esplicite per considerare attendibile il certificato di sviluppo locale.</span><span class="sxs-lookup"><span data-stu-id="baeb6-865">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="baeb6-866">I modelli di progetto consentono di configurare le app per l'esecuzione su HTTPS per impostazione predefinita e includono il [reindirizzamento HTTPS e il supporto HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="baeb6-866">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="baeb6-867">Chiamare i metodi <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> oppure <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> su <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> per configurare le porte e i prefissi URL per Kestrel.</span><span class="sxs-lookup"><span data-stu-id="baeb6-867">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="baeb6-868">Anche `UseUrls`, l'argomento della riga di comando `--urls`, la chiave di configurazione dell'host `urls` e la variabile di ambiente `ASPNETCORE_URLS` funzionano, ma con le limitazioni indicate più avanti nella sezione (deve essere disponibile un certificato predefinito per la configurazione dell'endopoint HTTPS).</span><span class="sxs-lookup"><span data-stu-id="baeb6-868">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="baeb6-869">`KestrelServerOptions` configurazione</span><span class="sxs-lookup"><span data-stu-id="baeb6-869">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="baeb6-870">ConfigureEndpointDefaults (azione \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="baeb6-870">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="baeb6-871">Specifica un elemento di configurazione `Action` da eseguire per ogni endpoint specificato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-871">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="baeb6-872">Se si chiama `ConfigureEndpointDefaults` più volte, gli elementi `Action` precedenti vengono sostituiti con l'ultimo elemento `Action` specificato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-872">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="baeb6-873">Per gli endpoint creati chiamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **prima** della chiamata a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> non verranno applicati i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-873">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="baeb6-874">ConfigureHttpsDefaults (azione \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="baeb6-874">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="baeb6-875">Specifica un elemento di configurazione `Action` da eseguire per ogni endpoint HTTPS.</span><span class="sxs-lookup"><span data-stu-id="baeb6-875">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="baeb6-876">Se si chiama `ConfigureHttpsDefaults` più volte, gli elementi `Action` precedenti vengono sostituiti con l'ultimo elemento `Action` specificato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-876">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="baeb6-877">Per gli endpoint creati chiamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **prima** della chiamata a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> non verranno applicati i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-877">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="baeb6-878">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="baeb6-878">Configure(IConfiguration)</span></span>

<span data-ttu-id="baeb6-879">Crea un loader di configurazione per la configurazione di Kestrel che accetta un elemento <xref:Microsoft.Extensions.Configuration.IConfiguration> come input.</span><span class="sxs-lookup"><span data-stu-id="baeb6-879">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="baeb6-880">L'ambito della configurazione deve essere la sezione di configurazione per Kestrel.</span><span class="sxs-lookup"><span data-stu-id="baeb6-880">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="baeb6-881">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="baeb6-881">ListenOptions.UseHttps</span></span>

<span data-ttu-id="baeb6-882">Configura Kestrel per l'uso di HTTPS.</span><span class="sxs-lookup"><span data-stu-id="baeb6-882">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="baeb6-883">Estensioni `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="baeb6-883">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="baeb6-884">`UseHttps`: Configurare gheppio per l'uso di HTTPS con il certificato predefinito.</span><span class="sxs-lookup"><span data-stu-id="baeb6-884">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="baeb6-885">Genera un'eccezione se non è stato configurato alcun certificato predefinito.</span><span class="sxs-lookup"><span data-stu-id="baeb6-885">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="baeb6-886">Parametri `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="baeb6-886">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="baeb6-887">`filename` è il percorso e il nome file di un file di certificato, relativo alla directory che contiene i file di contenuto dell'app.</span><span class="sxs-lookup"><span data-stu-id="baeb6-887">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="baeb6-888">`password` è la password richiesta per accedere ai dati del certificato X.509.</span><span class="sxs-lookup"><span data-stu-id="baeb6-888">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="baeb6-889">`configureOptions` è un elemento `Action` per configurare `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-889">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="baeb6-890">Restituisce `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-890">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="baeb6-891">`storeName` è l'archivio certificati da cui caricare il certificato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-891">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="baeb6-892">`subject` è il nome dell'oggetto del certificato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-892">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="baeb6-893">`allowInvalid` indica se devono essere considerati i certificati non validi, come ad esempio i certificati autofirmati.</span><span class="sxs-lookup"><span data-stu-id="baeb6-893">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="baeb6-894">`location` è il percorso dell'archivio da cui caricare il certificato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-894">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="baeb6-895">`serverCertificate` è il certificato X.509.</span><span class="sxs-lookup"><span data-stu-id="baeb6-895">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="baeb6-896">Nell'ambiente di produzione, HTTPS deve essere configurato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="baeb6-896">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="baeb6-897">Come minimo, è necessario specificare un certificato predefinito.</span><span class="sxs-lookup"><span data-stu-id="baeb6-897">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="baeb6-898">Configurazioni supportate descritte in seguito:</span><span class="sxs-lookup"><span data-stu-id="baeb6-898">Supported configurations described next:</span></span>

* <span data-ttu-id="baeb6-899">Nessuna configurazione</span><span class="sxs-lookup"><span data-stu-id="baeb6-899">No configuration</span></span>
* <span data-ttu-id="baeb6-900">Sostituire il certificato predefinito della configurazione</span><span class="sxs-lookup"><span data-stu-id="baeb6-900">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="baeb6-901">Modificare le impostazioni predefinite nel codice</span><span class="sxs-lookup"><span data-stu-id="baeb6-901">Change the defaults in code</span></span>

<span data-ttu-id="baeb6-902">*Nessuna configurazione*</span><span class="sxs-lookup"><span data-stu-id="baeb6-902">*No configuration*</span></span>

<span data-ttu-id="baeb6-903">Kestrel è in ascolto su `http://localhost:5000` e `https://localhost:5001` (se è disponibile un certificato predefinito).</span><span class="sxs-lookup"><span data-stu-id="baeb6-903">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="baeb6-904">*Sostituire il certificato predefinito della configurazione*</span><span class="sxs-lookup"><span data-stu-id="baeb6-904">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="baeb6-905">`CreateDefaultBuilder` chiama `Configure(context.Configuration.GetSection("Kestrel"))` per impostazione predefinita per caricare la configurazione di Kestrel.</span><span class="sxs-lookup"><span data-stu-id="baeb6-905">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="baeb6-906">È disponibile per Kestrel uno schema di configurazione delle impostazioni delle app HTTPS predefinito.</span><span class="sxs-lookup"><span data-stu-id="baeb6-906">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="baeb6-907">Configurare più endpoint, inclusi gli URL e i certificati da usare, da un file su disco o da un archivio certificati.</span><span class="sxs-lookup"><span data-stu-id="baeb6-907">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="baeb6-908">Nel file *appsettings.json* di esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="baeb6-908">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="baeb6-909">Impostare **AllowInvalid** su `true` per consentire l'uso di certificati non validi, come ad esempio i certificati autofirmati.</span><span class="sxs-lookup"><span data-stu-id="baeb6-909">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="baeb6-910">Tutti gli endpoint HTTPS che non specificano un certificato (**HttpsDefaultCert** nell'esempio che segue) usano il certificato definito in **Certificates** > **Default** o il certificato di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="baeb6-910">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="baeb6-911">Un'alternativa all'uso di **Path** e **Password** per qualsiasi nodo del certificato consiste nello specificare il certificato usando i campi dell'archivio certificati.</span><span class="sxs-lookup"><span data-stu-id="baeb6-911">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="baeb6-912">Ad esempio, il certificato predefinito **Certificates**  >  **Default** può essere specificato come:</span><span class="sxs-lookup"><span data-stu-id="baeb6-912">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="baeb6-913">Note di schema:</span><span class="sxs-lookup"><span data-stu-id="baeb6-913">Schema notes:</span></span>

* <span data-ttu-id="baeb6-914">I nomi degli endpoint non applicano la distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="baeb6-914">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="baeb6-915">Ad esempio, sono validi sia `HTTPS` che `Https`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-915">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="baeb6-916">Il parametro `Url` è obbligatorio per ogni endpoint.</span><span class="sxs-lookup"><span data-stu-id="baeb6-916">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="baeb6-917">Il formato per questo parametro è uguale a quello del parametro di configurazione di primo livello `Urls`, con la differenza che si limita a un singolo valore.</span><span class="sxs-lookup"><span data-stu-id="baeb6-917">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="baeb6-918">Questi endpoint sostituiscono quelli definiti nella configurazione di primo livello `Urls`, non vi si aggiungono.</span><span class="sxs-lookup"><span data-stu-id="baeb6-918">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="baeb6-919">Gli endpoint definiti nel codice tramite `Listen` si aggiungono agli endpoint definiti nella sezione di configurazione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-919">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="baeb6-920">La sezione `Certificate` è facoltativa.</span><span class="sxs-lookup"><span data-stu-id="baeb6-920">The `Certificate` section is optional.</span></span> <span data-ttu-id="baeb6-921">Se la sezione `Certificate` non è specificata, vengono usati i valori predefiniti definiti negli scenari precedenti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-921">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="baeb6-922">Se non è disponibile alcun valore predefinito, il server genera un'eccezione e non viene avviato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-922">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="baeb6-923">La `Certificate` sezione supporta sia la password del **percorso** &ndash; **Password** che i certificati dell'archivio dell' **oggetto** &ndash; **Store** .</span><span class="sxs-lookup"><span data-stu-id="baeb6-923">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="baeb6-924">In questo modo è possibile definire un numero qualsiasi di endpoint, purché non provochino conflitti di porte.</span><span class="sxs-lookup"><span data-stu-id="baeb6-924">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="baeb6-925">`options.Configure(context.Configuration.GetSection("{SECTION}"))` restituisce un oggetto `KestrelConfigurationLoader` con un metodo `.Endpoint(string name, listenOptions => { })` che può essere usato per integrare le impostazioni dell'endpoint configurato:</span><span class="sxs-lookup"><span data-stu-id="baeb6-925">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="baeb6-926">`KestrelServerOptions.ConfigurationLoader` è possibile accedere direttamente a per continuare a scorrere sul caricatore esistente, ad esempio quello fornito da <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="baeb6-926">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="baeb6-927">La sezione di configurazione per ogni endpoint è disponibile nelle opzioni del `Endpoint` metodo in modo che sia possibile leggere le impostazioni personalizzate.</span><span class="sxs-lookup"><span data-stu-id="baeb6-927">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="baeb6-928">È possibile caricare più configurazioni chiamando ancora `options.Configure(context.Configuration.GetSection("{SECTION}"))` con un'altra sezione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-928">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="baeb6-929">Viene usata solo l'ultima configurazione, a meno che `Load` sia stato chiamato esplicitamente in istanze precedenti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-929">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="baeb6-930">Il metapacchetto non chiama `Load` in modo che la sezione di configurazione predefinita venga sostituita.</span><span class="sxs-lookup"><span data-stu-id="baeb6-930">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="baeb6-931">`KestrelConfigurationLoader` riflette la famiglia di API `Listen` da `KestrelServerOptions` all'overload di `Endpoint`, in modo che gli endpoint di codice e di configurazione possano essere configurati nella stessa posizione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-931">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="baeb6-932">Questi overload non usano nomi e usano solo le impostazioni predefinite della configurazione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-932">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="baeb6-933">*Modificare le impostazioni predefinite nel codice*</span><span class="sxs-lookup"><span data-stu-id="baeb6-933">*Change the defaults in code*</span></span>

<span data-ttu-id="baeb6-934">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` possono essere usati per modificare le impostazioni predefinite per `ListenOptions` e `HttpsConnectionAdapterOptions`, inclusa l'esecuzione dell'override del certificato predefinito specificato nello scenario precedente.</span><span class="sxs-lookup"><span data-stu-id="baeb6-934">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="baeb6-935">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devono essere chiamati prima che venga configurato qualsiasi endpoint.</span><span class="sxs-lookup"><span data-stu-id="baeb6-935">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="baeb6-936">*Supporto kestrel per SNI*</span><span class="sxs-lookup"><span data-stu-id="baeb6-936">*Kestrel support for SNI*</span></span>

<span data-ttu-id="baeb6-937">[L'Indicazione nome server (SNI)](https://tools.ietf.org/html/rfc6066#section-3) può essere usata per ospitare più domini sulla stessa porta e indirizzo IP.</span><span class="sxs-lookup"><span data-stu-id="baeb6-937">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="baeb6-938">Perché SNI funzioni è necessario che il client invii al server il nome host per la sessione protetta durante l'handshake TLS in modo che il server possa specificare il certificato corretto.</span><span class="sxs-lookup"><span data-stu-id="baeb6-938">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="baeb6-939">Il client usa il certificato specificato per la comunicazione crittografata con il server durante la sessione protetta che segue l'handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="baeb6-939">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="baeb6-940">Kestrel supporta SNI tramite il callback `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-940">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="baeb6-941">Il callback viene richiamato una volta per ogni connessione per consentire all'app di controllare il nome host e selezionare il certificato appropriato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-941">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="baeb6-942">Il supporto SNI richiede:</span><span class="sxs-lookup"><span data-stu-id="baeb6-942">SNI support requires:</span></span>

* <span data-ttu-id="baeb6-943">In esecuzione su Framework di destinazione `netcoreapp2.1` o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="baeb6-943">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="baeb6-944">In `net461` o versioni successive, il callback viene richiamato, ma `name` è sempre `null` .</span><span class="sxs-lookup"><span data-stu-id="baeb6-944">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="baeb6-945">L'elemento `name` è `null` anche se il client non specifica il parametro del nome host nell'handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="baeb6-945">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="baeb6-946">Esecuzione di tutti i siti Web nella stessa istanza di Kestrel.</span><span class="sxs-lookup"><span data-stu-id="baeb6-946">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="baeb6-947">Kestrel supporta la condivisione di un indirizzo IP e di una porta tra più istanze solo con un proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="baeb6-947">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a><span data-ttu-id="baeb6-948">Registrazione connessione</span><span class="sxs-lookup"><span data-stu-id="baeb6-948">Connection logging</span></span>

<span data-ttu-id="baeb6-949">Chiamare <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> per creare log a livello di debug per la comunicazione a livello di byte in una connessione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-949">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="baeb6-950">La registrazione delle connessioni è utile per la risoluzione dei problemi nella comunicazione di basso livello, ad esempio durante la crittografia TLS e dietro i proxy.</span><span class="sxs-lookup"><span data-stu-id="baeb6-950">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="baeb6-951">Se `UseConnectionLogging` viene inserito prima `UseHttps` , viene registrato il traffico crittografato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-951">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="baeb6-952">Se `UseConnectionLogging` viene inserito dopo `UseHttps` , viene registrato il traffico decrittografato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-952">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="baeb6-953">Associazione a un socket TCP</span><span class="sxs-lookup"><span data-stu-id="baeb6-953">Bind to a TCP socket</span></span>

<span data-ttu-id="baeb6-954">Il metodo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> esegue l'associazione a un socket TCP e un'espressione lambda per le opzioni consente di configurare un certificato X.509:</span><span class="sxs-lookup"><span data-stu-id="baeb6-954">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="baeb6-955">L'esempio configura HTTPS per un endpoint con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="baeb6-955">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="baeb6-956">Usare la stessa API per configurare altre impostazioni di Kestrel per endpoint specifici.</span><span class="sxs-lookup"><span data-stu-id="baeb6-956">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="baeb6-957">Associazione a un socket Unix</span><span class="sxs-lookup"><span data-stu-id="baeb6-957">Bind to a Unix socket</span></span>

<span data-ttu-id="baeb6-958">Impostare l'ascolto su un socket Unix con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> per migliorare le prestazioni con Nginx, come visualizzato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="baeb6-958">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

* <span data-ttu-id="baeb6-959">Nel file nginx confiuguration impostare la `server`  >  `location`  >  `proxy_pass` voce su `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="baeb6-959">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="baeb6-960">`{KESTREL SOCKET}` nome del socket fornito a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (ad esempio, `kestrel-test.sock` nell'esempio precedente).</span><span class="sxs-lookup"><span data-stu-id="baeb6-960">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="baeb6-961">Verificare che il socket sia scrivibile da nginx (ad esempio, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="baeb6-961">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="baeb6-962">Porta 0</span><span class="sxs-lookup"><span data-stu-id="baeb6-962">Port 0</span></span>

<span data-ttu-id="baeb6-963">Quando si specifica il numero di porta `0`, Kestrel esegue l'associazione dinamica a una porta disponibile.</span><span class="sxs-lookup"><span data-stu-id="baeb6-963">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="baeb6-964">L'esempio seguente indica come determinare la porta alla quale Kestrel ha eseguito l'associazione in fase di esecuzione:</span><span class="sxs-lookup"><span data-stu-id="baeb6-964">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="baeb6-965">Quando si esegue l'app, l'output della finestra della console indica la porta dinamica in cui l'app può essere raggiunta:</span><span class="sxs-lookup"><span data-stu-id="baeb6-965">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="baeb6-966">Limitazioni</span><span class="sxs-lookup"><span data-stu-id="baeb6-966">Limitations</span></span>

<span data-ttu-id="baeb6-967">Configurare gli endpoint con i metodi seguenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-967">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="baeb6-968">L'argomento della riga di comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="baeb6-968">`--urls` command-line argument</span></span>
* <span data-ttu-id="baeb6-969">La chiave di configurazione dell'host `urls`</span><span class="sxs-lookup"><span data-stu-id="baeb6-969">`urls` host configuration key</span></span>
* <span data-ttu-id="baeb6-970">La variabile di ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="baeb6-970">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="baeb6-971">Questi metodi sono utili se si vuole che il codice funzioni con server diversi da Kestrel.</span><span class="sxs-lookup"><span data-stu-id="baeb6-971">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="baeb6-972">Tenere presenti, tuttavia, le limitazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-972">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="baeb6-973">HTTPS non può essere usato con questi approcci, a meno che non venga specificato un certificato predefinito nella configurazione dell'endpoint HTTPS (ad esempio, usando la configurazione `KestrelServerOptions` o un file di configurazione come illustrato in precedenza in questo argomento).</span><span class="sxs-lookup"><span data-stu-id="baeb6-973">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="baeb6-974">Quando sia l'approccio `Listen` che l'approccio `UseUrls` vengono usati contemporaneamente, gli endpoint `Listen` eseguono l'override degli endpoint `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-974">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="baeb6-975">Configurazione dell'endpoint IIS</span><span class="sxs-lookup"><span data-stu-id="baeb6-975">IIS endpoint configuration</span></span>

<span data-ttu-id="baeb6-976">Quando si usa IIS, le associazioni di URL per le associazioni di override di IIS vengono impostate mediante `Listen` o `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-976">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="baeb6-977">Per altre informazioni, vedere l'articolo [Introduzione al modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="baeb6-977">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="baeb6-978">Configurazione del trasporto</span><span class="sxs-lookup"><span data-stu-id="baeb6-978">Transport configuration</span></span>

<span data-ttu-id="baeb6-979">Con la versione ASP.NET Core 2.1, il trasporto predefinito di Kestrel non si basa più su Libuv, ma su socket gestiti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-979">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="baeb6-980">Si tratta di una modifica importante per le app ASP.NET 2.0 Core che vengono aggiornate alla versione 2.1, che chiamano <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> e dipendono da uno dei pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-980">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="baeb6-981">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (riferimento diretto al pacchetto)</span><span class="sxs-lookup"><span data-stu-id="baeb6-981">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="baeb6-982">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="baeb6-982">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="baeb6-983">Per i progetti che richiedono l'uso di libuv:</span><span class="sxs-lookup"><span data-stu-id="baeb6-983">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="baeb6-984">Aggiungere una dipendenza per il pacchetto [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="baeb6-984">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="baeb6-985">Chiamare <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="baeb6-985">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="baeb6-986">Prefissi URL</span><span class="sxs-lookup"><span data-stu-id="baeb6-986">URL prefixes</span></span>

<span data-ttu-id="baeb6-987">Se si usa `UseUrls`, l'argomento della riga di comando `--urls`, la chiave di configurazione dell'host `urls` o la variabile di ambiente `ASPNETCORE_URLS`, i prefissi URL possono avere uno dei formati seguenti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-987">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="baeb6-988">Sono validi solo i prefissi URL HTTP.</span><span class="sxs-lookup"><span data-stu-id="baeb6-988">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="baeb6-989">Kestrel non supporta HTTPS quando la configurazione delle associazioni URL viene eseguita con `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="baeb6-989">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="baeb6-990">Indirizzo IPv4 con numero di porta</span><span class="sxs-lookup"><span data-stu-id="baeb6-990">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="baeb6-991">`0.0.0.0` è un caso speciale che esegue l'associazione a tutti gli indirizzi IPv4.</span><span class="sxs-lookup"><span data-stu-id="baeb6-991">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="baeb6-992">Indirizzo IPv6 con numero di porta</span><span class="sxs-lookup"><span data-stu-id="baeb6-992">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="baeb6-993">`[::]` è l'equivalente IPv6 di `0.0.0.0` per IPv4.</span><span class="sxs-lookup"><span data-stu-id="baeb6-993">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="baeb6-994">Nome host con numero di porta</span><span class="sxs-lookup"><span data-stu-id="baeb6-994">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="baeb6-995">I nomi host, `*` e `+` non sono casi particolari.</span><span class="sxs-lookup"><span data-stu-id="baeb6-995">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="baeb6-996">Tutto ciò che non è riconosciuto come un indirizzo IP o un elemento `localhost` valido esegue l'associazione a tutti gli IP IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="baeb6-996">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="baeb6-997">Per associare nomi host diversi ad app ASP.NET Core diverse sulla stessa porta, usare [HTTP.sys](xref:fundamentals/servers/httpsys) o un server proxy inverso come IIS, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="baeb6-997">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="baeb6-998">Una configurazione che prevede un proxy inverso richiede il [filtro host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="baeb6-998">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="baeb6-999">Nome host `localhost` con numero di porta o IP di loopback con numero di porta</span><span class="sxs-lookup"><span data-stu-id="baeb6-999">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="baeb6-1000">Se è specificato `localhost`, Kestrel tenta l'associazione alle interfacce di loopback sia IPv4 che IPv6.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1000">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="baeb6-1001">Se la porta richiesta è in uso da parte di un altro servizio in una delle due interfacce di loopback, Kestrel non viene avviato.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1001">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="baeb6-1002">Se una delle due interfacce di loopback non è disponibile per qualsiasi altro motivo (in genere perché IPv6 non è supportato), Kestrel registra un avviso.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1002">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="baeb6-1003">Filtro host</span><span class="sxs-lookup"><span data-stu-id="baeb6-1003">Host filtering</span></span>

<span data-ttu-id="baeb6-1004">Mentre supporta la configurazione in base ai prefissi, ad esempio `http://example.com:5000`, Kestrel ignora quasi sempre il nome host.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1004">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="baeb6-1005">L'host `localhost` è un caso speciale usato per l'associazione agli indirizzi di loopback.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1005">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="baeb6-1006">Qualsiasi host che non sia un indirizzo IP esplicito esegue l'associazione a tutti gli indirizzi IP pubblici.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1006">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="baeb6-1007">Le intestazioni `Host` non vengono convalidate.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1007">`Host` headers aren't validated.</span></span>

<span data-ttu-id="baeb6-1008">Come soluzione alternativa, usare il middleware di filtro host.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1008">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="baeb6-1009">Il middleware di filtro host viene fornito dal pacchetto [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , incluso nel [metapacchetto Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 o 2,2).</span><span class="sxs-lookup"><span data-stu-id="baeb6-1009">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="baeb6-1010">Il middleware viene aggiunto da <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, che chiama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="baeb6-1010">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="baeb6-1011">Per impostazione predefinita, il middleware di filtro host è disabilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1011">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="baeb6-1012">Per abilitare il middleware, definire una `AllowedHosts` chiave in *appsettings.jssu* / *appSettings. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1012">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="baeb6-1013">Il valore è un elenco con valori delimitati da punto e virgola di nomi host senza numeri di porta:</span><span class="sxs-lookup"><span data-stu-id="baeb6-1013">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="baeb6-1014">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="baeb6-1014">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="baeb6-1015">Il [middleware delle intestazioni inoltrate](xref:host-and-deploy/proxy-load-balancer) include anche un'opzione <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1015">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="baeb6-1016">Il middleware di intestazioni inoltrate e il middleware di filtro host hanno funzionalità simili per diversi scenari.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1016">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="baeb6-1017">L'impostazione di `AllowedHosts` con il middleware delle intestazioni inoltrate è appropriato se l'intestazione `Host` non viene mantenuta durante l'inoltro delle richieste con un server proxy inverso o il bilanciamento del carico.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1017">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="baeb6-1018">L'impostazione di `AllowedHosts` con il middleware del filtro host è appropriata se si usa Kestrel come server perimetrale rivolto al pubblico o se l'intestazione `Host` viene inoltrata direttamente.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1018">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="baeb6-1019">Per altre informazioni sul middleware delle intestazioni inoltrate, vedere <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1019">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="baeb6-1020">Svuotamento richieste HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="baeb6-1020">HTTP/1.1 request draining</span></span>

<span data-ttu-id="baeb6-1021">L'apertura di connessioni HTTP richiede molto tempo.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1021">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="baeb6-1022">Per HTTPS, è anche a elevato utilizzo di risorse.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1022">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="baeb6-1023">Pertanto, gheppio tenta di riutilizzare le connessioni per il protocollo HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1023">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="baeb6-1024">Il corpo della richiesta deve essere completamente utilizzato per consentire il riutilizzo della connessione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1024">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="baeb6-1025">L'app non usa sempre il corpo della richiesta, ad esempio le `POST` richieste in cui il server restituisce una risposta di reindirizzamento o 404.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1025">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="baeb6-1026">Nel `POST` caso-Reindirizzamento:</span><span class="sxs-lookup"><span data-stu-id="baeb6-1026">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="baeb6-1027">Il client potrebbe avere già inviato parte dei `POST` dati.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1027">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="baeb6-1028">Il server scrive la risposta 301.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1028">The server writes the 301 response.</span></span>
* <span data-ttu-id="baeb6-1029">Non è possibile usare la connessione per una nuova richiesta fino a quando i `POST` dati del corpo della richiesta precedente non sono stati completamente letti.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1029">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="baeb6-1030">Gheppio tenta di svuotare il corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1030">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="baeb6-1031">Lo svuotamento del corpo della richiesta implica la lettura e l'eliminazione dei dati senza elaborarli.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1031">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="baeb6-1032">Il processo di svuotamento crea un tradoff tra la possibilità di riutilizzare la connessione e il tempo necessario per svuotare i dati rimanenti:</span><span class="sxs-lookup"><span data-stu-id="baeb6-1032">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="baeb6-1033">Lo svuotamento ha un timeout di cinque secondi, che non è configurabile.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1033">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="baeb6-1034">Se tutti i dati specificati dall' `Content-Length` `Transfer-Encoding` intestazione o non sono stati letti prima del timeout, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1034">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="baeb6-1035">In alcuni casi può essere necessario terminare immediatamente la richiesta, prima o dopo la scrittura della risposta.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1035">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="baeb6-1036">È ad esempio possibile che i client dispongano di protezioni dati restrittive, pertanto la limitazione dei dati caricati potrebbe avere una priorità.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1036">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="baeb6-1037">In questi casi, per terminare una richiesta, chiamare [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) da un controller, una Razor pagina o un middleware.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1037">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="baeb6-1038">Ci sono alcune avvertenze per chiamare `Abort` :</span><span class="sxs-lookup"><span data-stu-id="baeb6-1038">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="baeb6-1039">La creazione di nuove connessioni può essere lenta e dispendiosa.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1039">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="baeb6-1040">Non vi è alcuna garanzia che il client abbia letto la risposta prima che la connessione venga chiusa.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1040">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="baeb6-1041">`Abort`La chiamata a deve essere rara e riservata per i casi di errore gravi, non per errori comuni.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1041">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="baeb6-1042">Chiamare solo `Abort` quando è necessario risolvere un problema specifico.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1042">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="baeb6-1043">Ad esempio, chiamare `Abort` se i client malintenzionati tentano di eseguire `POST` dati o quando si verifica un bug nel codice client che genera richieste di grandi dimensioni o numerose.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1043">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="baeb6-1044">Non chiamare le `Abort` situazioni di errore comuni, ad esempio HTTP 404 (non trovato).</span><span class="sxs-lookup"><span data-stu-id="baeb6-1044">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="baeb6-1045">Quando si chiama [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) , prima `Abort` di chiamare si garantisce che il server abbia completato la scrittura della risposta.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1045">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="baeb6-1046">Tuttavia, il comportamento del client non è prevedibile e potrebbe non essere in grado di leggere la risposta prima che la connessione venga interrotta.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1046">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="baeb6-1047">Questo processo è diverso per HTTP/2 poiché il protocollo supporta l'interruzione dei singoli flussi di richiesta senza chiudere la connessione.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1047">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="baeb6-1048">Il timeout di svuotamento di cinque secondi non è applicabile.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1048">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="baeb6-1049">Se dopo il completamento di una risposta sono presenti dati del corpo della richiesta non letti, il server invia un frame RST HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1049">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="baeb6-1050">I frame di dati del corpo della richiesta aggiuntivi vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1050">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="baeb6-1051">Se possibile, è preferibile che i client utilizzino l'intestazione della richiesta [expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) e attendano la risposta del server prima di iniziare a inviare il corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1051">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="baeb6-1052">Che fornisce al client la possibilità di esaminare la risposta e interrompere prima di inviare dati non necessari.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1052">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="baeb6-1053">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="baeb6-1053">Additional resources</span></span>

* <span data-ttu-id="baeb6-1054">Quando si usano i socket UNIX in Linux, il socket non viene eliminato automaticamente all'arresto dell'app.</span><span class="sxs-lookup"><span data-stu-id="baeb6-1054">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="baeb6-1055">Per altre informazioni, vedere [questo problema di GitHub](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="baeb6-1055">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* <span data-ttu-id="baeb6-1056">[RFC 7230: Message Syntax and Routing (Section 5.4: Host)](https://tools.ietf.org/html/rfc7230#section-5.4) (RFC 7230: Sintassi e routing dei messaggi (sezione 5.4: Host))</span><span class="sxs-lookup"><span data-stu-id="baeb6-1056">[RFC 7230: Message Syntax and Routing (Section 5.4: Host)](https://tools.ietf.org/html/rfc7230#section-5.4)</span></span>
