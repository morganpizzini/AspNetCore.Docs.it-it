---
title: Implementazione del server Web Kestrel in ASP.NET Core
author: rick-anderson
description: Informazioni su Kestrel, il server Web multipiattaforma per ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/servers/kestrel
ms.openlocfilehash: cd05aabb7b8ce5c7d30af881228ef2dab34f2592
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776448"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="07c97-103">Implementazione del server Web Kestrel in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="07c97-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="07c97-104">Di [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) e [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="07c97-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="07c97-105">Kestrel è un [server Web per ASP.NET Core](xref:fundamentals/servers/index) multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="07c97-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="07c97-106">Kestrel è il server Web incluso per impostazione predefinita nei modelli di progetto di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="07c97-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="07c97-107">Kestrel supporta gli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="07c97-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="07c97-108">HTTPS</span></span>
* <span data-ttu-id="07c97-109">Aggiornamento opaco usato per abilitare [WebSocket](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="07c97-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="07c97-110">Socket Unix ad alte prestazioni dietro Nginx</span><span class="sxs-lookup"><span data-stu-id="07c97-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="07c97-111">HTTP/2 (tranne che in macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="07c97-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="07c97-112">&dagger;HTTP/2 verrà supportato in macOS in una versione futura.</span><span class="sxs-lookup"><span data-stu-id="07c97-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="07c97-113">Kestrel è supportato in tutte le piattaforme e le versioni supportate da .NET Core.</span><span class="sxs-lookup"><span data-stu-id="07c97-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="07c97-114">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="07c97-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="07c97-115">Supporto HTTP/2</span><span class="sxs-lookup"><span data-stu-id="07c97-115">HTTP/2 support</span></span>

<span data-ttu-id="07c97-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) è disponibile per le app ASP.NET Core se vengono soddisfatti i requisiti di base seguenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="07c97-117">Sistema operativo&dagger;</span><span class="sxs-lookup"><span data-stu-id="07c97-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="07c97-118">Windows Server 2016/Windows 10 o versioni successive&Dagger;</span><span class="sxs-lookup"><span data-stu-id="07c97-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="07c97-119">Linux con OpenSSL 1.0.2 o versioni successive (ad esempio, Ubuntu 16.04 o versioni successive)</span><span class="sxs-lookup"><span data-stu-id="07c97-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="07c97-120">Framework di destinazione: .NET Core 2.2 o versioni successive</span><span class="sxs-lookup"><span data-stu-id="07c97-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="07c97-121">Connessione [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="07c97-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="07c97-122">Connessione TLS 1.2 o successiva</span><span class="sxs-lookup"><span data-stu-id="07c97-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="07c97-123">&dagger;HTTP/2 verrà supportato in macOS in una versione futura.</span><span class="sxs-lookup"><span data-stu-id="07c97-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="07c97-124">&Dagger;Kestrel ha un supporto limitato per HTTP/2 in Windows Server 2012 R2 e Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="07c97-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="07c97-125">Il supporto è limitato perché l'elenco di suite di crittografia TLS supportate disponibili in questi sistemi operativi è limitato.</span><span class="sxs-lookup"><span data-stu-id="07c97-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="07c97-126">Un certificato generato con un algoritmo ECDSA potrebbe essere necessario per proteggere le connessioni TLS.</span><span class="sxs-lookup"><span data-stu-id="07c97-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="07c97-127">Se viene stabilita una connessione HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) corrisponde a `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="07c97-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="07c97-128">HTTP/2 è disabilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="07c97-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="07c97-129">Per altre informazioni sulla configurazione, vedere le sezioni [Opzioni Kestrel](#kestrel-options) e [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="07c97-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="07c97-130">Quando usare Kestrel con un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="07c97-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="07c97-131">È possibile usare Kestrel da solo o in combinazione con un *server proxy inverso*, ad esempio [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="07c97-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="07c97-132">Il server proxy inverso riceve le richieste HTTP dalla rete e le inoltra a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="07c97-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="07c97-133">Kestrel usato come server Web perimetrale (esposto a Internet):</span><span class="sxs-lookup"><span data-stu-id="07c97-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel comunica direttamente con Internet senza un server proxy inverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="07c97-135">Kestrel usato in una configurazione proxy inverso:</span><span class="sxs-lookup"><span data-stu-id="07c97-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel comunica indirettamente con Internet attraverso un server proxy inverso, ad esempio IIS, Nginx o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="07c97-137">La configurazione, con o senza un server proxy inverso, è una configurazione di hosting supportata.</span><span class="sxs-lookup"><span data-stu-id="07c97-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="07c97-138">Se viene utilizzato come server perimetrale in assenza di un server proxy inverso, Kestrel non supporta la condivisione tra più processi dell'IP e della porta.</span><span class="sxs-lookup"><span data-stu-id="07c97-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="07c97-139">Quando è configurato per restare in ascolto su una porta, Kestrel gestisce tutto il traffico per tale porta indipendentemente dalle intestazioni `Host` delle richieste.</span><span class="sxs-lookup"><span data-stu-id="07c97-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="07c97-140">Un proxy inverso che può condividere le porte può eseguire l'inoltro delle richieste a Kestrel su un unico IP e un'unica porta.</span><span class="sxs-lookup"><span data-stu-id="07c97-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="07c97-141">Anche se la presenza di un server proxy inverso non è necessaria, può risultare utile per i motivi seguenti.</span><span class="sxs-lookup"><span data-stu-id="07c97-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="07c97-142">Un proxy inverso:</span><span class="sxs-lookup"><span data-stu-id="07c97-142">A reverse proxy:</span></span>

* <span data-ttu-id="07c97-143">Può limitare l'area della superficie di attacco pubblica esposta delle app ospitate.</span><span class="sxs-lookup"><span data-stu-id="07c97-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="07c97-144">Offre un livello aggiuntivo di configurazione e protezione.</span><span class="sxs-lookup"><span data-stu-id="07c97-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="07c97-145">Potrebbe offrire un'integrazione migliore con l'infrastruttura esistente.</span><span class="sxs-lookup"><span data-stu-id="07c97-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="07c97-146">Semplifica il bilanciamento del carico e la configurazione di comunicazioni protette (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="07c97-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="07c97-147">Solo il server proxy inverso richiede un certificato X. 509 e il server è in grado di comunicare con i server dell'app nella rete interna tramite HTTP normale.</span><span class="sxs-lookup"><span data-stu-id="07c97-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="07c97-148">Una configurazione che prevede un proxy inverso richiede il [filtro host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="07c97-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="07c97-149">Gheppio nelle app ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="07c97-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="07c97-150">I modelli di progetto ASP.NET Core usano Kestrel per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="07c97-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="07c97-151">In *Program.cs*il <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> metodo chiama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="07c97-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="07c97-152">Per ulteriori informazioni sulla compilazione dell'host, vedere le sezioni configurare le impostazioni di *un host* e di un <xref:fundamentals/host/generic-host#set-up-a-host> *generatore predefinito* di.</span><span class="sxs-lookup"><span data-stu-id="07c97-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="07c97-153">Per fornire una configurazione aggiuntiva dopo la chiamata di `ConfigureWebHostDefaults`, usare `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="07c97-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="07c97-154">Opzioni Kestrel</span><span class="sxs-lookup"><span data-stu-id="07c97-154">Kestrel options</span></span>

<span data-ttu-id="07c97-155">Il server Web Kestrel dispone di opzioni di configurazione dei vincoli che risultano particolarmente utili nelle distribuzioni con connessione Internet.</span><span class="sxs-lookup"><span data-stu-id="07c97-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="07c97-156">Impostare i vincoli per la proprietà <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> della classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="07c97-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="07c97-157">La proprietà `Limits` contiene un'istanza della classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="07c97-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="07c97-158">Negli esempi seguenti viene usato lo spazio dei nomi <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="07c97-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="07c97-159">Negli esempi illustrati più avanti in questo articolo, le opzioni di gheppio sono configurate nel codice C#.</span><span class="sxs-lookup"><span data-stu-id="07c97-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="07c97-160">È inoltre possibile impostare le opzioni di Gheppio utilizzando un [provider di configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="07c97-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="07c97-161">Il [provider di configurazione file](xref:fundamentals/configuration/index#file-configuration-provider) , ad esempio, può caricare la configurazione di Gheppio da *appSettings. JSON* o *appSettings. { File Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="07c97-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="07c97-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>e la [configurazione dell'endpoint](#endpoint-configuration) sono configurabili dai provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="07c97-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="07c97-163">La configurazione del gheppio rimanente deve essere configurata nel codice C#.</span><span class="sxs-lookup"><span data-stu-id="07c97-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="07c97-164">Usare **uno** degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="07c97-165">Configurare gheppio in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="07c97-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="07c97-166">Inserire un'istanza di `IConfiguration` nella `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="07c97-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="07c97-167">Nell'esempio seguente si presuppone che la configurazione inserita venga assegnata alla `Configuration` proprietà.</span><span class="sxs-lookup"><span data-stu-id="07c97-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="07c97-168">In `Startup.ConfigureServices`caricare la `Kestrel` sezione della configurazione nella configurazione di Gheppio:</span><span class="sxs-lookup"><span data-stu-id="07c97-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="07c97-169">Configurare il gheppio durante la compilazione dell'host:</span><span class="sxs-lookup"><span data-stu-id="07c97-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="07c97-170">In *Program.cs*caricare la `Kestrel` sezione della configurazione nella configurazione di Gheppio:</span><span class="sxs-lookup"><span data-stu-id="07c97-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="07c97-171">Entrambi gli approcci precedenti funzionano con qualsiasi [provider di configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="07c97-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="07c97-172">Timeout keep-alive</span><span class="sxs-lookup"><span data-stu-id="07c97-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="07c97-173">Ottiene o imposta il [timeout keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="07c97-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="07c97-174">Il valore predefinito è 2 minuti.</span><span class="sxs-lookup"><span data-stu-id="07c97-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="07c97-175">Numero massimo di connessioni client</span><span class="sxs-lookup"><span data-stu-id="07c97-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="07c97-176">È possibile impostare il numero massimo di connessioni TCP aperte simultaneamente per l'intera app con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="07c97-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="07c97-177">È previsto un limite separato per le connessioni che sono state aggiornate da HTTP o HTTPS a un altro protocollo (ad esempio su una richiesta WebSocket).</span><span class="sxs-lookup"><span data-stu-id="07c97-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="07c97-178">Dopo l'aggiornamento di una connessione, questa non viene conteggiata per il limite `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="07c97-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="07c97-179">Per impostazione predefinita, il numero massimo di connessioni è illimitato (null).</span><span class="sxs-lookup"><span data-stu-id="07c97-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="07c97-180">Dimensione massima del corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="07c97-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="07c97-181">La dimensione massima predefinita del corpo della richiesta è pari a 30.000.000 di byte, equivalenti a circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="07c97-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="07c97-182">Il metodo consigliato per ignorare il limite in un'app ASP.NET Core MVC è l'uso dell'attributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> in un metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="07c97-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="07c97-183">L'esempio seguente illustra come configurare il vincolo per l'app in ogni richiesta:</span><span class="sxs-lookup"><span data-stu-id="07c97-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="07c97-184">Eseguire l'override dell'impostazione per una richiesta specifica nel middleware:</span><span class="sxs-lookup"><span data-stu-id="07c97-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="07c97-185">Viene generata un'eccezione se l'app configura il limite per una richiesta dopo che l'app ha iniziato a leggere la richiesta.</span><span class="sxs-lookup"><span data-stu-id="07c97-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="07c97-186">Una proprietà `IsReadOnly` indica se la proprietà `MaxRequestBodySize` è in stato di sola lettura e pertanto è troppo tardi per configurare il limite.</span><span class="sxs-lookup"><span data-stu-id="07c97-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="07c97-187">Quando un'app viene eseguita [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) dietro il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), il limite della dimensione del corpo della richiesta di Kestrel è disabilitato perché viene già impostato da IIS.</span><span class="sxs-lookup"><span data-stu-id="07c97-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="07c97-188">Velocità minima dei dati del corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="07c97-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="07c97-189">Kestrel controlla ogni secondo se i dati arrivano alla velocità in byte al secondo specificata.</span><span class="sxs-lookup"><span data-stu-id="07c97-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="07c97-190">Se la frequenza scende sotto il valore minimo, si è verificato il timeout della connessione. Il periodo di tolleranza è la quantità di tempo che il gheppio concede al client di aumentare la velocità di invio fino al minimo. la frequenza non viene controllata durante tale periodo di tempo.</span><span class="sxs-lookup"><span data-stu-id="07c97-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="07c97-191">Il periodo di prova consente di evitare l'interruzione di connessioni che inizialmente inviano i dati a velocità ridotta a causa dell'avvio lento del protocollo TCP.</span><span class="sxs-lookup"><span data-stu-id="07c97-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="07c97-192">La velocità minima predefinita è di 240 byte al secondo, con un periodo di tolleranza di 5 secondi.</span><span class="sxs-lookup"><span data-stu-id="07c97-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="07c97-193">Anche per la risposta è prevista una velocità minima.</span><span class="sxs-lookup"><span data-stu-id="07c97-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="07c97-194">Il codice per impostare il limite della richiesta e della risposta è identico e varia solo per `RequestBody` o `Response` nei nomi di proprietà e di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="07c97-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="07c97-195">L'esempio seguente visualizza come configurare la velocità minima dei dati in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="07c97-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="07c97-196">Ignorare i limiti di velocità minima per ogni richiesta nel middleware:</span><span class="sxs-lookup"><span data-stu-id="07c97-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="07c97-197">La funzionalità <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> a cui si fa riferimento nell'esempio precedente non è presente in `HttpContext.Features` per le richieste HTTP/2, perché la modifica dei limiti di velocità per ogni richiesta in genere non è supportata per HTTP/2 a causa del supporto del protocollo per il multiplexing delle richieste.</span><span class="sxs-lookup"><span data-stu-id="07c97-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="07c97-198">La funzionalità <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> è tuttavia ancora presente in `HttpContext.Features` per le richieste HTTP/2, perché il limite di velocità di lettura può comunque essere *disabilitato interamente* per ogni singola richiesta impostando `IHttpMinRequestBodyDataRateFeature.MinDataRate` su `null` anche per una richiesta HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="07c97-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="07c97-199">Il tentativo di leggere `IHttpMinRequestBodyDataRateFeature.MinDataRate` o il tentativo di impostazione di un valore diverso da `null` causerà la generazione di una `NotSupportedException` per una richiesta HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="07c97-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="07c97-200">I limiti di velocità a livello di server configurati tramite `KestrelServerOptions.Limits` vengono tuttavia applicati alle connessioni HTTP/1.x e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="07c97-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="07c97-201">Timeout delle intestazioni delle richieste</span><span class="sxs-lookup"><span data-stu-id="07c97-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="07c97-202">Ottiene o imposta la quantità massima di tempo che il server dedica alla ricezione delle intestazioni delle richieste.</span><span class="sxs-lookup"><span data-stu-id="07c97-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="07c97-203">Il valore predefinito è 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="07c97-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="07c97-204">Numero massimo di flussi per connessione</span><span class="sxs-lookup"><span data-stu-id="07c97-204">Maximum streams per connection</span></span>

<span data-ttu-id="07c97-205">`Http2.MaxStreamsPerConnection` limita il numero di flussi di richieste simultanee per ogni connessione HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="07c97-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="07c97-206">I flussi in eccesso vengono rifiutati.</span><span class="sxs-lookup"><span data-stu-id="07c97-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="07c97-207">Il valore predefinito è 100.</span><span class="sxs-lookup"><span data-stu-id="07c97-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="07c97-208">Dimensioni della tabella delle intestazioni</span><span class="sxs-lookup"><span data-stu-id="07c97-208">Header table size</span></span>

<span data-ttu-id="07c97-209">Il decodificatore HPACK decomprime le intestazioni HTTP per le connessioni HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="07c97-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="07c97-210">`Http2.HeaderTableSize` limita le dimensioni della tabella di compressione delle intestazioni usata dal decodificatore HPACK.</span><span class="sxs-lookup"><span data-stu-id="07c97-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="07c97-211">Il valore viene specificato in ottetti e deve essere maggiore di zero (0).</span><span class="sxs-lookup"><span data-stu-id="07c97-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="07c97-212">Il valore predefinito è 4096.</span><span class="sxs-lookup"><span data-stu-id="07c97-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="07c97-213">Dimensione massima del frame</span><span class="sxs-lookup"><span data-stu-id="07c97-213">Maximum frame size</span></span>

<span data-ttu-id="07c97-214">`Http2.MaxFrameSize`indica la dimensione massima consentita di un payload del frame di connessione HTTP/2 ricevuto o inviato dal server.</span><span class="sxs-lookup"><span data-stu-id="07c97-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="07c97-215">Il valore viene specificato in ottetti e deve essere compreso tra 2^14 (16.384) e 2^24-1 (16.777.215).</span><span class="sxs-lookup"><span data-stu-id="07c97-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="07c97-216">Il valore predefinito è 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="07c97-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="07c97-217">Dimensioni massime dell'intestazione della richiesta</span><span class="sxs-lookup"><span data-stu-id="07c97-217">Maximum request header size</span></span>

<span data-ttu-id="07c97-218">`Http2.MaxRequestHeaderFieldSize` indica le dimensioni massime consentite in ottetti di valori di intestazione di richiesta.</span><span class="sxs-lookup"><span data-stu-id="07c97-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="07c97-219">Questo limite si applica sia al nome che al valore nelle rappresentazioni compresse e non compresse.</span><span class="sxs-lookup"><span data-stu-id="07c97-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="07c97-220">Il valore deve essere maggiore di zero (0).</span><span class="sxs-lookup"><span data-stu-id="07c97-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="07c97-221">Il valore predefinito è 8.192.</span><span class="sxs-lookup"><span data-stu-id="07c97-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="07c97-222">Dimensioni della finestra di connessione iniziali</span><span class="sxs-lookup"><span data-stu-id="07c97-222">Initial connection window size</span></span>

<span data-ttu-id="07c97-223">`Http2.InitialConnectionWindowSize` indica il numero massimo di byte di dati del corpo della richiesta che il server memorizza nel buffer in una volta, aggregati in tutte le richieste (flussi), per ogni connessione.</span><span class="sxs-lookup"><span data-stu-id="07c97-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="07c97-224">Le richieste sono anche limitate da `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="07c97-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="07c97-225">Il valore deve essere maggiore di o uguale a 65.535 e minore di 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="07c97-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="07c97-226">Il valore predefinito è 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="07c97-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="07c97-227">Dimensioni della finestra di flusso iniziali</span><span class="sxs-lookup"><span data-stu-id="07c97-227">Initial stream window size</span></span>

<span data-ttu-id="07c97-228">`Http2.InitialStreamWindowSize` indica il numero massimo di byte di dati del corpo della richiesta che il server memorizza nel buffer in una volta per ogni richiesta (flusso).</span><span class="sxs-lookup"><span data-stu-id="07c97-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="07c97-229">Le richieste sono anche limitate da `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="07c97-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="07c97-230">Il valore deve essere maggiore di o uguale a 65.535 e minore di 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="07c97-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="07c97-231">Il valore predefinito è 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="07c97-231">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="07c97-232">I/O sincrono</span><span class="sxs-lookup"><span data-stu-id="07c97-232">Synchronous I/O</span></span>

<span data-ttu-id="07c97-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Controlla se è consentito l'I/O sincrono per la richiesta e la risposta.</span><span class="sxs-lookup"><span data-stu-id="07c97-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="07c97-234">Il valore predefinito è `false`.</span><span class="sxs-lookup"><span data-stu-id="07c97-234">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="07c97-235">Un numero elevato di operazioni di I/O sincrone bloccanti può causare l'esaurimento del pool di thread, il che rende l'app non risponde.</span><span class="sxs-lookup"><span data-stu-id="07c97-235">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="07c97-236">Abilitare `AllowSynchronousIO` solo quando si usa una libreria che non supporta l'I/O asincrono.</span><span class="sxs-lookup"><span data-stu-id="07c97-236">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="07c97-237">L'esempio seguente abilita l'I/O sincrono:</span><span class="sxs-lookup"><span data-stu-id="07c97-237">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="07c97-238">Per informazioni su altre opzioni e limiti di Kestrel, vedere:</span><span class="sxs-lookup"><span data-stu-id="07c97-238">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="07c97-239">Configurazione dell'endpoint</span><span class="sxs-lookup"><span data-stu-id="07c97-239">Endpoint configuration</span></span>

<span data-ttu-id="07c97-240">Per impostazione predefinita, ASP.NET Core è associato a:</span><span class="sxs-lookup"><span data-stu-id="07c97-240">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="07c97-241">`https://localhost:5001` (quando è presente un certificato di sviluppo locale)</span><span class="sxs-lookup"><span data-stu-id="07c97-241">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="07c97-242">Specificare gli URL usando gli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-242">Specify URLs using the:</span></span>

* <span data-ttu-id="07c97-243">La variabile di ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="07c97-243">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="07c97-244">L'argomento della riga di comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="07c97-244">`--urls` command-line argument.</span></span>
* <span data-ttu-id="07c97-245">La chiave di configurazione dell'host `urls`.</span><span class="sxs-lookup"><span data-stu-id="07c97-245">`urls` host configuration key.</span></span>
* <span data-ttu-id="07c97-246">Il metodo di estensione `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="07c97-246">`UseUrls` extension method.</span></span>

<span data-ttu-id="07c97-247">Il valore specificato usando i metodi seguenti può essere uno o più endpoint HTTP e HTTPS (HTTPS se è disponibile un certificato predefinito).</span><span class="sxs-lookup"><span data-stu-id="07c97-247">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="07c97-248">Configurare il valore come un elenco delimitato da punto e virgola (ad esempio, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="07c97-248">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="07c97-249">Per altre informazioni su questi approcci, vedere [URL del server](xref:fundamentals/host/web-host#server-urls) e [Override della configurazione](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="07c97-249">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="07c97-250">Viene creato un certificato di sviluppo nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-250">A development certificate is created:</span></span>

* <span data-ttu-id="07c97-251">Quando viene installato [.NET Core SDK](/dotnet/core/sdk).</span><span class="sxs-lookup"><span data-stu-id="07c97-251">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="07c97-252">Quando per creare un certificato viene usato [lo strumento dev-certs](xref:aspnetcore-2.1#https).</span><span class="sxs-lookup"><span data-stu-id="07c97-252">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="07c97-253">Alcuni browser richiedono la concessione di autorizzazioni esplicite per considerare attendibile il certificato di sviluppo locale.</span><span class="sxs-lookup"><span data-stu-id="07c97-253">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="07c97-254">I modelli di progetto consentono di configurare le app per l'esecuzione su HTTPS per impostazione predefinita e includono il [reindirizzamento HTTPS e il supporto HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="07c97-254">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="07c97-255">Chiamare i metodi <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> oppure <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> su <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> per configurare le porte e i prefissi URL per Kestrel.</span><span class="sxs-lookup"><span data-stu-id="07c97-255">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="07c97-256">Anche `UseUrls`, l'argomento della riga di comando `--urls`, la chiave di configurazione dell'host `urls` e la variabile di ambiente `ASPNETCORE_URLS` funzionano, ma con le limitazioni indicate più avanti nella sezione (deve essere disponibile un certificato predefinito per la configurazione dell'endopoint HTTPS).</span><span class="sxs-lookup"><span data-stu-id="07c97-256">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="07c97-257">`KestrelServerOptions`configurazione</span><span class="sxs-lookup"><span data-stu-id="07c97-257">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="07c97-258">ConfigureEndpointDefaults (azione\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="07c97-258">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="07c97-259">Specifica un elemento di configurazione `Action` da eseguire per ogni endpoint specificato.</span><span class="sxs-lookup"><span data-stu-id="07c97-259">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="07c97-260">Se si chiama `ConfigureEndpointDefaults` più volte, gli elementi `Action` precedenti vengono sostituiti con l'ultimo elemento `Action` specificato.</span><span class="sxs-lookup"><span data-stu-id="07c97-260">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="07c97-261">Per gli endpoint creati chiamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **prima** della <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> chiamata a non verranno applicati i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="07c97-261">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="07c97-262">ConfigureHttpsDefaults (azione\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="07c97-262">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="07c97-263">Specifica un elemento di configurazione `Action` da eseguire per ogni endpoint HTTPS.</span><span class="sxs-lookup"><span data-stu-id="07c97-263">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="07c97-264">Se si chiama `ConfigureHttpsDefaults` più volte, gli elementi `Action` precedenti vengono sostituiti con l'ultimo elemento `Action` specificato.</span><span class="sxs-lookup"><span data-stu-id="07c97-264">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="07c97-265">Per gli endpoint creati chiamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **prima** della <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> chiamata a non verranno applicati i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="07c97-265">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="07c97-266">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="07c97-266">Configure(IConfiguration)</span></span>

<span data-ttu-id="07c97-267">Crea un loader di configurazione per la configurazione di Kestrel che accetta un elemento <xref:Microsoft.Extensions.Configuration.IConfiguration> come input.</span><span class="sxs-lookup"><span data-stu-id="07c97-267">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="07c97-268">L'ambito della configurazione deve essere la sezione di configurazione per Kestrel.</span><span class="sxs-lookup"><span data-stu-id="07c97-268">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="07c97-269">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="07c97-269">ListenOptions.UseHttps</span></span>

<span data-ttu-id="07c97-270">Configura Kestrel per l'uso di HTTPS.</span><span class="sxs-lookup"><span data-stu-id="07c97-270">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="07c97-271">Estensioni `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="07c97-271">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="07c97-272">`UseHttps`&ndash; Configurare gheppio per l'uso di HTTPS con il certificato predefinito.</span><span class="sxs-lookup"><span data-stu-id="07c97-272">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="07c97-273">Genera un'eccezione se non è stato configurato alcun certificato predefinito.</span><span class="sxs-lookup"><span data-stu-id="07c97-273">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="07c97-274">Parametri `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="07c97-274">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="07c97-275">`filename` è il percorso e il nome file di un file di certificato, relativo alla directory che contiene i file di contenuto dell'app.</span><span class="sxs-lookup"><span data-stu-id="07c97-275">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="07c97-276">`password` è la password richiesta per accedere ai dati del certificato X.509.</span><span class="sxs-lookup"><span data-stu-id="07c97-276">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="07c97-277">`configureOptions` è un elemento `Action` per configurare `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="07c97-277">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="07c97-278">Restituisce `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="07c97-278">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="07c97-279">`storeName` è l'archivio certificati da cui caricare il certificato.</span><span class="sxs-lookup"><span data-stu-id="07c97-279">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="07c97-280">`subject` è il nome dell'oggetto del certificato.</span><span class="sxs-lookup"><span data-stu-id="07c97-280">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="07c97-281">`allowInvalid` indica se devono essere considerati i certificati non validi, come ad esempio i certificati autofirmati.</span><span class="sxs-lookup"><span data-stu-id="07c97-281">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="07c97-282">`location` è il percorso dell'archivio da cui caricare il certificato.</span><span class="sxs-lookup"><span data-stu-id="07c97-282">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="07c97-283">`serverCertificate` è il certificato X.509.</span><span class="sxs-lookup"><span data-stu-id="07c97-283">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="07c97-284">Nell'ambiente di produzione, HTTPS deve essere configurato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="07c97-284">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="07c97-285">Come minimo, è necessario specificare un certificato predefinito.</span><span class="sxs-lookup"><span data-stu-id="07c97-285">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="07c97-286">Configurazioni supportate descritte in seguito:</span><span class="sxs-lookup"><span data-stu-id="07c97-286">Supported configurations described next:</span></span>

* <span data-ttu-id="07c97-287">Nessuna configurazione</span><span class="sxs-lookup"><span data-stu-id="07c97-287">No configuration</span></span>
* <span data-ttu-id="07c97-288">Sostituire il certificato predefinito della configurazione</span><span class="sxs-lookup"><span data-stu-id="07c97-288">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="07c97-289">Modificare le impostazioni predefinite nel codice</span><span class="sxs-lookup"><span data-stu-id="07c97-289">Change the defaults in code</span></span>

<span data-ttu-id="07c97-290">*Nessuna configurazione*</span><span class="sxs-lookup"><span data-stu-id="07c97-290">*No configuration*</span></span>

<span data-ttu-id="07c97-291">Kestrel è in ascolto su `http://localhost:5000` e `https://localhost:5001` (se è disponibile un certificato predefinito).</span><span class="sxs-lookup"><span data-stu-id="07c97-291">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="07c97-292">*Sostituire il certificato predefinito della configurazione*</span><span class="sxs-lookup"><span data-stu-id="07c97-292">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="07c97-293">`CreateDefaultBuilder` chiama `Configure(context.Configuration.GetSection("Kestrel"))` per impostazione predefinita per caricare la configurazione di Kestrel.</span><span class="sxs-lookup"><span data-stu-id="07c97-293">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="07c97-294">È disponibile per Kestrel uno schema di configurazione delle impostazioni delle app HTTPS predefinito.</span><span class="sxs-lookup"><span data-stu-id="07c97-294">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="07c97-295">Configurare più endpoint, inclusi gli URL e i certificati da usare, da un file su disco o da un archivio certificati.</span><span class="sxs-lookup"><span data-stu-id="07c97-295">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="07c97-296">Nel file *appsettings.json* di esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="07c97-296">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="07c97-297">Impostare **AllowInvalid** su `true` per consentire l'uso di certificati non validi, come ad esempio i certificati autofirmati.</span><span class="sxs-lookup"><span data-stu-id="07c97-297">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="07c97-298">Tutti gli endpoint HTTPS che non specificano un certificato (**HttpsDefaultCert** nell'esempio che segue) usano il certificato definito in **Certificates** > **Default** o il certificato di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="07c97-298">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="07c97-299">Un'alternativa all'uso di **Path** e **Password** per qualsiasi nodo del certificato consiste nello specificare il certificato usando i campi dell'archivio certificati.</span><span class="sxs-lookup"><span data-stu-id="07c97-299">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="07c97-300">Ad esempio, il certificato**predefinito** **Certificates** > può essere specificato come:</span><span class="sxs-lookup"><span data-stu-id="07c97-300">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="07c97-301">Note di schema:</span><span class="sxs-lookup"><span data-stu-id="07c97-301">Schema notes:</span></span>

* <span data-ttu-id="07c97-302">I nomi degli endpoint non applicano la distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="07c97-302">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="07c97-303">Ad esempio, sono validi sia `HTTPS` che `Https`.</span><span class="sxs-lookup"><span data-stu-id="07c97-303">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="07c97-304">Il parametro `Url` è obbligatorio per ogni endpoint.</span><span class="sxs-lookup"><span data-stu-id="07c97-304">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="07c97-305">Il formato per questo parametro è uguale a quello del parametro di configurazione di primo livello `Urls`, con la differenza che si limita a un singolo valore.</span><span class="sxs-lookup"><span data-stu-id="07c97-305">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="07c97-306">Questi endpoint sostituiscono quelli definiti nella configurazione di primo livello `Urls`, non vi si aggiungono.</span><span class="sxs-lookup"><span data-stu-id="07c97-306">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="07c97-307">Gli endpoint definiti nel codice tramite `Listen` si aggiungono agli endpoint definiti nella sezione di configurazione.</span><span class="sxs-lookup"><span data-stu-id="07c97-307">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="07c97-308">La sezione `Certificate` è facoltativa.</span><span class="sxs-lookup"><span data-stu-id="07c97-308">The `Certificate` section is optional.</span></span> <span data-ttu-id="07c97-309">Se la sezione `Certificate` non è specificata, vengono usati i valori predefiniti definiti negli scenari precedenti.</span><span class="sxs-lookup"><span data-stu-id="07c97-309">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="07c97-310">Se non è disponibile alcun valore predefinito, il server genera un'eccezione e non viene avviato.</span><span class="sxs-lookup"><span data-stu-id="07c97-310">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="07c97-311">La `Certificate` sezione supporta sia la**password** del **percorso**&ndash;che i certificati dell'**Archivio** dell' **oggetto**&ndash;.</span><span class="sxs-lookup"><span data-stu-id="07c97-311">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="07c97-312">In questo modo è possibile definire un numero qualsiasi di endpoint, purché non provochino conflitti di porte.</span><span class="sxs-lookup"><span data-stu-id="07c97-312">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="07c97-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))` restituisce un oggetto `KestrelConfigurationLoader` con un metodo `.Endpoint(string name, listenOptions => { })` che può essere usato per integrare le impostazioni dell'endpoint configurato:</span><span class="sxs-lookup"><span data-stu-id="07c97-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="07c97-314">`KestrelServerOptions.ConfigurationLoader`è possibile accedere direttamente a per continuare a scorrere sul caricatore esistente, ad esempio quello fornito da <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="07c97-314">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="07c97-315">La sezione di configurazione per ogni endpoint è disponibile nelle opzioni del `Endpoint` metodo in modo che sia possibile leggere le impostazioni personalizzate.</span><span class="sxs-lookup"><span data-stu-id="07c97-315">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="07c97-316">È possibile caricare più configurazioni chiamando ancora `options.Configure(context.Configuration.GetSection("{SECTION}"))` con un'altra sezione.</span><span class="sxs-lookup"><span data-stu-id="07c97-316">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="07c97-317">Viene usata solo l'ultima configurazione, a meno che `Load` sia stato chiamato esplicitamente in istanze precedenti.</span><span class="sxs-lookup"><span data-stu-id="07c97-317">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="07c97-318">Il metapacchetto non chiama `Load` in modo che la sezione di configurazione predefinita venga sostituita.</span><span class="sxs-lookup"><span data-stu-id="07c97-318">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="07c97-319">`KestrelConfigurationLoader` riflette la famiglia di API `Listen` da `KestrelServerOptions` all'overload di `Endpoint`, in modo che gli endpoint di codice e di configurazione possano essere configurati nella stessa posizione.</span><span class="sxs-lookup"><span data-stu-id="07c97-319">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="07c97-320">Questi overload non usano nomi e usano solo le impostazioni predefinite della configurazione.</span><span class="sxs-lookup"><span data-stu-id="07c97-320">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="07c97-321">*Modificare le impostazioni predefinite nel codice*</span><span class="sxs-lookup"><span data-stu-id="07c97-321">*Change the defaults in code*</span></span>

<span data-ttu-id="07c97-322">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` possono essere usati per modificare le impostazioni predefinite per `ListenOptions` e `HttpsConnectionAdapterOptions`, inclusa l'esecuzione dell'override del certificato predefinito specificato nello scenario precedente.</span><span class="sxs-lookup"><span data-stu-id="07c97-322">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="07c97-323">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devono essere chiamati prima che venga configurato qualsiasi endpoint.</span><span class="sxs-lookup"><span data-stu-id="07c97-323">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="07c97-324">*Supporto kestrel per SNI*</span><span class="sxs-lookup"><span data-stu-id="07c97-324">*Kestrel support for SNI*</span></span>

<span data-ttu-id="07c97-325">[L'Indicazione nome server (SNI)](https://tools.ietf.org/html/rfc6066#section-3) può essere usata per ospitare più domini sulla stessa porta e indirizzo IP.</span><span class="sxs-lookup"><span data-stu-id="07c97-325">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="07c97-326">Perché SNI funzioni è necessario che il client invii al server il nome host per la sessione protetta durante l'handshake TLS in modo che il server possa specificare il certificato corretto.</span><span class="sxs-lookup"><span data-stu-id="07c97-326">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="07c97-327">Il client usa il certificato specificato per la comunicazione crittografata con il server durante la sessione protetta che segue l'handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="07c97-327">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="07c97-328">Kestrel supporta SNI tramite il callback `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="07c97-328">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="07c97-329">Il callback viene richiamato una volta per ogni connessione per consentire all'app di controllare il nome host e selezionare il certificato appropriato.</span><span class="sxs-lookup"><span data-stu-id="07c97-329">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="07c97-330">Il supporto SNI richiede:</span><span class="sxs-lookup"><span data-stu-id="07c97-330">SNI support requires:</span></span>

* <span data-ttu-id="07c97-331">In esecuzione su Framework `netcoreapp2.1` di destinazione o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="07c97-331">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="07c97-332">In `net461` o versioni successive, il callback viene richiamato `name` , ma `null`è sempre.</span><span class="sxs-lookup"><span data-stu-id="07c97-332">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="07c97-333">L'elemento `name` è `null` anche se il client non specifica il parametro del nome host nell'handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="07c97-333">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="07c97-334">Esecuzione di tutti i siti Web nella stessa istanza di Kestrel.</span><span class="sxs-lookup"><span data-stu-id="07c97-334">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="07c97-335">Kestrel supporta la condivisione di un indirizzo IP e di una porta tra più istanze solo con un proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="07c97-335">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="07c97-336">Registrazione connessione</span><span class="sxs-lookup"><span data-stu-id="07c97-336">Connection logging</span></span>

<span data-ttu-id="07c97-337">Chiamare <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> per creare log a livello di debug per la comunicazione a livello di byte in una connessione.</span><span class="sxs-lookup"><span data-stu-id="07c97-337">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="07c97-338">La registrazione delle connessioni è utile per la risoluzione dei problemi nella comunicazione di basso livello, ad esempio durante la crittografia TLS e dietro i proxy.</span><span class="sxs-lookup"><span data-stu-id="07c97-338">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="07c97-339">Se `UseConnectionLogging` viene inserito prima `UseHttps`, viene registrato il traffico crittografato.</span><span class="sxs-lookup"><span data-stu-id="07c97-339">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="07c97-340">Se `UseConnectionLogging` viene inserito dopo `UseHttps`, viene registrato il traffico decrittografato.</span><span class="sxs-lookup"><span data-stu-id="07c97-340">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="07c97-341">Associazione a un socket TCP</span><span class="sxs-lookup"><span data-stu-id="07c97-341">Bind to a TCP socket</span></span>

<span data-ttu-id="07c97-342">Il metodo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> esegue l'associazione a un socket TCP e un'espressione lambda per le opzioni consente di configurare un certificato X.509:</span><span class="sxs-lookup"><span data-stu-id="07c97-342">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="07c97-343">L'esempio configura HTTPS per un endpoint con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="07c97-343">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="07c97-344">Usare la stessa API per configurare altre impostazioni di Kestrel per endpoint specifici.</span><span class="sxs-lookup"><span data-stu-id="07c97-344">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="07c97-345">Associazione a un socket Unix</span><span class="sxs-lookup"><span data-stu-id="07c97-345">Bind to a Unix socket</span></span>

<span data-ttu-id="07c97-346">Impostare l'ascolto su un socket Unix con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> per migliorare le prestazioni con Nginx, come visualizzato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="07c97-346">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="07c97-347">Nel `server`  >  `location`  >  `proxy_pass` file di configurazione nginx impostare la voce su `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="07c97-347">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="07c97-348">`{KESTREL SOCKET}`nome del socket fornito a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (ad esempio, `kestrel-test.sock` nell'esempio precedente).</span><span class="sxs-lookup"><span data-stu-id="07c97-348">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="07c97-349">Verificare che il socket sia scrivibile da nginx (ad esempio, `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="07c97-349">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="07c97-350">Porta 0</span><span class="sxs-lookup"><span data-stu-id="07c97-350">Port 0</span></span>

<span data-ttu-id="07c97-351">Quando si specifica il numero di porta `0`, Kestrel esegue l'associazione dinamica a una porta disponibile.</span><span class="sxs-lookup"><span data-stu-id="07c97-351">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="07c97-352">L'esempio seguente indica come determinare la porta alla quale Kestrel ha eseguito l'associazione in fase di esecuzione:</span><span class="sxs-lookup"><span data-stu-id="07c97-352">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="07c97-353">Quando si esegue l'app, l'output della finestra della console indica la porta dinamica in cui l'app può essere raggiunta:</span><span class="sxs-lookup"><span data-stu-id="07c97-353">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="07c97-354">Limitazioni</span><span class="sxs-lookup"><span data-stu-id="07c97-354">Limitations</span></span>

<span data-ttu-id="07c97-355">Configurare gli endpoint con i metodi seguenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-355">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="07c97-356">L'argomento della riga di comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="07c97-356">`--urls` command-line argument</span></span>
* <span data-ttu-id="07c97-357">La chiave di configurazione dell'host `urls`</span><span class="sxs-lookup"><span data-stu-id="07c97-357">`urls` host configuration key</span></span>
* <span data-ttu-id="07c97-358">La variabile di ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="07c97-358">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="07c97-359">Questi metodi sono utili se si vuole che il codice funzioni con server diversi da Kestrel.</span><span class="sxs-lookup"><span data-stu-id="07c97-359">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="07c97-360">Tenere presenti, tuttavia, le limitazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-360">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="07c97-361">HTTPS non può essere usato con questi approcci, a meno che non venga specificato un certificato predefinito nella configurazione dell'endpoint HTTPS (ad esempio, usando la configurazione `KestrelServerOptions` o un file di configurazione come illustrato in precedenza in questo argomento).</span><span class="sxs-lookup"><span data-stu-id="07c97-361">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="07c97-362">Quando sia l'approccio `Listen` che l'approccio `UseUrls` vengono usati contemporaneamente, gli endpoint `Listen` eseguono l'override degli endpoint `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="07c97-362">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="07c97-363">Configurazione dell'endpoint IIS</span><span class="sxs-lookup"><span data-stu-id="07c97-363">IIS endpoint configuration</span></span>

<span data-ttu-id="07c97-364">Quando si usa IIS, le associazioni di URL per le associazioni di override di IIS vengono impostate mediante `Listen` o `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="07c97-364">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="07c97-365">Per altre informazioni, vedere l'articolo [Introduzione al modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="07c97-365">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="07c97-366">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="07c97-366">ListenOptions.Protocols</span></span>

<span data-ttu-id="07c97-367">La proprietà `Protocols` stabilisce i protocolli HTTP (`HttpProtocols`) abilitati in un endpoint di connessione o per il server.</span><span class="sxs-lookup"><span data-stu-id="07c97-367">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="07c97-368">Assegnare un valore alla proprietà `Protocols` dall'enumerazione `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="07c97-368">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="07c97-369">Valore di enumerazione `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="07c97-369">`HttpProtocols` enum value</span></span> | <span data-ttu-id="07c97-370">Protocollo di connessione consentito</span><span class="sxs-lookup"><span data-stu-id="07c97-370">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="07c97-371">Solo HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="07c97-371">HTTP/1.1 only.</span></span> <span data-ttu-id="07c97-372">Può essere usato con o senza TLS.</span><span class="sxs-lookup"><span data-stu-id="07c97-372">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="07c97-373">Solo HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="07c97-373">HTTP/2 only.</span></span> <span data-ttu-id="07c97-374">Può essere usato senza TLS solo se il client supporta una [modalità di conoscenza pregressa](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="07c97-374">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="07c97-375">HTTP/1.1 e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="07c97-375">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="07c97-376">HTTP/2 richiede che il client selezioni HTTP/2 nell'handshake TLS [(Application-Layer Protocol negotiation) ALPN](https://tools.ietf.org/html/rfc7301#section-3) in caso contrario, il valore predefinito per la connessione è HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="07c97-376">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="07c97-377">Il valore `ListenOptions.Protocols` predefinito per qualsiasi endpoint è `HttpProtocols.Http1AndHttp2`.</span><span class="sxs-lookup"><span data-stu-id="07c97-377">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="07c97-378">Restrizioni relative a TLS per HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="07c97-378">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="07c97-379">TLS versione 1.2 o successiva</span><span class="sxs-lookup"><span data-stu-id="07c97-379">TLS version 1.2 or later</span></span>
* <span data-ttu-id="07c97-380">Rinegoziazione disabilitata</span><span class="sxs-lookup"><span data-stu-id="07c97-380">Renegotiation disabled</span></span>
* <span data-ttu-id="07c97-381">Compressione disabilitata</span><span class="sxs-lookup"><span data-stu-id="07c97-381">Compression disabled</span></span>
* <span data-ttu-id="07c97-382">Dimensioni minime per lo scambio di chiavi temporanee:</span><span class="sxs-lookup"><span data-stu-id="07c97-382">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="07c97-383">Diffie-Hellman a curva ellittica (ECDH) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bit (minimo)</span><span class="sxs-lookup"><span data-stu-id="07c97-383">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="07c97-384">Minimum-Hellman Diffie-Hellman (dhe &lbrack; `TLS12` &rbrack; &ndash; ) 2048 bit minimo</span><span class="sxs-lookup"><span data-stu-id="07c97-384">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="07c97-385">Pacchetto di crittografia consentito</span><span class="sxs-lookup"><span data-stu-id="07c97-385">Cipher suite not blacklisted</span></span>

<span data-ttu-id="07c97-386">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; &lbrack; con la `FIPS186` curva &rbrack; ellittica P-256 è supportata per impostazione predefinita. `TLS-ECDHE` &rbrack;</span><span class="sxs-lookup"><span data-stu-id="07c97-386">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="07c97-387">Nell'esempio seguente sono consentite connessioni HTTP/1.1 e HTTP/2 sulla porta 8000.</span><span class="sxs-lookup"><span data-stu-id="07c97-387">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="07c97-388">Le connessioni sono protette da TLS con un certificato incluso:</span><span class="sxs-lookup"><span data-stu-id="07c97-388">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="07c97-389">Usare il middleware di connessione per filtrare gli handshake TLS in base alla connessione per le crittografie specifiche, se necessario.</span><span class="sxs-lookup"><span data-stu-id="07c97-389">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="07c97-390">L'esempio seguente genera <xref:System.NotSupportedException> un'eccezione per qualsiasi algoritmo crittografico non supportato dall'app.</span><span class="sxs-lookup"><span data-stu-id="07c97-390">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="07c97-391">In alternativa, definire e confrontare [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) con un elenco di pacchetti di crittografia accettabili.</span><span class="sxs-lookup"><span data-stu-id="07c97-391">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="07c97-392">Non viene utilizzata alcuna crittografia con un algoritmo di crittografia [CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) .</span><span class="sxs-lookup"><span data-stu-id="07c97-392">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="07c97-393">Il filtro della connessione può essere configurato anche <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> tramite un'espressione lambda:</span><span class="sxs-lookup"><span data-stu-id="07c97-393">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="07c97-394">In Linux è <xref:System.Net.Security.CipherSuitesPolicy> possibile usare per filtrare gli handshake TLS in base alla connessione:</span><span class="sxs-lookup"><span data-stu-id="07c97-394">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="07c97-395">*Impostare il protocollo dalla configurazione*</span><span class="sxs-lookup"><span data-stu-id="07c97-395">*Set the protocol from configuration*</span></span>

<span data-ttu-id="07c97-396">`CreateDefaultBuilder` chiama `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` per impostazione predefinita per caricare la configurazione di Kestrel.</span><span class="sxs-lookup"><span data-stu-id="07c97-396">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="07c97-397">L'esempio *appSettings. JSON* seguente stabilisce http/1.1 come protocollo di connessione predefinito per tutti gli endpoint:</span><span class="sxs-lookup"><span data-stu-id="07c97-397">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="07c97-398">Nell'esempio *appSettings. JSON* seguente viene stabilito il protocollo di connessione HTTP/1.1 per un endpoint specifico:</span><span class="sxs-lookup"><span data-stu-id="07c97-398">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="07c97-399">I protocolli specificati nei valori di override del codice sono impostati dalla configurazione.</span><span class="sxs-lookup"><span data-stu-id="07c97-399">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="07c97-400">Configurazione del trasporto</span><span class="sxs-lookup"><span data-stu-id="07c97-400">Transport configuration</span></span>

<span data-ttu-id="07c97-401">Per i progetti che richiedono l'utilizzo di libuv<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>():</span><span class="sxs-lookup"><span data-stu-id="07c97-401">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="07c97-402">Aggiungere una dipendenza per il pacchetto [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="07c97-402">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="07c97-403">Chiamare <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> su `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="07c97-403">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="07c97-404">Prefissi URL</span><span class="sxs-lookup"><span data-stu-id="07c97-404">URL prefixes</span></span>

<span data-ttu-id="07c97-405">Se si usa `UseUrls`, l'argomento della riga di comando `--urls`, la chiave di configurazione dell'host `urls` o la variabile di ambiente `ASPNETCORE_URLS`, i prefissi URL possono avere uno dei formati seguenti.</span><span class="sxs-lookup"><span data-stu-id="07c97-405">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="07c97-406">Sono validi solo i prefissi URL HTTP.</span><span class="sxs-lookup"><span data-stu-id="07c97-406">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="07c97-407">Kestrel non supporta HTTPS quando la configurazione delle associazioni URL viene eseguita con `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="07c97-407">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="07c97-408">Indirizzo IPv4 con numero di porta</span><span class="sxs-lookup"><span data-stu-id="07c97-408">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="07c97-409">`0.0.0.0` è un caso speciale che esegue l'associazione a tutti gli indirizzi IPv4.</span><span class="sxs-lookup"><span data-stu-id="07c97-409">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="07c97-410">Indirizzo IPv6 con numero di porta</span><span class="sxs-lookup"><span data-stu-id="07c97-410">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="07c97-411">`[::]` è l'equivalente IPv6 di `0.0.0.0` per IPv4.</span><span class="sxs-lookup"><span data-stu-id="07c97-411">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="07c97-412">Nome host con numero di porta</span><span class="sxs-lookup"><span data-stu-id="07c97-412">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="07c97-413">I nomi host, `*` e `+` non sono casi particolari.</span><span class="sxs-lookup"><span data-stu-id="07c97-413">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="07c97-414">Tutto ciò che non è riconosciuto come un indirizzo IP o un elemento `localhost` valido esegue l'associazione a tutti gli IP IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="07c97-414">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="07c97-415">Per associare nomi host diversi ad app ASP.NET Core diverse sulla stessa porta, usare [HTTP.sys](xref:fundamentals/servers/httpsys) o un server proxy inverso come IIS, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="07c97-415">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="07c97-416">Una configurazione che prevede un proxy inverso richiede il [filtro host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="07c97-416">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="07c97-417">Nome host `localhost` con numero di porta o IP di loopback con numero di porta</span><span class="sxs-lookup"><span data-stu-id="07c97-417">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="07c97-418">Se è specificato `localhost`, Kestrel tenta l'associazione alle interfacce di loopback sia IPv4 che IPv6.</span><span class="sxs-lookup"><span data-stu-id="07c97-418">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="07c97-419">Se la porta richiesta è in uso da parte di un altro servizio in una delle due interfacce di loopback, Kestrel non viene avviato.</span><span class="sxs-lookup"><span data-stu-id="07c97-419">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="07c97-420">Se una delle due interfacce di loopback non è disponibile per qualsiasi altro motivo (in genere perché IPv6 non è supportato), Kestrel registra un avviso.</span><span class="sxs-lookup"><span data-stu-id="07c97-420">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="07c97-421">Filtro host</span><span class="sxs-lookup"><span data-stu-id="07c97-421">Host filtering</span></span>

<span data-ttu-id="07c97-422">Mentre supporta la configurazione in base ai prefissi, ad esempio `http://example.com:5000`, Kestrel ignora quasi sempre il nome host.</span><span class="sxs-lookup"><span data-stu-id="07c97-422">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="07c97-423">L'host `localhost` è un caso speciale usato per l'associazione agli indirizzi di loopback.</span><span class="sxs-lookup"><span data-stu-id="07c97-423">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="07c97-424">Qualsiasi host che non sia un indirizzo IP esplicito esegue l'associazione a tutti gli indirizzi IP pubblici.</span><span class="sxs-lookup"><span data-stu-id="07c97-424">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="07c97-425">Le intestazioni `Host` non vengono convalidate.</span><span class="sxs-lookup"><span data-stu-id="07c97-425">`Host` headers aren't validated.</span></span>

<span data-ttu-id="07c97-426">Come soluzione alternativa, usare il middleware di filtro host.</span><span class="sxs-lookup"><span data-stu-id="07c97-426">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="07c97-427">Il middleware di filtro host viene fornito dal pacchetto [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , fornito in modo implicito per le app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="07c97-427">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="07c97-428">Il middleware viene aggiunto da <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, che chiama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="07c97-428">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="07c97-429">Per impostazione predefinita, il middleware di filtro host è disabilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="07c97-429">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="07c97-430">Per abilitare il middleware, definire una `AllowedHosts` chiave in *appSettings. JSON*/*appSettings.\< EnvironmentName>. JSON*.</span><span class="sxs-lookup"><span data-stu-id="07c97-430">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="07c97-431">Il valore è un elenco con valori delimitati da punto e virgola di nomi host senza numeri di porta:</span><span class="sxs-lookup"><span data-stu-id="07c97-431">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="07c97-432">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="07c97-432">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="07c97-433">Il [middleware delle intestazioni inoltrate](xref:host-and-deploy/proxy-load-balancer) include anche un'opzione <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="07c97-433">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="07c97-434">Il middleware di intestazioni inoltrate e il middleware di filtro host hanno funzionalità simili per diversi scenari.</span><span class="sxs-lookup"><span data-stu-id="07c97-434">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="07c97-435">L'impostazione di `AllowedHosts` con il middleware delle intestazioni inoltrate è appropriato se l'intestazione `Host` non viene mantenuta durante l'inoltro delle richieste con un server proxy inverso o il bilanciamento del carico.</span><span class="sxs-lookup"><span data-stu-id="07c97-435">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="07c97-436">L'impostazione di `AllowedHosts` con il middleware del filtro host è appropriata se si usa Kestrel come server perimetrale rivolto al pubblico o se l'intestazione `Host` viene inoltrata direttamente.</span><span class="sxs-lookup"><span data-stu-id="07c97-436">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="07c97-437">Per altre informazioni sul middleware delle intestazioni inoltrate, vedere <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="07c97-437">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="07c97-438">Kestrel è un [server Web per ASP.NET Core](xref:fundamentals/servers/index) multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="07c97-438">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="07c97-439">Kestrel è il server Web incluso per impostazione predefinita nei modelli di progetto di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="07c97-439">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="07c97-440">Kestrel supporta gli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-440">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="07c97-441">HTTPS</span><span class="sxs-lookup"><span data-stu-id="07c97-441">HTTPS</span></span>
* <span data-ttu-id="07c97-442">Aggiornamento opaco usato per abilitare [WebSocket](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="07c97-442">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="07c97-443">Socket Unix ad alte prestazioni dietro Nginx</span><span class="sxs-lookup"><span data-stu-id="07c97-443">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="07c97-444">HTTP/2 (tranne che in macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="07c97-444">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="07c97-445">&dagger;HTTP/2 verrà supportato in macOS in una versione futura.</span><span class="sxs-lookup"><span data-stu-id="07c97-445">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="07c97-446">Kestrel è supportato in tutte le piattaforme e le versioni supportate da .NET Core.</span><span class="sxs-lookup"><span data-stu-id="07c97-446">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="07c97-447">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="07c97-447">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="07c97-448">Supporto HTTP/2</span><span class="sxs-lookup"><span data-stu-id="07c97-448">HTTP/2 support</span></span>

<span data-ttu-id="07c97-449">[HTTP/2](https://httpwg.org/specs/rfc7540.html) è disponibile per le app ASP.NET Core se vengono soddisfatti i requisiti di base seguenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-449">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="07c97-450">Sistema operativo&dagger;</span><span class="sxs-lookup"><span data-stu-id="07c97-450">Operating system&dagger;</span></span>
  * <span data-ttu-id="07c97-451">Windows Server 2016/Windows 10 o versioni successive&Dagger;</span><span class="sxs-lookup"><span data-stu-id="07c97-451">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="07c97-452">Linux con OpenSSL 1.0.2 o versioni successive (ad esempio, Ubuntu 16.04 o versioni successive)</span><span class="sxs-lookup"><span data-stu-id="07c97-452">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="07c97-453">Framework di destinazione: .NET Core 2.2 o versioni successive</span><span class="sxs-lookup"><span data-stu-id="07c97-453">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="07c97-454">Connessione [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="07c97-454">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="07c97-455">Connessione TLS 1.2 o successiva</span><span class="sxs-lookup"><span data-stu-id="07c97-455">TLS 1.2 or later connection</span></span>

<span data-ttu-id="07c97-456">&dagger;HTTP/2 verrà supportato in macOS in una versione futura.</span><span class="sxs-lookup"><span data-stu-id="07c97-456">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="07c97-457">&Dagger;Kestrel ha un supporto limitato per HTTP/2 in Windows Server 2012 R2 e Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="07c97-457">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="07c97-458">Il supporto è limitato perché l'elenco di suite di crittografia TLS supportate disponibili in questi sistemi operativi è limitato.</span><span class="sxs-lookup"><span data-stu-id="07c97-458">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="07c97-459">Un certificato generato con un algoritmo ECDSA potrebbe essere necessario per proteggere le connessioni TLS.</span><span class="sxs-lookup"><span data-stu-id="07c97-459">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="07c97-460">Se viene stabilita una connessione HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) corrisponde a `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="07c97-460">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="07c97-461">HTTP/2 è disabilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="07c97-461">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="07c97-462">Per altre informazioni sulla configurazione, vedere le sezioni [Opzioni Kestrel](#kestrel-options) e [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="07c97-462">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="07c97-463">Quando usare Kestrel con un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="07c97-463">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="07c97-464">È possibile usare Kestrel da solo o in combinazione con un *server proxy inverso*, ad esempio [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="07c97-464">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="07c97-465">Il server proxy inverso riceve le richieste HTTP dalla rete e le inoltra a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="07c97-465">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="07c97-466">Kestrel usato come server Web perimetrale (esposto a Internet):</span><span class="sxs-lookup"><span data-stu-id="07c97-466">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel comunica direttamente con Internet senza un server proxy inverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="07c97-468">Kestrel usato in una configurazione proxy inverso:</span><span class="sxs-lookup"><span data-stu-id="07c97-468">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel comunica indirettamente con Internet attraverso un server proxy inverso, ad esempio IIS, Nginx o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="07c97-470">La configurazione, con o senza un server proxy inverso, è una configurazione di hosting supportata.</span><span class="sxs-lookup"><span data-stu-id="07c97-470">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="07c97-471">Se viene utilizzato come server perimetrale in assenza di un server proxy inverso, Kestrel non supporta la condivisione tra più processi dell'IP e della porta.</span><span class="sxs-lookup"><span data-stu-id="07c97-471">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="07c97-472">Quando è configurato per restare in ascolto su una porta, Kestrel gestisce tutto il traffico per tale porta indipendentemente dalle intestazioni `Host` delle richieste.</span><span class="sxs-lookup"><span data-stu-id="07c97-472">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="07c97-473">Un proxy inverso che può condividere le porte può eseguire l'inoltro delle richieste a Kestrel su un unico IP e un'unica porta.</span><span class="sxs-lookup"><span data-stu-id="07c97-473">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="07c97-474">Anche se la presenza di un server proxy inverso non è necessaria, può risultare utile per i motivi seguenti.</span><span class="sxs-lookup"><span data-stu-id="07c97-474">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="07c97-475">Un proxy inverso:</span><span class="sxs-lookup"><span data-stu-id="07c97-475">A reverse proxy:</span></span>

* <span data-ttu-id="07c97-476">Può limitare l'area della superficie di attacco pubblica esposta delle app ospitate.</span><span class="sxs-lookup"><span data-stu-id="07c97-476">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="07c97-477">Offre un livello aggiuntivo di configurazione e protezione.</span><span class="sxs-lookup"><span data-stu-id="07c97-477">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="07c97-478">Potrebbe offrire un'integrazione migliore con l'infrastruttura esistente.</span><span class="sxs-lookup"><span data-stu-id="07c97-478">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="07c97-479">Semplifica il bilanciamento del carico e la configurazione di comunicazioni protette (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="07c97-479">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="07c97-480">Solo il server proxy inverso richiede un certificato X. 509 e il server è in grado di comunicare con i server dell'app nella rete interna tramite HTTP normale.</span><span class="sxs-lookup"><span data-stu-id="07c97-480">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="07c97-481">Una configurazione che prevede un proxy inverso richiede il [filtro host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="07c97-481">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="07c97-482">Come usare Kestrel nelle app ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="07c97-482">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="07c97-483">Il pacchetto [Microsoft. AspNetCore. Server. gheppio](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) è incluso nel [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="07c97-483">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="07c97-484">I modelli di progetto ASP.NET Core usano Kestrel per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="07c97-484">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="07c97-485">In *Program.cs* il codice del modello chiama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, che chiama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> in background.</span><span class="sxs-lookup"><span data-stu-id="07c97-485">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="07c97-486">Per ulteriori informazioni su `CreateDefaultBuilder` e sulla compilazione dell'host, vedere la sezione *configurare un host* di <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="07c97-486">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="07c97-487">Per fornire una configurazione aggiuntiva dopo la chiamata di `CreateDefaultBuilder`, usare `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="07c97-487">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="07c97-488">Se l'app non chiama `CreateDefaultBuilder` per configurare l'host, chiamare <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **prima** di chiamare `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="07c97-488">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="07c97-489">Opzioni Kestrel</span><span class="sxs-lookup"><span data-stu-id="07c97-489">Kestrel options</span></span>

<span data-ttu-id="07c97-490">Il server Web Kestrel dispone di opzioni di configurazione dei vincoli che risultano particolarmente utili nelle distribuzioni con connessione Internet.</span><span class="sxs-lookup"><span data-stu-id="07c97-490">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="07c97-491">Impostare i vincoli per la proprietà <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> della classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="07c97-491">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="07c97-492">La proprietà `Limits` contiene un'istanza della classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="07c97-492">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="07c97-493">Negli esempi seguenti viene usato lo spazio dei nomi <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="07c97-493">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="07c97-494">Le opzioni gheppio, che sono configurate nel codice C# negli esempi seguenti, possono essere impostate anche usando un [provider di configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="07c97-494">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="07c97-495">Il provider di configurazione file, ad esempio, può caricare la configurazione di Gheppio da *appSettings. JSON* o *appSettings. { File Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="07c97-495">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="07c97-496">Usare **uno** degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-496">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="07c97-497">Configurare gheppio in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="07c97-497">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="07c97-498">Inserire un'istanza di `IConfiguration` nella `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="07c97-498">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="07c97-499">Nell'esempio seguente si presuppone che la configurazione inserita venga assegnata alla `Configuration` proprietà.</span><span class="sxs-lookup"><span data-stu-id="07c97-499">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="07c97-500">In `Startup.ConfigureServices`caricare la `Kestrel` sezione della configurazione nella configurazione di Gheppio:</span><span class="sxs-lookup"><span data-stu-id="07c97-500">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="07c97-501">Configurare il gheppio durante la compilazione dell'host:</span><span class="sxs-lookup"><span data-stu-id="07c97-501">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="07c97-502">In *Program.cs*caricare la `Kestrel` sezione della configurazione nella configurazione di Gheppio:</span><span class="sxs-lookup"><span data-stu-id="07c97-502">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="07c97-503">Entrambi gli approcci precedenti funzionano con qualsiasi [provider di configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="07c97-503">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="07c97-504">Timeout keep-alive</span><span class="sxs-lookup"><span data-stu-id="07c97-504">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="07c97-505">Ottiene o imposta il [timeout keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="07c97-505">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="07c97-506">Il valore predefinito è 2 minuti.</span><span class="sxs-lookup"><span data-stu-id="07c97-506">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="07c97-507">Numero massimo di connessioni client</span><span class="sxs-lookup"><span data-stu-id="07c97-507">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="07c97-508">È possibile impostare il numero massimo di connessioni TCP aperte simultaneamente per l'intera app con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="07c97-508">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="07c97-509">È previsto un limite separato per le connessioni che sono state aggiornate da HTTP o HTTPS a un altro protocollo (ad esempio su una richiesta WebSocket).</span><span class="sxs-lookup"><span data-stu-id="07c97-509">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="07c97-510">Dopo l'aggiornamento di una connessione, questa non viene conteggiata per il limite `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="07c97-510">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="07c97-511">Per impostazione predefinita, il numero massimo di connessioni è illimitato (null).</span><span class="sxs-lookup"><span data-stu-id="07c97-511">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="07c97-512">Dimensione massima del corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="07c97-512">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="07c97-513">La dimensione massima predefinita del corpo della richiesta è pari a 30.000.000 di byte, equivalenti a circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="07c97-513">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="07c97-514">Il metodo consigliato per ignorare il limite in un'app ASP.NET Core MVC è l'uso dell'attributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> in un metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="07c97-514">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="07c97-515">L'esempio seguente illustra come configurare il vincolo per l'app in ogni richiesta:</span><span class="sxs-lookup"><span data-stu-id="07c97-515">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="07c97-516">Eseguire l'override dell'impostazione per una richiesta specifica nel middleware:</span><span class="sxs-lookup"><span data-stu-id="07c97-516">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="07c97-517">Viene generata un'eccezione se l'app configura il limite per una richiesta dopo che l'app ha iniziato a leggere la richiesta.</span><span class="sxs-lookup"><span data-stu-id="07c97-517">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="07c97-518">Una proprietà `IsReadOnly` indica se la proprietà `MaxRequestBodySize` è in stato di sola lettura e pertanto è troppo tardi per configurare il limite.</span><span class="sxs-lookup"><span data-stu-id="07c97-518">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="07c97-519">Quando un'app viene eseguita [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) dietro il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), il limite della dimensione del corpo della richiesta di Kestrel è disabilitato perché viene già impostato da IIS.</span><span class="sxs-lookup"><span data-stu-id="07c97-519">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="07c97-520">Velocità minima dei dati del corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="07c97-520">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="07c97-521">Kestrel controlla ogni secondo se i dati arrivano alla velocità in byte al secondo specificata.</span><span class="sxs-lookup"><span data-stu-id="07c97-521">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="07c97-522">Se la frequenza scende sotto il valore minimo, si è verificato il timeout della connessione. Il periodo di tolleranza è la quantità di tempo che il gheppio concede al client di aumentare la velocità di invio fino al minimo. la frequenza non viene controllata durante tale periodo di tempo.</span><span class="sxs-lookup"><span data-stu-id="07c97-522">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="07c97-523">Il periodo di prova consente di evitare l'interruzione di connessioni che inizialmente inviano i dati a velocità ridotta a causa dell'avvio lento del protocollo TCP.</span><span class="sxs-lookup"><span data-stu-id="07c97-523">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="07c97-524">La velocità minima predefinita è di 240 byte al secondo, con un periodo di tolleranza di 5 secondi.</span><span class="sxs-lookup"><span data-stu-id="07c97-524">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="07c97-525">Anche per la risposta è prevista una velocità minima.</span><span class="sxs-lookup"><span data-stu-id="07c97-525">A minimum rate also applies to the response.</span></span> <span data-ttu-id="07c97-526">Il codice per impostare il limite della richiesta e della risposta è identico e varia solo per `RequestBody` o `Response` nei nomi di proprietà e di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="07c97-526">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="07c97-527">L'esempio seguente visualizza come configurare la velocità minima dei dati in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="07c97-527">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="07c97-528">Ignorare i limiti di velocità minima per ogni richiesta nel middleware:</span><span class="sxs-lookup"><span data-stu-id="07c97-528">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="07c97-529">Nessuna delle due funzionalità relative alla velocità, a cui si fa riferimento nell'esempio precedente, è presente in `HttpContext.Features` per le richieste HTTP/2 perché la modifica dei limiti di velocità per ogni richiesta non è supportata per HTTP/2 a causa del supporto del protocollo per il multiplexing delle richieste.</span><span class="sxs-lookup"><span data-stu-id="07c97-529">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="07c97-530">I limiti di velocità a livello di server configurati tramite `KestrelServerOptions.Limits` vengono tuttavia applicati alle connessioni HTTP/1.x e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="07c97-530">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="07c97-531">Timeout delle intestazioni delle richieste</span><span class="sxs-lookup"><span data-stu-id="07c97-531">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="07c97-532">Ottiene o imposta la quantità massima di tempo che il server dedica alla ricezione delle intestazioni delle richieste.</span><span class="sxs-lookup"><span data-stu-id="07c97-532">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="07c97-533">Il valore predefinito è 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="07c97-533">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="07c97-534">Numero massimo di flussi per connessione</span><span class="sxs-lookup"><span data-stu-id="07c97-534">Maximum streams per connection</span></span>

<span data-ttu-id="07c97-535">`Http2.MaxStreamsPerConnection` limita il numero di flussi di richieste simultanee per ogni connessione HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="07c97-535">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="07c97-536">I flussi in eccesso vengono rifiutati.</span><span class="sxs-lookup"><span data-stu-id="07c97-536">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="07c97-537">Il valore predefinito è 100.</span><span class="sxs-lookup"><span data-stu-id="07c97-537">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="07c97-538">Dimensioni della tabella delle intestazioni</span><span class="sxs-lookup"><span data-stu-id="07c97-538">Header table size</span></span>

<span data-ttu-id="07c97-539">Il decodificatore HPACK decomprime le intestazioni HTTP per le connessioni HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="07c97-539">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="07c97-540">`Http2.HeaderTableSize` limita le dimensioni della tabella di compressione delle intestazioni usata dal decodificatore HPACK.</span><span class="sxs-lookup"><span data-stu-id="07c97-540">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="07c97-541">Il valore viene specificato in ottetti e deve essere maggiore di zero (0).</span><span class="sxs-lookup"><span data-stu-id="07c97-541">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="07c97-542">Il valore predefinito è 4096.</span><span class="sxs-lookup"><span data-stu-id="07c97-542">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="07c97-543">Dimensione massima del frame</span><span class="sxs-lookup"><span data-stu-id="07c97-543">Maximum frame size</span></span>

<span data-ttu-id="07c97-544">`Http2.MaxFrameSize` indica le dimensioni massime del payload del frame di connessione HTTP/2 da ricevere.</span><span class="sxs-lookup"><span data-stu-id="07c97-544">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="07c97-545">Il valore viene specificato in ottetti e deve essere compreso tra 2^14 (16.384) e 2^24-1 (16.777.215).</span><span class="sxs-lookup"><span data-stu-id="07c97-545">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="07c97-546">Il valore predefinito è 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="07c97-546">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="07c97-547">Dimensioni massime dell'intestazione della richiesta</span><span class="sxs-lookup"><span data-stu-id="07c97-547">Maximum request header size</span></span>

<span data-ttu-id="07c97-548">`Http2.MaxRequestHeaderFieldSize` indica le dimensioni massime consentite in ottetti di valori di intestazione di richiesta.</span><span class="sxs-lookup"><span data-stu-id="07c97-548">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="07c97-549">Questo limite si applica insieme sia al nome che al valore nelle rappresentazioni compresse e non compresse.</span><span class="sxs-lookup"><span data-stu-id="07c97-549">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="07c97-550">Il valore deve essere maggiore di zero (0).</span><span class="sxs-lookup"><span data-stu-id="07c97-550">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="07c97-551">Il valore predefinito è 8.192.</span><span class="sxs-lookup"><span data-stu-id="07c97-551">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="07c97-552">Dimensioni della finestra di connessione iniziali</span><span class="sxs-lookup"><span data-stu-id="07c97-552">Initial connection window size</span></span>

<span data-ttu-id="07c97-553">`Http2.InitialConnectionWindowSize` indica il numero massimo di byte di dati del corpo della richiesta che il server memorizza nel buffer in una volta, aggregati in tutte le richieste (flussi), per ogni connessione.</span><span class="sxs-lookup"><span data-stu-id="07c97-553">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="07c97-554">Le richieste sono anche limitate da `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="07c97-554">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="07c97-555">Il valore deve essere maggiore di o uguale a 65.535 e minore di 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="07c97-555">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="07c97-556">Il valore predefinito è 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="07c97-556">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="07c97-557">Dimensioni della finestra di flusso iniziali</span><span class="sxs-lookup"><span data-stu-id="07c97-557">Initial stream window size</span></span>

<span data-ttu-id="07c97-558">`Http2.InitialStreamWindowSize` indica il numero massimo di byte di dati del corpo della richiesta che il server memorizza nel buffer in una volta per ogni richiesta (flusso).</span><span class="sxs-lookup"><span data-stu-id="07c97-558">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="07c97-559">Le richieste sono anche limitate da `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="07c97-559">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="07c97-560">Il valore deve essere maggiore di o uguale a 65.535 e minore di 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="07c97-560">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="07c97-561">Il valore predefinito è 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="07c97-561">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="07c97-562">I/O sincrono</span><span class="sxs-lookup"><span data-stu-id="07c97-562">Synchronous I/O</span></span>

<span data-ttu-id="07c97-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Controlla se è consentito l'I/O sincrono per la richiesta e la risposta.</span><span class="sxs-lookup"><span data-stu-id="07c97-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="07c97-564">Il valore predefinito è `true`.</span><span class="sxs-lookup"><span data-stu-id="07c97-564">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="07c97-565">Un numero elevato di operazioni di I/O sincrone bloccanti può causare l'esaurimento del pool di thread, il che rende l'app non risponde.</span><span class="sxs-lookup"><span data-stu-id="07c97-565">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="07c97-566">Abilitare `AllowSynchronousIO` solo quando si usa una libreria che non supporta l'I/O asincrono.</span><span class="sxs-lookup"><span data-stu-id="07c97-566">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="07c97-567">L'esempio seguente abilita l'I/O sincrono:</span><span class="sxs-lookup"><span data-stu-id="07c97-567">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="07c97-568">Per informazioni su altre opzioni e limiti di Kestrel, vedere:</span><span class="sxs-lookup"><span data-stu-id="07c97-568">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="07c97-569">Configurazione dell'endpoint</span><span class="sxs-lookup"><span data-stu-id="07c97-569">Endpoint configuration</span></span>

<span data-ttu-id="07c97-570">Per impostazione predefinita, ASP.NET Core è associato a:</span><span class="sxs-lookup"><span data-stu-id="07c97-570">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="07c97-571">`https://localhost:5001` (quando è presente un certificato di sviluppo locale)</span><span class="sxs-lookup"><span data-stu-id="07c97-571">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="07c97-572">Specificare gli URL usando gli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-572">Specify URLs using the:</span></span>

* <span data-ttu-id="07c97-573">La variabile di ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="07c97-573">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="07c97-574">L'argomento della riga di comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="07c97-574">`--urls` command-line argument.</span></span>
* <span data-ttu-id="07c97-575">La chiave di configurazione dell'host `urls`.</span><span class="sxs-lookup"><span data-stu-id="07c97-575">`urls` host configuration key.</span></span>
* <span data-ttu-id="07c97-576">Il metodo di estensione `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="07c97-576">`UseUrls` extension method.</span></span>

<span data-ttu-id="07c97-577">Il valore specificato usando i metodi seguenti può essere uno o più endpoint HTTP e HTTPS (HTTPS se è disponibile un certificato predefinito).</span><span class="sxs-lookup"><span data-stu-id="07c97-577">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="07c97-578">Configurare il valore come un elenco delimitato da punto e virgola (ad esempio, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="07c97-578">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="07c97-579">Per altre informazioni su questi approcci, vedere [URL del server](xref:fundamentals/host/web-host#server-urls) e [Override della configurazione](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="07c97-579">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="07c97-580">Viene creato un certificato di sviluppo nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-580">A development certificate is created:</span></span>

* <span data-ttu-id="07c97-581">Quando viene installato [.NET Core SDK](/dotnet/core/sdk).</span><span class="sxs-lookup"><span data-stu-id="07c97-581">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="07c97-582">Quando per creare un certificato viene usato [lo strumento dev-certs](xref:aspnetcore-2.1#https).</span><span class="sxs-lookup"><span data-stu-id="07c97-582">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="07c97-583">Alcuni browser richiedono la concessione di autorizzazioni esplicite per considerare attendibile il certificato di sviluppo locale.</span><span class="sxs-lookup"><span data-stu-id="07c97-583">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="07c97-584">I modelli di progetto consentono di configurare le app per l'esecuzione su HTTPS per impostazione predefinita e includono il [reindirizzamento HTTPS e il supporto HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="07c97-584">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="07c97-585">Chiamare i metodi <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> oppure <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> su <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> per configurare le porte e i prefissi URL per Kestrel.</span><span class="sxs-lookup"><span data-stu-id="07c97-585">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="07c97-586">Anche `UseUrls`, l'argomento della riga di comando `--urls`, la chiave di configurazione dell'host `urls` e la variabile di ambiente `ASPNETCORE_URLS` funzionano, ma con le limitazioni indicate più avanti nella sezione (deve essere disponibile un certificato predefinito per la configurazione dell'endopoint HTTPS).</span><span class="sxs-lookup"><span data-stu-id="07c97-586">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="07c97-587">`KestrelServerOptions`configurazione</span><span class="sxs-lookup"><span data-stu-id="07c97-587">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="07c97-588">ConfigureEndpointDefaults (azione\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="07c97-588">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="07c97-589">Specifica un elemento di configurazione `Action` da eseguire per ogni endpoint specificato.</span><span class="sxs-lookup"><span data-stu-id="07c97-589">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="07c97-590">Se si chiama `ConfigureEndpointDefaults` più volte, gli elementi `Action` precedenti vengono sostituiti con l'ultimo elemento `Action` specificato.</span><span class="sxs-lookup"><span data-stu-id="07c97-590">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="07c97-591">Per gli endpoint creati chiamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **prima** della <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> chiamata a non verranno applicati i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="07c97-591">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="07c97-592">ConfigureHttpsDefaults (azione\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="07c97-592">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="07c97-593">Specifica un elemento di configurazione `Action` da eseguire per ogni endpoint HTTPS.</span><span class="sxs-lookup"><span data-stu-id="07c97-593">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="07c97-594">Se si chiama `ConfigureHttpsDefaults` più volte, gli elementi `Action` precedenti vengono sostituiti con l'ultimo elemento `Action` specificato.</span><span class="sxs-lookup"><span data-stu-id="07c97-594">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="07c97-595">Per gli endpoint creati chiamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **prima** della <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> chiamata a non verranno applicati i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="07c97-595">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="07c97-596">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="07c97-596">Configure(IConfiguration)</span></span>

<span data-ttu-id="07c97-597">Crea un loader di configurazione per la configurazione di Kestrel che accetta un elemento <xref:Microsoft.Extensions.Configuration.IConfiguration> come input.</span><span class="sxs-lookup"><span data-stu-id="07c97-597">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="07c97-598">L'ambito della configurazione deve essere la sezione di configurazione per Kestrel.</span><span class="sxs-lookup"><span data-stu-id="07c97-598">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="07c97-599">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="07c97-599">ListenOptions.UseHttps</span></span>

<span data-ttu-id="07c97-600">Configura Kestrel per l'uso di HTTPS.</span><span class="sxs-lookup"><span data-stu-id="07c97-600">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="07c97-601">Estensioni `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="07c97-601">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="07c97-602">`UseHttps`&ndash; Configurare gheppio per l'uso di HTTPS con il certificato predefinito.</span><span class="sxs-lookup"><span data-stu-id="07c97-602">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="07c97-603">Genera un'eccezione se non è stato configurato alcun certificato predefinito.</span><span class="sxs-lookup"><span data-stu-id="07c97-603">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="07c97-604">Parametri `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="07c97-604">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="07c97-605">`filename` è il percorso e il nome file di un file di certificato, relativo alla directory che contiene i file di contenuto dell'app.</span><span class="sxs-lookup"><span data-stu-id="07c97-605">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="07c97-606">`password` è la password richiesta per accedere ai dati del certificato X.509.</span><span class="sxs-lookup"><span data-stu-id="07c97-606">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="07c97-607">`configureOptions` è un elemento `Action` per configurare `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="07c97-607">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="07c97-608">Restituisce `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="07c97-608">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="07c97-609">`storeName` è l'archivio certificati da cui caricare il certificato.</span><span class="sxs-lookup"><span data-stu-id="07c97-609">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="07c97-610">`subject` è il nome dell'oggetto del certificato.</span><span class="sxs-lookup"><span data-stu-id="07c97-610">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="07c97-611">`allowInvalid` indica se devono essere considerati i certificati non validi, come ad esempio i certificati autofirmati.</span><span class="sxs-lookup"><span data-stu-id="07c97-611">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="07c97-612">`location` è il percorso dell'archivio da cui caricare il certificato.</span><span class="sxs-lookup"><span data-stu-id="07c97-612">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="07c97-613">`serverCertificate` è il certificato X.509.</span><span class="sxs-lookup"><span data-stu-id="07c97-613">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="07c97-614">Nell'ambiente di produzione, HTTPS deve essere configurato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="07c97-614">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="07c97-615">Come minimo, è necessario specificare un certificato predefinito.</span><span class="sxs-lookup"><span data-stu-id="07c97-615">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="07c97-616">Configurazioni supportate descritte in seguito:</span><span class="sxs-lookup"><span data-stu-id="07c97-616">Supported configurations described next:</span></span>

* <span data-ttu-id="07c97-617">Nessuna configurazione</span><span class="sxs-lookup"><span data-stu-id="07c97-617">No configuration</span></span>
* <span data-ttu-id="07c97-618">Sostituire il certificato predefinito della configurazione</span><span class="sxs-lookup"><span data-stu-id="07c97-618">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="07c97-619">Modificare le impostazioni predefinite nel codice</span><span class="sxs-lookup"><span data-stu-id="07c97-619">Change the defaults in code</span></span>

<span data-ttu-id="07c97-620">*Nessuna configurazione*</span><span class="sxs-lookup"><span data-stu-id="07c97-620">*No configuration*</span></span>

<span data-ttu-id="07c97-621">Kestrel è in ascolto su `http://localhost:5000` e `https://localhost:5001` (se è disponibile un certificato predefinito).</span><span class="sxs-lookup"><span data-stu-id="07c97-621">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="07c97-622">*Sostituire il certificato predefinito della configurazione*</span><span class="sxs-lookup"><span data-stu-id="07c97-622">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="07c97-623">`CreateDefaultBuilder` chiama `Configure(context.Configuration.GetSection("Kestrel"))` per impostazione predefinita per caricare la configurazione di Kestrel.</span><span class="sxs-lookup"><span data-stu-id="07c97-623">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="07c97-624">È disponibile per Kestrel uno schema di configurazione delle impostazioni delle app HTTPS predefinito.</span><span class="sxs-lookup"><span data-stu-id="07c97-624">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="07c97-625">Configurare più endpoint, inclusi gli URL e i certificati da usare, da un file su disco o da un archivio certificati.</span><span class="sxs-lookup"><span data-stu-id="07c97-625">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="07c97-626">Nel file *appsettings.json* di esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="07c97-626">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="07c97-627">Impostare **AllowInvalid** su `true` per consentire l'uso di certificati non validi, come ad esempio i certificati autofirmati.</span><span class="sxs-lookup"><span data-stu-id="07c97-627">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="07c97-628">Tutti gli endpoint HTTPS che non specificano un certificato (**HttpsDefaultCert** nell'esempio che segue) usano il certificato definito in **Certificates** > **Default** o il certificato di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="07c97-628">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="07c97-629">Un'alternativa all'uso di **Path** e **Password** per qualsiasi nodo del certificato consiste nello specificare il certificato usando i campi dell'archivio certificati.</span><span class="sxs-lookup"><span data-stu-id="07c97-629">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="07c97-630">Ad esempio, il certificato**predefinito** **Certificates** > può essere specificato come:</span><span class="sxs-lookup"><span data-stu-id="07c97-630">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="07c97-631">Note di schema:</span><span class="sxs-lookup"><span data-stu-id="07c97-631">Schema notes:</span></span>

* <span data-ttu-id="07c97-632">I nomi degli endpoint non applicano la distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="07c97-632">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="07c97-633">Ad esempio, sono validi sia `HTTPS` che `Https`.</span><span class="sxs-lookup"><span data-stu-id="07c97-633">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="07c97-634">Il parametro `Url` è obbligatorio per ogni endpoint.</span><span class="sxs-lookup"><span data-stu-id="07c97-634">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="07c97-635">Il formato per questo parametro è uguale a quello del parametro di configurazione di primo livello `Urls`, con la differenza che si limita a un singolo valore.</span><span class="sxs-lookup"><span data-stu-id="07c97-635">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="07c97-636">Questi endpoint sostituiscono quelli definiti nella configurazione di primo livello `Urls`, non vi si aggiungono.</span><span class="sxs-lookup"><span data-stu-id="07c97-636">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="07c97-637">Gli endpoint definiti nel codice tramite `Listen` si aggiungono agli endpoint definiti nella sezione di configurazione.</span><span class="sxs-lookup"><span data-stu-id="07c97-637">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="07c97-638">La sezione `Certificate` è facoltativa.</span><span class="sxs-lookup"><span data-stu-id="07c97-638">The `Certificate` section is optional.</span></span> <span data-ttu-id="07c97-639">Se la sezione `Certificate` non è specificata, vengono usati i valori predefiniti definiti negli scenari precedenti.</span><span class="sxs-lookup"><span data-stu-id="07c97-639">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="07c97-640">Se non è disponibile alcun valore predefinito, il server genera un'eccezione e non viene avviato.</span><span class="sxs-lookup"><span data-stu-id="07c97-640">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="07c97-641">La `Certificate` sezione supporta sia la**password** del **percorso**&ndash;che i certificati dell'**Archivio** dell' **oggetto**&ndash;.</span><span class="sxs-lookup"><span data-stu-id="07c97-641">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="07c97-642">In questo modo è possibile definire un numero qualsiasi di endpoint, purché non provochino conflitti di porte.</span><span class="sxs-lookup"><span data-stu-id="07c97-642">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="07c97-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))` restituisce un oggetto `KestrelConfigurationLoader` con un metodo `.Endpoint(string name, listenOptions => { })` che può essere usato per integrare le impostazioni dell'endpoint configurato:</span><span class="sxs-lookup"><span data-stu-id="07c97-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="07c97-644">`KestrelServerOptions.ConfigurationLoader`è possibile accedere direttamente a per continuare a scorrere sul caricatore esistente, ad esempio quello fornito da <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="07c97-644">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="07c97-645">La sezione di configurazione per ogni endpoint è disponibile nelle opzioni del `Endpoint` metodo in modo che sia possibile leggere le impostazioni personalizzate.</span><span class="sxs-lookup"><span data-stu-id="07c97-645">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="07c97-646">È possibile caricare più configurazioni chiamando ancora `options.Configure(context.Configuration.GetSection("{SECTION}"))` con un'altra sezione.</span><span class="sxs-lookup"><span data-stu-id="07c97-646">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="07c97-647">Viene usata solo l'ultima configurazione, a meno che `Load` sia stato chiamato esplicitamente in istanze precedenti.</span><span class="sxs-lookup"><span data-stu-id="07c97-647">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="07c97-648">Il metapacchetto non chiama `Load` in modo che la sezione di configurazione predefinita venga sostituita.</span><span class="sxs-lookup"><span data-stu-id="07c97-648">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="07c97-649">`KestrelConfigurationLoader` riflette la famiglia di API `Listen` da `KestrelServerOptions` all'overload di `Endpoint`, in modo che gli endpoint di codice e di configurazione possano essere configurati nella stessa posizione.</span><span class="sxs-lookup"><span data-stu-id="07c97-649">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="07c97-650">Questi overload non usano nomi e usano solo le impostazioni predefinite della configurazione.</span><span class="sxs-lookup"><span data-stu-id="07c97-650">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="07c97-651">*Modificare le impostazioni predefinite nel codice*</span><span class="sxs-lookup"><span data-stu-id="07c97-651">*Change the defaults in code*</span></span>

<span data-ttu-id="07c97-652">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` possono essere usati per modificare le impostazioni predefinite per `ListenOptions` e `HttpsConnectionAdapterOptions`, inclusa l'esecuzione dell'override del certificato predefinito specificato nello scenario precedente.</span><span class="sxs-lookup"><span data-stu-id="07c97-652">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="07c97-653">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devono essere chiamati prima che venga configurato qualsiasi endpoint.</span><span class="sxs-lookup"><span data-stu-id="07c97-653">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="07c97-654">*Supporto kestrel per SNI*</span><span class="sxs-lookup"><span data-stu-id="07c97-654">*Kestrel support for SNI*</span></span>

<span data-ttu-id="07c97-655">[L'Indicazione nome server (SNI)](https://tools.ietf.org/html/rfc6066#section-3) può essere usata per ospitare più domini sulla stessa porta e indirizzo IP.</span><span class="sxs-lookup"><span data-stu-id="07c97-655">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="07c97-656">Perché SNI funzioni è necessario che il client invii al server il nome host per la sessione protetta durante l'handshake TLS in modo che il server possa specificare il certificato corretto.</span><span class="sxs-lookup"><span data-stu-id="07c97-656">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="07c97-657">Il client usa il certificato specificato per la comunicazione crittografata con il server durante la sessione protetta che segue l'handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="07c97-657">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="07c97-658">Kestrel supporta SNI tramite il callback `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="07c97-658">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="07c97-659">Il callback viene richiamato una volta per ogni connessione per consentire all'app di controllare il nome host e selezionare il certificato appropriato.</span><span class="sxs-lookup"><span data-stu-id="07c97-659">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="07c97-660">Il supporto SNI richiede:</span><span class="sxs-lookup"><span data-stu-id="07c97-660">SNI support requires:</span></span>

* <span data-ttu-id="07c97-661">In esecuzione su Framework `netcoreapp2.1` di destinazione o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="07c97-661">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="07c97-662">In `net461` o versioni successive, il callback viene richiamato `name` , ma `null`è sempre.</span><span class="sxs-lookup"><span data-stu-id="07c97-662">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="07c97-663">L'elemento `name` è `null` anche se il client non specifica il parametro del nome host nell'handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="07c97-663">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="07c97-664">Esecuzione di tutti i siti Web nella stessa istanza di Kestrel.</span><span class="sxs-lookup"><span data-stu-id="07c97-664">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="07c97-665">Kestrel supporta la condivisione di un indirizzo IP e di una porta tra più istanze solo con un proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="07c97-665">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="07c97-666">Registrazione connessione</span><span class="sxs-lookup"><span data-stu-id="07c97-666">Connection logging</span></span>

<span data-ttu-id="07c97-667">Chiamare <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> per creare log a livello di debug per la comunicazione a livello di byte in una connessione.</span><span class="sxs-lookup"><span data-stu-id="07c97-667">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="07c97-668">La registrazione delle connessioni è utile per la risoluzione dei problemi nella comunicazione di basso livello, ad esempio durante la crittografia TLS e dietro i proxy.</span><span class="sxs-lookup"><span data-stu-id="07c97-668">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="07c97-669">Se `UseConnectionLogging` viene inserito prima `UseHttps`, viene registrato il traffico crittografato.</span><span class="sxs-lookup"><span data-stu-id="07c97-669">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="07c97-670">Se `UseConnectionLogging` viene inserito dopo `UseHttps`, viene registrato il traffico decrittografato.</span><span class="sxs-lookup"><span data-stu-id="07c97-670">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="07c97-671">Associazione a un socket TCP</span><span class="sxs-lookup"><span data-stu-id="07c97-671">Bind to a TCP socket</span></span>

<span data-ttu-id="07c97-672">Il metodo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> esegue l'associazione a un socket TCP e un'espressione lambda per le opzioni consente di configurare un certificato X.509:</span><span class="sxs-lookup"><span data-stu-id="07c97-672">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="07c97-673">L'esempio configura HTTPS per un endpoint con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="07c97-673">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="07c97-674">Usare la stessa API per configurare altre impostazioni di Kestrel per endpoint specifici.</span><span class="sxs-lookup"><span data-stu-id="07c97-674">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="07c97-675">Associazione a un socket Unix</span><span class="sxs-lookup"><span data-stu-id="07c97-675">Bind to a Unix socket</span></span>

<span data-ttu-id="07c97-676">Impostare l'ascolto su un socket Unix con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> per migliorare le prestazioni con Nginx, come visualizzato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="07c97-676">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="07c97-677">Nel `server`  >  `location`  >  `proxy_pass` file nginx confiuguration impostare la voce su `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="07c97-677">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="07c97-678">`{KESTREL SOCKET}`nome del socket fornito a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (ad esempio, `kestrel-test.sock` nell'esempio precedente).</span><span class="sxs-lookup"><span data-stu-id="07c97-678">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="07c97-679">Verificare che il socket sia scrivibile da nginx (ad esempio, `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="07c97-679">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="07c97-680">Porta 0</span><span class="sxs-lookup"><span data-stu-id="07c97-680">Port 0</span></span>

<span data-ttu-id="07c97-681">Quando si specifica il numero di porta `0`, Kestrel esegue l'associazione dinamica a una porta disponibile.</span><span class="sxs-lookup"><span data-stu-id="07c97-681">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="07c97-682">L'esempio seguente indica come determinare la porta alla quale Kestrel ha eseguito l'associazione in fase di esecuzione:</span><span class="sxs-lookup"><span data-stu-id="07c97-682">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="07c97-683">Quando si esegue l'app, l'output della finestra della console indica la porta dinamica in cui l'app può essere raggiunta:</span><span class="sxs-lookup"><span data-stu-id="07c97-683">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="07c97-684">Limitazioni</span><span class="sxs-lookup"><span data-stu-id="07c97-684">Limitations</span></span>

<span data-ttu-id="07c97-685">Configurare gli endpoint con i metodi seguenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-685">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="07c97-686">L'argomento della riga di comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="07c97-686">`--urls` command-line argument</span></span>
* <span data-ttu-id="07c97-687">La chiave di configurazione dell'host `urls`</span><span class="sxs-lookup"><span data-stu-id="07c97-687">`urls` host configuration key</span></span>
* <span data-ttu-id="07c97-688">La variabile di ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="07c97-688">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="07c97-689">Questi metodi sono utili se si vuole che il codice funzioni con server diversi da Kestrel.</span><span class="sxs-lookup"><span data-stu-id="07c97-689">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="07c97-690">Tenere presenti, tuttavia, le limitazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-690">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="07c97-691">HTTPS non può essere usato con questi approcci, a meno che non venga specificato un certificato predefinito nella configurazione dell'endpoint HTTPS (ad esempio, usando la configurazione `KestrelServerOptions` o un file di configurazione come illustrato in precedenza in questo argomento).</span><span class="sxs-lookup"><span data-stu-id="07c97-691">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="07c97-692">Quando sia l'approccio `Listen` che l'approccio `UseUrls` vengono usati contemporaneamente, gli endpoint `Listen` eseguono l'override degli endpoint `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="07c97-692">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="07c97-693">Configurazione dell'endpoint IIS</span><span class="sxs-lookup"><span data-stu-id="07c97-693">IIS endpoint configuration</span></span>

<span data-ttu-id="07c97-694">Quando si usa IIS, le associazioni di URL per le associazioni di override di IIS vengono impostate mediante `Listen` o `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="07c97-694">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="07c97-695">Per altre informazioni, vedere l'articolo [Introduzione al modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="07c97-695">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="07c97-696">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="07c97-696">ListenOptions.Protocols</span></span>

<span data-ttu-id="07c97-697">La proprietà `Protocols` stabilisce i protocolli HTTP (`HttpProtocols`) abilitati in un endpoint di connessione o per il server.</span><span class="sxs-lookup"><span data-stu-id="07c97-697">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="07c97-698">Assegnare un valore alla proprietà `Protocols` dall'enumerazione `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="07c97-698">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="07c97-699">Valore di enumerazione `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="07c97-699">`HttpProtocols` enum value</span></span> | <span data-ttu-id="07c97-700">Protocollo di connessione consentito</span><span class="sxs-lookup"><span data-stu-id="07c97-700">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="07c97-701">Solo HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="07c97-701">HTTP/1.1 only.</span></span> <span data-ttu-id="07c97-702">Può essere usato con o senza TLS.</span><span class="sxs-lookup"><span data-stu-id="07c97-702">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="07c97-703">Solo HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="07c97-703">HTTP/2 only.</span></span> <span data-ttu-id="07c97-704">Può essere usato senza TLS solo se il client supporta una [modalità di conoscenza pregressa](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="07c97-704">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="07c97-705">HTTP/1.1 e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="07c97-705">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="07c97-706">HTTP/2 richiede una connessione TLS e [ALPN (Application-Layer Protocol negotiation)](https://tools.ietf.org/html/rfc7301#section-3) ; in caso contrario, il valore predefinito per la connessione è HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="07c97-706">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="07c97-707">Il protocollo predefinito è HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="07c97-707">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="07c97-708">Restrizioni relative a TLS per HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="07c97-708">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="07c97-709">TLS versione 1.2 o successiva</span><span class="sxs-lookup"><span data-stu-id="07c97-709">TLS version 1.2 or later</span></span>
* <span data-ttu-id="07c97-710">Rinegoziazione disabilitata</span><span class="sxs-lookup"><span data-stu-id="07c97-710">Renegotiation disabled</span></span>
* <span data-ttu-id="07c97-711">Compressione disabilitata</span><span class="sxs-lookup"><span data-stu-id="07c97-711">Compression disabled</span></span>
* <span data-ttu-id="07c97-712">Dimensioni minime per lo scambio di chiavi temporanee:</span><span class="sxs-lookup"><span data-stu-id="07c97-712">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="07c97-713">Diffie-Hellman a curva ellittica (ECDH) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bit (minimo)</span><span class="sxs-lookup"><span data-stu-id="07c97-713">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="07c97-714">Minimum-Hellman Diffie-Hellman (dhe &lbrack; `TLS12` &rbrack; &ndash; ) 2048 bit minimo</span><span class="sxs-lookup"><span data-stu-id="07c97-714">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="07c97-715">Pacchetto di crittografia consentito</span><span class="sxs-lookup"><span data-stu-id="07c97-715">Cipher suite not blacklisted</span></span>

<span data-ttu-id="07c97-716">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; &lbrack; con la `FIPS186` curva &rbrack; ellittica P-256 è supportata per impostazione predefinita. `TLS-ECDHE` &rbrack;</span><span class="sxs-lookup"><span data-stu-id="07c97-716">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="07c97-717">Nell'esempio seguente sono consentite connessioni HTTP/1.1 e HTTP/2 sulla porta 8000.</span><span class="sxs-lookup"><span data-stu-id="07c97-717">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="07c97-718">Le connessioni sono protette da TLS con un certificato incluso:</span><span class="sxs-lookup"><span data-stu-id="07c97-718">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="07c97-719">Facoltativamente, creare un'implementazione `IConnectionAdapter` per filtrare gli handshake TLS in base alla connessione in caso di crittografie specifiche:</span><span class="sxs-lookup"><span data-stu-id="07c97-719">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="07c97-720">*Impostare il protocollo dalla configurazione*</span><span class="sxs-lookup"><span data-stu-id="07c97-720">*Set the protocol from configuration*</span></span>

<span data-ttu-id="07c97-721"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> chiama `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` per impostazione predefinita per caricare la configurazione di Kestrel.</span><span class="sxs-lookup"><span data-stu-id="07c97-721"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="07c97-722">Nell'esempio *appsettings.json* seguente viene stabilito un protocollo di connessione predefinito (HTTP/1.1 e HTTP/2) per tutti gli endpoint Kestrel:</span><span class="sxs-lookup"><span data-stu-id="07c97-722">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="07c97-723">Il file di configurazione di esempio seguente stabilisce un protocollo di connessione per un endpoint specifico:</span><span class="sxs-lookup"><span data-stu-id="07c97-723">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="07c97-724">I protocolli specificati nei valori di override del codice sono impostati dalla configurazione.</span><span class="sxs-lookup"><span data-stu-id="07c97-724">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="07c97-725">Configurazione del trasporto</span><span class="sxs-lookup"><span data-stu-id="07c97-725">Transport configuration</span></span>

<span data-ttu-id="07c97-726">Con la versione ASP.NET Core 2.1, il trasporto predefinito di Kestrel non si basa più su Libuv, ma su socket gestiti.</span><span class="sxs-lookup"><span data-stu-id="07c97-726">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="07c97-727">Si tratta di una modifica importante per le app ASP.NET 2.0 Core che vengono aggiornate alla versione 2.1, che chiamano <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> e dipendono da uno dei pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-727">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="07c97-728">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (riferimento diretto al pacchetto)</span><span class="sxs-lookup"><span data-stu-id="07c97-728">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="07c97-729">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="07c97-729">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="07c97-730">Per i progetti che richiedono l'uso di libuv:</span><span class="sxs-lookup"><span data-stu-id="07c97-730">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="07c97-731">Aggiungere una dipendenza per il pacchetto [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="07c97-731">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="07c97-732">Chiamare <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="07c97-732">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="07c97-733">Prefissi URL</span><span class="sxs-lookup"><span data-stu-id="07c97-733">URL prefixes</span></span>

<span data-ttu-id="07c97-734">Se si usa `UseUrls`, l'argomento della riga di comando `--urls`, la chiave di configurazione dell'host `urls` o la variabile di ambiente `ASPNETCORE_URLS`, i prefissi URL possono avere uno dei formati seguenti.</span><span class="sxs-lookup"><span data-stu-id="07c97-734">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="07c97-735">Sono validi solo i prefissi URL HTTP.</span><span class="sxs-lookup"><span data-stu-id="07c97-735">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="07c97-736">Kestrel non supporta HTTPS quando la configurazione delle associazioni URL viene eseguita con `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="07c97-736">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="07c97-737">Indirizzo IPv4 con numero di porta</span><span class="sxs-lookup"><span data-stu-id="07c97-737">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="07c97-738">`0.0.0.0` è un caso speciale che esegue l'associazione a tutti gli indirizzi IPv4.</span><span class="sxs-lookup"><span data-stu-id="07c97-738">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="07c97-739">Indirizzo IPv6 con numero di porta</span><span class="sxs-lookup"><span data-stu-id="07c97-739">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="07c97-740">`[::]` è l'equivalente IPv6 di `0.0.0.0` per IPv4.</span><span class="sxs-lookup"><span data-stu-id="07c97-740">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="07c97-741">Nome host con numero di porta</span><span class="sxs-lookup"><span data-stu-id="07c97-741">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="07c97-742">I nomi host, `*` e `+` non sono casi particolari.</span><span class="sxs-lookup"><span data-stu-id="07c97-742">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="07c97-743">Tutto ciò che non è riconosciuto come un indirizzo IP o un elemento `localhost` valido esegue l'associazione a tutti gli IP IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="07c97-743">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="07c97-744">Per associare nomi host diversi ad app ASP.NET Core diverse sulla stessa porta, usare [HTTP.sys](xref:fundamentals/servers/httpsys) o un server proxy inverso come IIS, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="07c97-744">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="07c97-745">Una configurazione che prevede un proxy inverso richiede il [filtro host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="07c97-745">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="07c97-746">Nome host `localhost` con numero di porta o IP di loopback con numero di porta</span><span class="sxs-lookup"><span data-stu-id="07c97-746">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="07c97-747">Se è specificato `localhost`, Kestrel tenta l'associazione alle interfacce di loopback sia IPv4 che IPv6.</span><span class="sxs-lookup"><span data-stu-id="07c97-747">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="07c97-748">Se la porta richiesta è in uso da parte di un altro servizio in una delle due interfacce di loopback, Kestrel non viene avviato.</span><span class="sxs-lookup"><span data-stu-id="07c97-748">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="07c97-749">Se una delle due interfacce di loopback non è disponibile per qualsiasi altro motivo (in genere perché IPv6 non è supportato), Kestrel registra un avviso.</span><span class="sxs-lookup"><span data-stu-id="07c97-749">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="07c97-750">Filtro host</span><span class="sxs-lookup"><span data-stu-id="07c97-750">Host filtering</span></span>

<span data-ttu-id="07c97-751">Mentre supporta la configurazione in base ai prefissi, ad esempio `http://example.com:5000`, Kestrel ignora quasi sempre il nome host.</span><span class="sxs-lookup"><span data-stu-id="07c97-751">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="07c97-752">L'host `localhost` è un caso speciale usato per l'associazione agli indirizzi di loopback.</span><span class="sxs-lookup"><span data-stu-id="07c97-752">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="07c97-753">Qualsiasi host che non sia un indirizzo IP esplicito esegue l'associazione a tutti gli indirizzi IP pubblici.</span><span class="sxs-lookup"><span data-stu-id="07c97-753">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="07c97-754">Le intestazioni `Host` non vengono convalidate.</span><span class="sxs-lookup"><span data-stu-id="07c97-754">`Host` headers aren't validated.</span></span>

<span data-ttu-id="07c97-755">Come soluzione alternativa, usare il middleware di filtro host.</span><span class="sxs-lookup"><span data-stu-id="07c97-755">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="07c97-756">Il middleware di filtro host viene fornito dal pacchetto [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , incluso nel [metapacchetto Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 o 2,2).</span><span class="sxs-lookup"><span data-stu-id="07c97-756">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="07c97-757">Il middleware viene aggiunto da <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, che chiama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="07c97-757">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="07c97-758">Per impostazione predefinita, il middleware di filtro host è disabilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="07c97-758">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="07c97-759">Per abilitare il middleware, definire una `AllowedHosts` chiave in *appSettings. JSON*/*appSettings.\< EnvironmentName>. JSON*.</span><span class="sxs-lookup"><span data-stu-id="07c97-759">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="07c97-760">Il valore è un elenco con valori delimitati da punto e virgola di nomi host senza numeri di porta:</span><span class="sxs-lookup"><span data-stu-id="07c97-760">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="07c97-761">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="07c97-761">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="07c97-762">Il [middleware delle intestazioni inoltrate](xref:host-and-deploy/proxy-load-balancer) include anche un'opzione <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="07c97-762">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="07c97-763">Il middleware di intestazioni inoltrate e il middleware di filtro host hanno funzionalità simili per diversi scenari.</span><span class="sxs-lookup"><span data-stu-id="07c97-763">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="07c97-764">L'impostazione di `AllowedHosts` con il middleware delle intestazioni inoltrate è appropriato se l'intestazione `Host` non viene mantenuta durante l'inoltro delle richieste con un server proxy inverso o il bilanciamento del carico.</span><span class="sxs-lookup"><span data-stu-id="07c97-764">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="07c97-765">L'impostazione di `AllowedHosts` con il middleware del filtro host è appropriata se si usa Kestrel come server perimetrale rivolto al pubblico o se l'intestazione `Host` viene inoltrata direttamente.</span><span class="sxs-lookup"><span data-stu-id="07c97-765">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="07c97-766">Per altre informazioni sul middleware delle intestazioni inoltrate, vedere <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="07c97-766">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="07c97-767">Kestrel è un [server Web per ASP.NET Core](xref:fundamentals/servers/index) multipiattaforma.</span><span class="sxs-lookup"><span data-stu-id="07c97-767">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="07c97-768">Kestrel è il server Web incluso per impostazione predefinita nei modelli di progetto di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="07c97-768">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="07c97-769">Kestrel supporta gli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-769">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="07c97-770">HTTPS</span><span class="sxs-lookup"><span data-stu-id="07c97-770">HTTPS</span></span>
* <span data-ttu-id="07c97-771">Aggiornamento opaco usato per abilitare [WebSocket](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="07c97-771">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="07c97-772">Socket Unix ad alte prestazioni dietro Nginx</span><span class="sxs-lookup"><span data-stu-id="07c97-772">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="07c97-773">Kestrel è supportato in tutte le piattaforme e le versioni supportate da .NET Core.</span><span class="sxs-lookup"><span data-stu-id="07c97-773">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="07c97-774">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="07c97-774">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="07c97-775">Quando usare Kestrel con un proxy inverso</span><span class="sxs-lookup"><span data-stu-id="07c97-775">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="07c97-776">È possibile usare Kestrel da solo o in combinazione con un *server proxy inverso*, ad esempio [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="07c97-776">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="07c97-777">Il server proxy inverso riceve le richieste HTTP dalla rete e le inoltra a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="07c97-777">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="07c97-778">Kestrel usato come server Web perimetrale (esposto a Internet):</span><span class="sxs-lookup"><span data-stu-id="07c97-778">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel comunica direttamente con Internet senza un server proxy inverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="07c97-780">Kestrel usato in una configurazione proxy inverso:</span><span class="sxs-lookup"><span data-stu-id="07c97-780">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel comunica indirettamente con Internet attraverso un server proxy inverso, ad esempio IIS, Nginx o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="07c97-782">La configurazione, con o senza un server proxy inverso, è una configurazione di hosting supportata.</span><span class="sxs-lookup"><span data-stu-id="07c97-782">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="07c97-783">Se viene utilizzato come server perimetrale in assenza di un server proxy inverso, Kestrel non supporta la condivisione tra più processi dell'IP e della porta.</span><span class="sxs-lookup"><span data-stu-id="07c97-783">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="07c97-784">Quando è configurato per restare in ascolto su una porta, Kestrel gestisce tutto il traffico per tale porta indipendentemente dalle intestazioni `Host` delle richieste.</span><span class="sxs-lookup"><span data-stu-id="07c97-784">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="07c97-785">Un proxy inverso che può condividere le porte può eseguire l'inoltro delle richieste a Kestrel su un unico IP e un'unica porta.</span><span class="sxs-lookup"><span data-stu-id="07c97-785">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="07c97-786">Anche se la presenza di un server proxy inverso non è necessaria, può risultare utile per i motivi seguenti.</span><span class="sxs-lookup"><span data-stu-id="07c97-786">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="07c97-787">Un proxy inverso:</span><span class="sxs-lookup"><span data-stu-id="07c97-787">A reverse proxy:</span></span>

* <span data-ttu-id="07c97-788">Può limitare l'area della superficie di attacco pubblica esposta delle app ospitate.</span><span class="sxs-lookup"><span data-stu-id="07c97-788">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="07c97-789">Offre un livello aggiuntivo di configurazione e protezione.</span><span class="sxs-lookup"><span data-stu-id="07c97-789">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="07c97-790">Potrebbe offrire un'integrazione migliore con l'infrastruttura esistente.</span><span class="sxs-lookup"><span data-stu-id="07c97-790">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="07c97-791">Semplifica il bilanciamento del carico e la configurazione di comunicazioni protette (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="07c97-791">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="07c97-792">Solo il server proxy inverso richiede un certificato X. 509 e il server è in grado di comunicare con i server dell'app nella rete interna tramite HTTP normale.</span><span class="sxs-lookup"><span data-stu-id="07c97-792">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="07c97-793">Una configurazione che prevede un proxy inverso richiede il [filtro host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="07c97-793">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="07c97-794">Come usare Kestrel nelle app ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="07c97-794">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="07c97-795">Il pacchetto [Microsoft. AspNetCore. Server. gheppio](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) è incluso nel [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="07c97-795">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="07c97-796">I modelli di progetto ASP.NET Core usano Kestrel per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="07c97-796">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="07c97-797">In *Program.cs* il codice del modello chiama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, che chiama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> in background.</span><span class="sxs-lookup"><span data-stu-id="07c97-797">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="07c97-798">Per fornire una configurazione aggiuntiva dopo la chiamata di `CreateDefaultBuilder`, chiamare <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="07c97-798">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="07c97-799">Per ulteriori informazioni su `CreateDefaultBuilder` e sulla compilazione dell'host, vedere la sezione *configurare un host* di <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="07c97-799">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="07c97-800">Opzioni Kestrel</span><span class="sxs-lookup"><span data-stu-id="07c97-800">Kestrel options</span></span>

<span data-ttu-id="07c97-801">Il server Web Kestrel dispone di opzioni di configurazione dei vincoli che risultano particolarmente utili nelle distribuzioni con connessione Internet.</span><span class="sxs-lookup"><span data-stu-id="07c97-801">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="07c97-802">Impostare i vincoli per la proprietà <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> della classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="07c97-802">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="07c97-803">La proprietà `Limits` contiene un'istanza della classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="07c97-803">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="07c97-804">Negli esempi seguenti viene usato lo spazio dei nomi <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="07c97-804">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="07c97-805">Le opzioni gheppio, che sono configurate nel codice C# negli esempi seguenti, possono essere impostate anche usando un [provider di configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="07c97-805">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="07c97-806">Il provider di configurazione file, ad esempio, può caricare la configurazione di Gheppio da *appSettings. JSON* o *appSettings. { File Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="07c97-806">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="07c97-807">Usare **uno** degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-807">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="07c97-808">Configurare gheppio in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="07c97-808">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="07c97-809">Inserire un'istanza di `IConfiguration` nella `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="07c97-809">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="07c97-810">Nell'esempio seguente si presuppone che la configurazione inserita venga assegnata alla `Configuration` proprietà.</span><span class="sxs-lookup"><span data-stu-id="07c97-810">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="07c97-811">In `Startup.ConfigureServices`caricare la `Kestrel` sezione della configurazione nella configurazione di Gheppio:</span><span class="sxs-lookup"><span data-stu-id="07c97-811">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="07c97-812">Configurare il gheppio durante la compilazione dell'host:</span><span class="sxs-lookup"><span data-stu-id="07c97-812">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="07c97-813">In *Program.cs*caricare la `Kestrel` sezione della configurazione nella configurazione di Gheppio:</span><span class="sxs-lookup"><span data-stu-id="07c97-813">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="07c97-814">Entrambi gli approcci precedenti funzionano con qualsiasi [provider di configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="07c97-814">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="07c97-815">Timeout keep-alive</span><span class="sxs-lookup"><span data-stu-id="07c97-815">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="07c97-816">Ottiene o imposta il [timeout keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="07c97-816">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="07c97-817">Il valore predefinito è 2 minuti.</span><span class="sxs-lookup"><span data-stu-id="07c97-817">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="07c97-818">Numero massimo di connessioni client</span><span class="sxs-lookup"><span data-stu-id="07c97-818">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="07c97-819">È possibile impostare il numero massimo di connessioni TCP aperte simultaneamente per l'intera app con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="07c97-819">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="07c97-820">È previsto un limite separato per le connessioni che sono state aggiornate da HTTP o HTTPS a un altro protocollo (ad esempio su una richiesta WebSocket).</span><span class="sxs-lookup"><span data-stu-id="07c97-820">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="07c97-821">Dopo l'aggiornamento di una connessione, questa non viene conteggiata per il limite `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="07c97-821">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="07c97-822">Per impostazione predefinita, il numero massimo di connessioni è illimitato (null).</span><span class="sxs-lookup"><span data-stu-id="07c97-822">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="07c97-823">Dimensione massima del corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="07c97-823">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="07c97-824">La dimensione massima predefinita del corpo della richiesta è pari a 30.000.000 di byte, equivalenti a circa 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="07c97-824">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="07c97-825">Il metodo consigliato per ignorare il limite in un'app ASP.NET Core MVC è l'uso dell'attributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> in un metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="07c97-825">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="07c97-826">L'esempio seguente illustra come configurare il vincolo per l'app in ogni richiesta:</span><span class="sxs-lookup"><span data-stu-id="07c97-826">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="07c97-827">Eseguire l'override dell'impostazione per una richiesta specifica nel middleware:</span><span class="sxs-lookup"><span data-stu-id="07c97-827">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="07c97-828">Viene generata un'eccezione se l'app configura il limite per una richiesta dopo che l'app ha iniziato a leggere la richiesta.</span><span class="sxs-lookup"><span data-stu-id="07c97-828">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="07c97-829">Una proprietà `IsReadOnly` indica se la proprietà `MaxRequestBodySize` è in stato di sola lettura e pertanto è troppo tardi per configurare il limite.</span><span class="sxs-lookup"><span data-stu-id="07c97-829">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="07c97-830">Quando un'app viene eseguita [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) dietro il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), il limite della dimensione del corpo della richiesta di Kestrel è disabilitato perché viene già impostato da IIS.</span><span class="sxs-lookup"><span data-stu-id="07c97-830">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="07c97-831">Velocità minima dei dati del corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="07c97-831">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="07c97-832">Kestrel controlla ogni secondo se i dati arrivano alla velocità in byte al secondo specificata.</span><span class="sxs-lookup"><span data-stu-id="07c97-832">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="07c97-833">Se la frequenza scende sotto il valore minimo, si è verificato il timeout della connessione. Il periodo di tolleranza è la quantità di tempo che il gheppio concede al client di aumentare la velocità di invio fino al minimo. la frequenza non viene controllata durante tale periodo di tempo.</span><span class="sxs-lookup"><span data-stu-id="07c97-833">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="07c97-834">Il periodo di prova consente di evitare l'interruzione di connessioni che inizialmente inviano i dati a velocità ridotta a causa dell'avvio lento del protocollo TCP.</span><span class="sxs-lookup"><span data-stu-id="07c97-834">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="07c97-835">La velocità minima predefinita è di 240 byte al secondo, con un periodo di tolleranza di 5 secondi.</span><span class="sxs-lookup"><span data-stu-id="07c97-835">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="07c97-836">Anche per la risposta è prevista una velocità minima.</span><span class="sxs-lookup"><span data-stu-id="07c97-836">A minimum rate also applies to the response.</span></span> <span data-ttu-id="07c97-837">Il codice per impostare il limite della richiesta e della risposta è identico e varia solo per `RequestBody` o `Response` nei nomi di proprietà e di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="07c97-837">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="07c97-838">L'esempio seguente visualizza come configurare la velocità minima dei dati in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="07c97-838">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="07c97-839">Timeout delle intestazioni delle richieste</span><span class="sxs-lookup"><span data-stu-id="07c97-839">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="07c97-840">Ottiene o imposta la quantità massima di tempo che il server dedica alla ricezione delle intestazioni delle richieste.</span><span class="sxs-lookup"><span data-stu-id="07c97-840">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="07c97-841">Il valore predefinito è 30 secondi.</span><span class="sxs-lookup"><span data-stu-id="07c97-841">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="07c97-842">I/O sincrono</span><span class="sxs-lookup"><span data-stu-id="07c97-842">Synchronous I/O</span></span>

<span data-ttu-id="07c97-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Controlla se è consentito l'I/O sincrono per la richiesta e la risposta.</span><span class="sxs-lookup"><span data-stu-id="07c97-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="07c97-844">Il valore predefinito è `true`.</span><span class="sxs-lookup"><span data-stu-id="07c97-844">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="07c97-845">Un numero elevato di operazioni di I/O sincrone bloccanti può causare l'esaurimento del pool di thread, il che rende l'app non risponde.</span><span class="sxs-lookup"><span data-stu-id="07c97-845">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="07c97-846">Abilitare `AllowSynchronousIO` solo quando si usa una libreria che non supporta l'I/O asincrono.</span><span class="sxs-lookup"><span data-stu-id="07c97-846">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="07c97-847">Nell'esempio seguente viene disabilitato l'I/O sincrono:</span><span class="sxs-lookup"><span data-stu-id="07c97-847">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="07c97-848">Per informazioni su altre opzioni e limiti di Kestrel, vedere:</span><span class="sxs-lookup"><span data-stu-id="07c97-848">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="07c97-849">Configurazione dell'endpoint</span><span class="sxs-lookup"><span data-stu-id="07c97-849">Endpoint configuration</span></span>

<span data-ttu-id="07c97-850">Per impostazione predefinita, ASP.NET Core è associato a:</span><span class="sxs-lookup"><span data-stu-id="07c97-850">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="07c97-851">`https://localhost:5001` (quando è presente un certificato di sviluppo locale)</span><span class="sxs-lookup"><span data-stu-id="07c97-851">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="07c97-852">Specificare gli URL usando gli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-852">Specify URLs using the:</span></span>

* <span data-ttu-id="07c97-853">La variabile di ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="07c97-853">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="07c97-854">L'argomento della riga di comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="07c97-854">`--urls` command-line argument.</span></span>
* <span data-ttu-id="07c97-855">La chiave di configurazione dell'host `urls`.</span><span class="sxs-lookup"><span data-stu-id="07c97-855">`urls` host configuration key.</span></span>
* <span data-ttu-id="07c97-856">Il metodo di estensione `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="07c97-856">`UseUrls` extension method.</span></span>

<span data-ttu-id="07c97-857">Il valore specificato usando i metodi seguenti può essere uno o più endpoint HTTP e HTTPS (HTTPS se è disponibile un certificato predefinito).</span><span class="sxs-lookup"><span data-stu-id="07c97-857">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="07c97-858">Configurare il valore come un elenco delimitato da punto e virgola (ad esempio, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="07c97-858">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="07c97-859">Per altre informazioni su questi approcci, vedere [URL del server](xref:fundamentals/host/web-host#server-urls) e [Override della configurazione](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="07c97-859">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="07c97-860">Viene creato un certificato di sviluppo nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-860">A development certificate is created:</span></span>

* <span data-ttu-id="07c97-861">Quando viene installato [.NET Core SDK](/dotnet/core/sdk).</span><span class="sxs-lookup"><span data-stu-id="07c97-861">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="07c97-862">Quando per creare un certificato viene usato [lo strumento dev-certs](xref:aspnetcore-2.1#https).</span><span class="sxs-lookup"><span data-stu-id="07c97-862">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="07c97-863">Alcuni browser richiedono la concessione di autorizzazioni esplicite per considerare attendibile il certificato di sviluppo locale.</span><span class="sxs-lookup"><span data-stu-id="07c97-863">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="07c97-864">I modelli di progetto consentono di configurare le app per l'esecuzione su HTTPS per impostazione predefinita e includono il [reindirizzamento HTTPS e il supporto HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="07c97-864">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="07c97-865">Chiamare i metodi <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> oppure <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> su <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> per configurare le porte e i prefissi URL per Kestrel.</span><span class="sxs-lookup"><span data-stu-id="07c97-865">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="07c97-866">Anche `UseUrls`, l'argomento della riga di comando `--urls`, la chiave di configurazione dell'host `urls` e la variabile di ambiente `ASPNETCORE_URLS` funzionano, ma con le limitazioni indicate più avanti nella sezione (deve essere disponibile un certificato predefinito per la configurazione dell'endopoint HTTPS).</span><span class="sxs-lookup"><span data-stu-id="07c97-866">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="07c97-867">`KestrelServerOptions`configurazione</span><span class="sxs-lookup"><span data-stu-id="07c97-867">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="07c97-868">ConfigureEndpointDefaults (azione\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="07c97-868">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="07c97-869">Specifica un elemento di configurazione `Action` da eseguire per ogni endpoint specificato.</span><span class="sxs-lookup"><span data-stu-id="07c97-869">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="07c97-870">Se si chiama `ConfigureEndpointDefaults` più volte, gli elementi `Action` precedenti vengono sostituiti con l'ultimo elemento `Action` specificato.</span><span class="sxs-lookup"><span data-stu-id="07c97-870">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="07c97-871">Per gli endpoint creati chiamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **prima** della <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> chiamata a non verranno applicati i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="07c97-871">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="07c97-872">ConfigureHttpsDefaults (azione\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="07c97-872">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="07c97-873">Specifica un elemento di configurazione `Action` da eseguire per ogni endpoint HTTPS.</span><span class="sxs-lookup"><span data-stu-id="07c97-873">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="07c97-874">Se si chiama `ConfigureHttpsDefaults` più volte, gli elementi `Action` precedenti vengono sostituiti con l'ultimo elemento `Action` specificato.</span><span class="sxs-lookup"><span data-stu-id="07c97-874">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="07c97-875">Per gli endpoint creati chiamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **prima** della <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> chiamata a non verranno applicati i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="07c97-875">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="07c97-876">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="07c97-876">Configure(IConfiguration)</span></span>

<span data-ttu-id="07c97-877">Crea un loader di configurazione per la configurazione di Kestrel che accetta un elemento <xref:Microsoft.Extensions.Configuration.IConfiguration> come input.</span><span class="sxs-lookup"><span data-stu-id="07c97-877">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="07c97-878">L'ambito della configurazione deve essere la sezione di configurazione per Kestrel.</span><span class="sxs-lookup"><span data-stu-id="07c97-878">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="07c97-879">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="07c97-879">ListenOptions.UseHttps</span></span>

<span data-ttu-id="07c97-880">Configura Kestrel per l'uso di HTTPS.</span><span class="sxs-lookup"><span data-stu-id="07c97-880">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="07c97-881">Estensioni `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="07c97-881">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="07c97-882">`UseHttps`&ndash; Configurare gheppio per l'uso di HTTPS con il certificato predefinito.</span><span class="sxs-lookup"><span data-stu-id="07c97-882">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="07c97-883">Genera un'eccezione se non è stato configurato alcun certificato predefinito.</span><span class="sxs-lookup"><span data-stu-id="07c97-883">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="07c97-884">Parametri `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="07c97-884">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="07c97-885">`filename` è il percorso e il nome file di un file di certificato, relativo alla directory che contiene i file di contenuto dell'app.</span><span class="sxs-lookup"><span data-stu-id="07c97-885">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="07c97-886">`password` è la password richiesta per accedere ai dati del certificato X.509.</span><span class="sxs-lookup"><span data-stu-id="07c97-886">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="07c97-887">`configureOptions` è un elemento `Action` per configurare `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="07c97-887">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="07c97-888">Restituisce `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="07c97-888">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="07c97-889">`storeName` è l'archivio certificati da cui caricare il certificato.</span><span class="sxs-lookup"><span data-stu-id="07c97-889">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="07c97-890">`subject` è il nome dell'oggetto del certificato.</span><span class="sxs-lookup"><span data-stu-id="07c97-890">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="07c97-891">`allowInvalid` indica se devono essere considerati i certificati non validi, come ad esempio i certificati autofirmati.</span><span class="sxs-lookup"><span data-stu-id="07c97-891">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="07c97-892">`location` è il percorso dell'archivio da cui caricare il certificato.</span><span class="sxs-lookup"><span data-stu-id="07c97-892">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="07c97-893">`serverCertificate` è il certificato X.509.</span><span class="sxs-lookup"><span data-stu-id="07c97-893">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="07c97-894">Nell'ambiente di produzione, HTTPS deve essere configurato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="07c97-894">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="07c97-895">Come minimo, è necessario specificare un certificato predefinito.</span><span class="sxs-lookup"><span data-stu-id="07c97-895">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="07c97-896">Configurazioni supportate descritte in seguito:</span><span class="sxs-lookup"><span data-stu-id="07c97-896">Supported configurations described next:</span></span>

* <span data-ttu-id="07c97-897">Nessuna configurazione</span><span class="sxs-lookup"><span data-stu-id="07c97-897">No configuration</span></span>
* <span data-ttu-id="07c97-898">Sostituire il certificato predefinito della configurazione</span><span class="sxs-lookup"><span data-stu-id="07c97-898">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="07c97-899">Modificare le impostazioni predefinite nel codice</span><span class="sxs-lookup"><span data-stu-id="07c97-899">Change the defaults in code</span></span>

<span data-ttu-id="07c97-900">*Nessuna configurazione*</span><span class="sxs-lookup"><span data-stu-id="07c97-900">*No configuration*</span></span>

<span data-ttu-id="07c97-901">Kestrel è in ascolto su `http://localhost:5000` e `https://localhost:5001` (se è disponibile un certificato predefinito).</span><span class="sxs-lookup"><span data-stu-id="07c97-901">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="07c97-902">*Sostituire il certificato predefinito della configurazione*</span><span class="sxs-lookup"><span data-stu-id="07c97-902">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="07c97-903">`CreateDefaultBuilder` chiama `Configure(context.Configuration.GetSection("Kestrel"))` per impostazione predefinita per caricare la configurazione di Kestrel.</span><span class="sxs-lookup"><span data-stu-id="07c97-903">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="07c97-904">È disponibile per Kestrel uno schema di configurazione delle impostazioni delle app HTTPS predefinito.</span><span class="sxs-lookup"><span data-stu-id="07c97-904">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="07c97-905">Configurare più endpoint, inclusi gli URL e i certificati da usare, da un file su disco o da un archivio certificati.</span><span class="sxs-lookup"><span data-stu-id="07c97-905">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="07c97-906">Nel file *appsettings.json* di esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="07c97-906">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="07c97-907">Impostare **AllowInvalid** su `true` per consentire l'uso di certificati non validi, come ad esempio i certificati autofirmati.</span><span class="sxs-lookup"><span data-stu-id="07c97-907">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="07c97-908">Tutti gli endpoint HTTPS che non specificano un certificato (**HttpsDefaultCert** nell'esempio che segue) usano il certificato definito in **Certificates** > **Default** o il certificato di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="07c97-908">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="07c97-909">Un'alternativa all'uso di **Path** e **Password** per qualsiasi nodo del certificato consiste nello specificare il certificato usando i campi dell'archivio certificati.</span><span class="sxs-lookup"><span data-stu-id="07c97-909">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="07c97-910">Ad esempio, il certificato**predefinito** **Certificates** > può essere specificato come:</span><span class="sxs-lookup"><span data-stu-id="07c97-910">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="07c97-911">Note di schema:</span><span class="sxs-lookup"><span data-stu-id="07c97-911">Schema notes:</span></span>

* <span data-ttu-id="07c97-912">I nomi degli endpoint non applicano la distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="07c97-912">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="07c97-913">Ad esempio, sono validi sia `HTTPS` che `Https`.</span><span class="sxs-lookup"><span data-stu-id="07c97-913">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="07c97-914">Il parametro `Url` è obbligatorio per ogni endpoint.</span><span class="sxs-lookup"><span data-stu-id="07c97-914">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="07c97-915">Il formato per questo parametro è uguale a quello del parametro di configurazione di primo livello `Urls`, con la differenza che si limita a un singolo valore.</span><span class="sxs-lookup"><span data-stu-id="07c97-915">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="07c97-916">Questi endpoint sostituiscono quelli definiti nella configurazione di primo livello `Urls`, non vi si aggiungono.</span><span class="sxs-lookup"><span data-stu-id="07c97-916">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="07c97-917">Gli endpoint definiti nel codice tramite `Listen` si aggiungono agli endpoint definiti nella sezione di configurazione.</span><span class="sxs-lookup"><span data-stu-id="07c97-917">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="07c97-918">La sezione `Certificate` è facoltativa.</span><span class="sxs-lookup"><span data-stu-id="07c97-918">The `Certificate` section is optional.</span></span> <span data-ttu-id="07c97-919">Se la sezione `Certificate` non è specificata, vengono usati i valori predefiniti definiti negli scenari precedenti.</span><span class="sxs-lookup"><span data-stu-id="07c97-919">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="07c97-920">Se non è disponibile alcun valore predefinito, il server genera un'eccezione e non viene avviato.</span><span class="sxs-lookup"><span data-stu-id="07c97-920">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="07c97-921">La `Certificate` sezione supporta sia la**password** del **percorso**&ndash;che i certificati dell'**Archivio** dell' **oggetto**&ndash;.</span><span class="sxs-lookup"><span data-stu-id="07c97-921">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="07c97-922">In questo modo è possibile definire un numero qualsiasi di endpoint, purché non provochino conflitti di porte.</span><span class="sxs-lookup"><span data-stu-id="07c97-922">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="07c97-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))` restituisce un oggetto `KestrelConfigurationLoader` con un metodo `.Endpoint(string name, listenOptions => { })` che può essere usato per integrare le impostazioni dell'endpoint configurato:</span><span class="sxs-lookup"><span data-stu-id="07c97-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="07c97-924">`KestrelServerOptions.ConfigurationLoader`è possibile accedere direttamente a per continuare a scorrere sul caricatore esistente, ad esempio quello fornito da <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="07c97-924">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="07c97-925">La sezione di configurazione per ogni endpoint è disponibile nelle opzioni del `Endpoint` metodo in modo che sia possibile leggere le impostazioni personalizzate.</span><span class="sxs-lookup"><span data-stu-id="07c97-925">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="07c97-926">È possibile caricare più configurazioni chiamando ancora `options.Configure(context.Configuration.GetSection("{SECTION}"))` con un'altra sezione.</span><span class="sxs-lookup"><span data-stu-id="07c97-926">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="07c97-927">Viene usata solo l'ultima configurazione, a meno che `Load` sia stato chiamato esplicitamente in istanze precedenti.</span><span class="sxs-lookup"><span data-stu-id="07c97-927">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="07c97-928">Il metapacchetto non chiama `Load` in modo che la sezione di configurazione predefinita venga sostituita.</span><span class="sxs-lookup"><span data-stu-id="07c97-928">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="07c97-929">`KestrelConfigurationLoader` riflette la famiglia di API `Listen` da `KestrelServerOptions` all'overload di `Endpoint`, in modo che gli endpoint di codice e di configurazione possano essere configurati nella stessa posizione.</span><span class="sxs-lookup"><span data-stu-id="07c97-929">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="07c97-930">Questi overload non usano nomi e usano solo le impostazioni predefinite della configurazione.</span><span class="sxs-lookup"><span data-stu-id="07c97-930">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="07c97-931">*Modificare le impostazioni predefinite nel codice*</span><span class="sxs-lookup"><span data-stu-id="07c97-931">*Change the defaults in code*</span></span>

<span data-ttu-id="07c97-932">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` possono essere usati per modificare le impostazioni predefinite per `ListenOptions` e `HttpsConnectionAdapterOptions`, inclusa l'esecuzione dell'override del certificato predefinito specificato nello scenario precedente.</span><span class="sxs-lookup"><span data-stu-id="07c97-932">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="07c97-933">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devono essere chiamati prima che venga configurato qualsiasi endpoint.</span><span class="sxs-lookup"><span data-stu-id="07c97-933">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="07c97-934">*Supporto kestrel per SNI*</span><span class="sxs-lookup"><span data-stu-id="07c97-934">*Kestrel support for SNI*</span></span>

<span data-ttu-id="07c97-935">[L'Indicazione nome server (SNI)](https://tools.ietf.org/html/rfc6066#section-3) può essere usata per ospitare più domini sulla stessa porta e indirizzo IP.</span><span class="sxs-lookup"><span data-stu-id="07c97-935">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="07c97-936">Perché SNI funzioni è necessario che il client invii al server il nome host per la sessione protetta durante l'handshake TLS in modo che il server possa specificare il certificato corretto.</span><span class="sxs-lookup"><span data-stu-id="07c97-936">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="07c97-937">Il client usa il certificato specificato per la comunicazione crittografata con il server durante la sessione protetta che segue l'handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="07c97-937">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="07c97-938">Kestrel supporta SNI tramite il callback `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="07c97-938">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="07c97-939">Il callback viene richiamato una volta per ogni connessione per consentire all'app di controllare il nome host e selezionare il certificato appropriato.</span><span class="sxs-lookup"><span data-stu-id="07c97-939">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="07c97-940">Il supporto SNI richiede:</span><span class="sxs-lookup"><span data-stu-id="07c97-940">SNI support requires:</span></span>

* <span data-ttu-id="07c97-941">In esecuzione su Framework `netcoreapp2.1` di destinazione o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="07c97-941">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="07c97-942">In `net461` o versioni successive, il callback viene richiamato `name` , ma `null`è sempre.</span><span class="sxs-lookup"><span data-stu-id="07c97-942">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="07c97-943">L'elemento `name` è `null` anche se il client non specifica il parametro del nome host nell'handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="07c97-943">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="07c97-944">Esecuzione di tutti i siti Web nella stessa istanza di Kestrel.</span><span class="sxs-lookup"><span data-stu-id="07c97-944">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="07c97-945">Kestrel supporta la condivisione di un indirizzo IP e di una porta tra più istanze solo con un proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="07c97-945">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="07c97-946">Registrazione connessione</span><span class="sxs-lookup"><span data-stu-id="07c97-946">Connection logging</span></span>

<span data-ttu-id="07c97-947">Chiamare <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> per creare log a livello di debug per la comunicazione a livello di byte in una connessione.</span><span class="sxs-lookup"><span data-stu-id="07c97-947">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="07c97-948">La registrazione delle connessioni è utile per la risoluzione dei problemi nella comunicazione di basso livello, ad esempio durante la crittografia TLS e dietro i proxy.</span><span class="sxs-lookup"><span data-stu-id="07c97-948">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="07c97-949">Se `UseConnectionLogging` viene inserito prima `UseHttps`, viene registrato il traffico crittografato.</span><span class="sxs-lookup"><span data-stu-id="07c97-949">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="07c97-950">Se `UseConnectionLogging` viene inserito dopo `UseHttps`, viene registrato il traffico decrittografato.</span><span class="sxs-lookup"><span data-stu-id="07c97-950">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="07c97-951">Associazione a un socket TCP</span><span class="sxs-lookup"><span data-stu-id="07c97-951">Bind to a TCP socket</span></span>

<span data-ttu-id="07c97-952">Il metodo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> esegue l'associazione a un socket TCP e un'espressione lambda per le opzioni consente di configurare un certificato X.509:</span><span class="sxs-lookup"><span data-stu-id="07c97-952">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="07c97-953">L'esempio configura HTTPS per un endpoint con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="07c97-953">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="07c97-954">Usare la stessa API per configurare altre impostazioni di Kestrel per endpoint specifici.</span><span class="sxs-lookup"><span data-stu-id="07c97-954">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="07c97-955">Associazione a un socket Unix</span><span class="sxs-lookup"><span data-stu-id="07c97-955">Bind to a Unix socket</span></span>

<span data-ttu-id="07c97-956">Impostare l'ascolto su un socket Unix con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> per migliorare le prestazioni con Nginx, come visualizzato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="07c97-956">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="07c97-957">Nel `server`  >  `location`  >  `proxy_pass` file nginx confiuguration impostare la voce su `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="07c97-957">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="07c97-958">`{KESTREL SOCKET}`nome del socket fornito a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (ad esempio, `kestrel-test.sock` nell'esempio precedente).</span><span class="sxs-lookup"><span data-stu-id="07c97-958">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="07c97-959">Verificare che il socket sia scrivibile da nginx (ad esempio, `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="07c97-959">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="07c97-960">Porta 0</span><span class="sxs-lookup"><span data-stu-id="07c97-960">Port 0</span></span>

<span data-ttu-id="07c97-961">Quando si specifica il numero di porta `0`, Kestrel esegue l'associazione dinamica a una porta disponibile.</span><span class="sxs-lookup"><span data-stu-id="07c97-961">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="07c97-962">L'esempio seguente indica come determinare la porta alla quale Kestrel ha eseguito l'associazione in fase di esecuzione:</span><span class="sxs-lookup"><span data-stu-id="07c97-962">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="07c97-963">Quando si esegue l'app, l'output della finestra della console indica la porta dinamica in cui l'app può essere raggiunta:</span><span class="sxs-lookup"><span data-stu-id="07c97-963">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="07c97-964">Limitazioni</span><span class="sxs-lookup"><span data-stu-id="07c97-964">Limitations</span></span>

<span data-ttu-id="07c97-965">Configurare gli endpoint con i metodi seguenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-965">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="07c97-966">L'argomento della riga di comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="07c97-966">`--urls` command-line argument</span></span>
* <span data-ttu-id="07c97-967">La chiave di configurazione dell'host `urls`</span><span class="sxs-lookup"><span data-stu-id="07c97-967">`urls` host configuration key</span></span>
* <span data-ttu-id="07c97-968">La variabile di ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="07c97-968">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="07c97-969">Questi metodi sono utili se si vuole che il codice funzioni con server diversi da Kestrel.</span><span class="sxs-lookup"><span data-stu-id="07c97-969">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="07c97-970">Tenere presenti, tuttavia, le limitazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-970">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="07c97-971">HTTPS non può essere usato con questi approcci, a meno che non venga specificato un certificato predefinito nella configurazione dell'endpoint HTTPS (ad esempio, usando la configurazione `KestrelServerOptions` o un file di configurazione come illustrato in precedenza in questo argomento).</span><span class="sxs-lookup"><span data-stu-id="07c97-971">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="07c97-972">Quando sia l'approccio `Listen` che l'approccio `UseUrls` vengono usati contemporaneamente, gli endpoint `Listen` eseguono l'override degli endpoint `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="07c97-972">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="07c97-973">Configurazione dell'endpoint IIS</span><span class="sxs-lookup"><span data-stu-id="07c97-973">IIS endpoint configuration</span></span>

<span data-ttu-id="07c97-974">Quando si usa IIS, le associazioni di URL per le associazioni di override di IIS vengono impostate mediante `Listen` o `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="07c97-974">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="07c97-975">Per altre informazioni, vedere l'articolo [Introduzione al modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="07c97-975">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="07c97-976">Configurazione del trasporto</span><span class="sxs-lookup"><span data-stu-id="07c97-976">Transport configuration</span></span>

<span data-ttu-id="07c97-977">Con la versione ASP.NET Core 2.1, il trasporto predefinito di Kestrel non si basa più su Libuv, ma su socket gestiti.</span><span class="sxs-lookup"><span data-stu-id="07c97-977">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="07c97-978">Si tratta di una modifica importante per le app ASP.NET 2.0 Core che vengono aggiornate alla versione 2.1, che chiamano <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> e dipendono da uno dei pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-978">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="07c97-979">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (riferimento diretto al pacchetto)</span><span class="sxs-lookup"><span data-stu-id="07c97-979">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="07c97-980">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="07c97-980">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="07c97-981">Per i progetti che richiedono l'uso di libuv:</span><span class="sxs-lookup"><span data-stu-id="07c97-981">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="07c97-982">Aggiungere una dipendenza per il pacchetto [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="07c97-982">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="07c97-983">Chiamare <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="07c97-983">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="07c97-984">Prefissi URL</span><span class="sxs-lookup"><span data-stu-id="07c97-984">URL prefixes</span></span>

<span data-ttu-id="07c97-985">Se si usa `UseUrls`, l'argomento della riga di comando `--urls`, la chiave di configurazione dell'host `urls` o la variabile di ambiente `ASPNETCORE_URLS`, i prefissi URL possono avere uno dei formati seguenti.</span><span class="sxs-lookup"><span data-stu-id="07c97-985">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="07c97-986">Sono validi solo i prefissi URL HTTP.</span><span class="sxs-lookup"><span data-stu-id="07c97-986">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="07c97-987">Kestrel non supporta HTTPS quando la configurazione delle associazioni URL viene eseguita con `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="07c97-987">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="07c97-988">Indirizzo IPv4 con numero di porta</span><span class="sxs-lookup"><span data-stu-id="07c97-988">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="07c97-989">`0.0.0.0` è un caso speciale che esegue l'associazione a tutti gli indirizzi IPv4.</span><span class="sxs-lookup"><span data-stu-id="07c97-989">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="07c97-990">Indirizzo IPv6 con numero di porta</span><span class="sxs-lookup"><span data-stu-id="07c97-990">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="07c97-991">`[::]` è l'equivalente IPv6 di `0.0.0.0` per IPv4.</span><span class="sxs-lookup"><span data-stu-id="07c97-991">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="07c97-992">Nome host con numero di porta</span><span class="sxs-lookup"><span data-stu-id="07c97-992">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="07c97-993">I nomi host, `*` e `+` non sono casi particolari.</span><span class="sxs-lookup"><span data-stu-id="07c97-993">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="07c97-994">Tutto ciò che non è riconosciuto come un indirizzo IP o un elemento `localhost` valido esegue l'associazione a tutti gli IP IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="07c97-994">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="07c97-995">Per associare nomi host diversi ad app ASP.NET Core diverse sulla stessa porta, usare [HTTP.sys](xref:fundamentals/servers/httpsys) o un server proxy inverso come IIS, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="07c97-995">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="07c97-996">Una configurazione che prevede un proxy inverso richiede il [filtro host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="07c97-996">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="07c97-997">Nome host `localhost` con numero di porta o IP di loopback con numero di porta</span><span class="sxs-lookup"><span data-stu-id="07c97-997">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="07c97-998">Se è specificato `localhost`, Kestrel tenta l'associazione alle interfacce di loopback sia IPv4 che IPv6.</span><span class="sxs-lookup"><span data-stu-id="07c97-998">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="07c97-999">Se la porta richiesta è in uso da parte di un altro servizio in una delle due interfacce di loopback, Kestrel non viene avviato.</span><span class="sxs-lookup"><span data-stu-id="07c97-999">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="07c97-1000">Se una delle due interfacce di loopback non è disponibile per qualsiasi altro motivo (in genere perché IPv6 non è supportato), Kestrel registra un avviso.</span><span class="sxs-lookup"><span data-stu-id="07c97-1000">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="07c97-1001">Filtro host</span><span class="sxs-lookup"><span data-stu-id="07c97-1001">Host filtering</span></span>

<span data-ttu-id="07c97-1002">Mentre supporta la configurazione in base ai prefissi, ad esempio `http://example.com:5000`, Kestrel ignora quasi sempre il nome host.</span><span class="sxs-lookup"><span data-stu-id="07c97-1002">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="07c97-1003">L'host `localhost` è un caso speciale usato per l'associazione agli indirizzi di loopback.</span><span class="sxs-lookup"><span data-stu-id="07c97-1003">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="07c97-1004">Qualsiasi host che non sia un indirizzo IP esplicito esegue l'associazione a tutti gli indirizzi IP pubblici.</span><span class="sxs-lookup"><span data-stu-id="07c97-1004">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="07c97-1005">Le intestazioni `Host` non vengono convalidate.</span><span class="sxs-lookup"><span data-stu-id="07c97-1005">`Host` headers aren't validated.</span></span>

<span data-ttu-id="07c97-1006">Come soluzione alternativa, usare il middleware di filtro host.</span><span class="sxs-lookup"><span data-stu-id="07c97-1006">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="07c97-1007">Il middleware di filtro host viene fornito dal pacchetto [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , incluso nel [metapacchetto Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 o 2,2).</span><span class="sxs-lookup"><span data-stu-id="07c97-1007">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="07c97-1008">Il middleware viene aggiunto da <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, che chiama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="07c97-1008">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="07c97-1009">Per impostazione predefinita, il middleware di filtro host è disabilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="07c97-1009">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="07c97-1010">Per abilitare il middleware, definire una `AllowedHosts` chiave in *appSettings. JSON*/*appSettings.\< EnvironmentName>. JSON*.</span><span class="sxs-lookup"><span data-stu-id="07c97-1010">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="07c97-1011">Il valore è un elenco con valori delimitati da punto e virgola di nomi host senza numeri di porta:</span><span class="sxs-lookup"><span data-stu-id="07c97-1011">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="07c97-1012">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="07c97-1012">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="07c97-1013">Il [middleware delle intestazioni inoltrate](xref:host-and-deploy/proxy-load-balancer) include anche un'opzione <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="07c97-1013">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="07c97-1014">Il middleware di intestazioni inoltrate e il middleware di filtro host hanno funzionalità simili per diversi scenari.</span><span class="sxs-lookup"><span data-stu-id="07c97-1014">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="07c97-1015">L'impostazione di `AllowedHosts` con il middleware delle intestazioni inoltrate è appropriato se l'intestazione `Host` non viene mantenuta durante l'inoltro delle richieste con un server proxy inverso o il bilanciamento del carico.</span><span class="sxs-lookup"><span data-stu-id="07c97-1015">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="07c97-1016">L'impostazione di `AllowedHosts` con il middleware del filtro host è appropriata se si usa Kestrel come server perimetrale rivolto al pubblico o se l'intestazione `Host` viene inoltrata direttamente.</span><span class="sxs-lookup"><span data-stu-id="07c97-1016">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="07c97-1017">Per altre informazioni sul middleware delle intestazioni inoltrate, vedere <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="07c97-1017">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="07c97-1018">Svuotamento richieste HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="07c97-1018">HTTP/1.1 request draining</span></span>

<span data-ttu-id="07c97-1019">L'apertura di connessioni HTTP richiede molto tempo.</span><span class="sxs-lookup"><span data-stu-id="07c97-1019">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="07c97-1020">Per HTTPS, è anche a elevato utilizzo di risorse.</span><span class="sxs-lookup"><span data-stu-id="07c97-1020">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="07c97-1021">Pertanto, gheppio tenta di riutilizzare le connessioni per il protocollo HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="07c97-1021">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="07c97-1022">Il corpo della richiesta deve essere completamente utilizzato per consentire il riutilizzo della connessione.</span><span class="sxs-lookup"><span data-stu-id="07c97-1022">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="07c97-1023">L'app non usa sempre il corpo della richiesta, ad esempio `POST` le richieste in cui il server restituisce una risposta di reindirizzamento o 404.</span><span class="sxs-lookup"><span data-stu-id="07c97-1023">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="07c97-1024">Nel caso `POST`-Reindirizzamento:</span><span class="sxs-lookup"><span data-stu-id="07c97-1024">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="07c97-1025">Il client potrebbe avere già inviato parte dei `POST` dati.</span><span class="sxs-lookup"><span data-stu-id="07c97-1025">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="07c97-1026">Il server scrive la risposta 301.</span><span class="sxs-lookup"><span data-stu-id="07c97-1026">The server writes the 301 response.</span></span>
* <span data-ttu-id="07c97-1027">Non è possibile usare la connessione per una nuova richiesta fino `POST` a quando i dati del corpo della richiesta precedente non sono stati completamente letti.</span><span class="sxs-lookup"><span data-stu-id="07c97-1027">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="07c97-1028">Gheppio tenta di svuotare il corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="07c97-1028">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="07c97-1029">Lo svuotamento del corpo della richiesta implica la lettura e l'eliminazione dei dati senza elaborarli.</span><span class="sxs-lookup"><span data-stu-id="07c97-1029">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="07c97-1030">Il processo di svuotamento crea un tradoff tra la possibilità di riutilizzare la connessione e il tempo necessario per svuotare i dati rimanenti:</span><span class="sxs-lookup"><span data-stu-id="07c97-1030">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="07c97-1031">Lo svuotamento ha un timeout di cinque secondi, che non è configurabile.</span><span class="sxs-lookup"><span data-stu-id="07c97-1031">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="07c97-1032">Se tutti i dati specificati dall'intestazione `Content-Length` o `Transfer-Encoding` non sono stati letti prima del timeout, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="07c97-1032">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="07c97-1033">In alcuni casi può essere necessario terminare immediatamente la richiesta, prima o dopo la scrittura della risposta.</span><span class="sxs-lookup"><span data-stu-id="07c97-1033">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="07c97-1034">È ad esempio possibile che i client dispongano di protezioni dati restrittive, pertanto la limitazione dei dati caricati potrebbe avere una priorità.</span><span class="sxs-lookup"><span data-stu-id="07c97-1034">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="07c97-1035">In questi casi, per terminare una richiesta, chiamare [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) da un controller Razor , una pagina o un middleware.</span><span class="sxs-lookup"><span data-stu-id="07c97-1035">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="07c97-1036">Ci sono alcune avvertenze per chiamare `Abort`:</span><span class="sxs-lookup"><span data-stu-id="07c97-1036">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="07c97-1037">La creazione di nuove connessioni può essere lenta e dispendiosa.</span><span class="sxs-lookup"><span data-stu-id="07c97-1037">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="07c97-1038">Non vi è alcuna garanzia che il client abbia letto la risposta prima che la connessione venga chiusa.</span><span class="sxs-lookup"><span data-stu-id="07c97-1038">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="07c97-1039">La `Abort` chiamata a deve essere rara e riservata per i casi di errore gravi, non per errori comuni.</span><span class="sxs-lookup"><span data-stu-id="07c97-1039">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="07c97-1040">Chiamare `Abort` solo quando è necessario risolvere un problema specifico.</span><span class="sxs-lookup"><span data-stu-id="07c97-1040">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="07c97-1041">Ad esempio, chiamare `Abort` se i client malintenzionati `POST` tentano di eseguire dati o quando si verifica un bug nel codice client che genera richieste di grandi dimensioni o numerose.</span><span class="sxs-lookup"><span data-stu-id="07c97-1041">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="07c97-1042">Non chiamare `Abort` le situazioni di errore comuni, ad esempio http 404 (non trovato).</span><span class="sxs-lookup"><span data-stu-id="07c97-1042">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="07c97-1043">Quando si chiama [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) , prima di chiamare `Abort` si garantisce che il server abbia completato la scrittura della risposta.</span><span class="sxs-lookup"><span data-stu-id="07c97-1043">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="07c97-1044">Tuttavia, il comportamento del client non è prevedibile e potrebbe non essere in grado di leggere la risposta prima che la connessione venga interrotta.</span><span class="sxs-lookup"><span data-stu-id="07c97-1044">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="07c97-1045">Questo processo è diverso per HTTP/2 poiché il protocollo supporta l'interruzione dei singoli flussi di richiesta senza chiudere la connessione.</span><span class="sxs-lookup"><span data-stu-id="07c97-1045">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="07c97-1046">Il timeout di svuotamento di cinque secondi non è applicabile.</span><span class="sxs-lookup"><span data-stu-id="07c97-1046">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="07c97-1047">Se dopo il completamento di una risposta sono presenti dati del corpo della richiesta non letti, il server invia un frame RST HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="07c97-1047">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="07c97-1048">I frame di dati del corpo della richiesta aggiuntivi vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="07c97-1048">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="07c97-1049">Se possibile, è preferibile che i client utilizzino l'intestazione della richiesta [expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) e attendano la risposta del server prima di iniziare a inviare il corpo della richiesta.</span><span class="sxs-lookup"><span data-stu-id="07c97-1049">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="07c97-1050">Che fornisce al client la possibilità di esaminare la risposta e interrompere prima di inviare dati non necessari.</span><span class="sxs-lookup"><span data-stu-id="07c97-1050">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="07c97-1051">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="07c97-1051">Additional resources</span></span>

* <span data-ttu-id="07c97-1052">Quando si usano i socket UNIX in Linux, il socket non viene eliminato automaticamente all'arresto dell'app.</span><span class="sxs-lookup"><span data-stu-id="07c97-1052">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="07c97-1053">Per altre informazioni, vedere [questo problema di GitHub](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="07c97-1053">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* <span data-ttu-id="07c97-1054">[RFC 7230: Message Syntax and Routing (Section 5.4: Host)](https://tools.ietf.org/html/rfc7230#section-5.4) (RFC 7230: Sintassi e routing dei messaggi (sezione 5.4: Host))</span><span class="sxs-lookup"><span data-stu-id="07c97-1054">[RFC 7230: Message Syntax and Routing (Section 5.4: Host)](https://tools.ietf.org/html/rfc7230#section-5.4)</span></span>
