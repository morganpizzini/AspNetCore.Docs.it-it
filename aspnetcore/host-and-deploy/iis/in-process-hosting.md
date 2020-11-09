---
title: Hosting in-process con IIS e ASP.NET Core
author: rick-anderson
description: Informazioni sull'hosting in-process con IIS e il modulo ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: 47dc6f65f398ecce45c563c359dfde6e17d1dc1b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058480"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="2d29c-103">Hosting in-process con IIS e ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2d29c-103">In-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="2d29c-104">L'hosting in-process esegue un'app ASP.NET Core nello stesso processo del processo di lavoro IIS.</span><span class="sxs-lookup"><span data-stu-id="2d29c-104">In-process hosting runs an ASP.NET Core app in the same process as its IIS worker process.</span></span> <span data-ttu-id="2d29c-105">L'hosting in-process offre prestazioni migliori rispetto all'hosting out-of-process perché le richieste non vengono inserite in proxy sulla scheda di loopback, un'interfaccia di rete che restituisce il traffico di rete in uscita allo stesso computer.</span><span class="sxs-lookup"><span data-stu-id="2d29c-105">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="2d29c-106">Il diagramma seguente illustra la relazione tra IIS, il modulo ASP.NET Core e un'app ospitata in-process:</span><span class="sxs-lookup"><span data-stu-id="2d29c-106">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![Modulo ASP.NET Core nello scenario di hosting in-process](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a><span data-ttu-id="2d29c-108">Abilita hosting in-process</span><span class="sxs-lookup"><span data-stu-id="2d29c-108">Enable in-process hosting</span></span>

<span data-ttu-id="2d29c-109">Dal ASP.NET Core 3,0, l'hosting in-process è stato abilitato per impostazione predefinita per tutte le app distribuite in IIS.</span><span class="sxs-lookup"><span data-stu-id="2d29c-109">Since ASP.NET Core 3.0, in-process hosting has been enabled by default for all app deployed to IIS.</span></span>

<span data-ttu-id="2d29c-110">Per configurare in modo esplicito un'app per l'hosting in-process, impostare il valore della `<AspNetCoreHostingModel>` proprietà su `InProcess` nel file di progetto ( `.csproj` ):</span><span class="sxs-lookup"><span data-stu-id="2d29c-110">To explicitly configure an app for in-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `InProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a><span data-ttu-id="2d29c-111">Architettura generale</span><span class="sxs-lookup"><span data-stu-id="2d29c-111">General architecture</span></span>

<span data-ttu-id="2d29c-112">Il flusso generale di una richiesta è il seguente:</span><span class="sxs-lookup"><span data-stu-id="2d29c-112">The general flow of a request is as follows:</span></span>

1. <span data-ttu-id="2d29c-113">Una richiesta arriva dal Web al driver HTTP.sys in modalità kernel.</span><span class="sxs-lookup"><span data-stu-id="2d29c-113">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="2d29c-114">Il driver instrada la richiesta nativa IIS sulla porta configurata per il sito Web, in genere 80 (HTTP) o 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2d29c-114">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="2d29c-115">Il modulo ASP.NET Core riceve la richiesta nativa e la passa al server HTTP IIS ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="2d29c-115">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="2d29c-116">Il server HTTP di IIS è un'implementazione di server in-process per IIS che converte la richiesta da nativa a gestita.</span><span class="sxs-lookup"><span data-stu-id="2d29c-116">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="2d29c-117">Dopo l'elaborazione della richiesta da parte del server HTTP IIS:</span><span class="sxs-lookup"><span data-stu-id="2d29c-117">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="2d29c-118">La richiesta viene inviata alla pipeline del middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2d29c-118">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="2d29c-119">La pipeline middleware gestisce la richiesta e la passa come istanza di `HttpContext` alla logica dell'app.</span><span class="sxs-lookup"><span data-stu-id="2d29c-119">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="2d29c-120">La risposta dell'app viene restituita a IIS tramite il server HTTP di IIS.</span><span class="sxs-lookup"><span data-stu-id="2d29c-120">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="2d29c-121">IIS invia la risposta al client che ha avviato la richiesta.</span><span class="sxs-lookup"><span data-stu-id="2d29c-121">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="2d29c-122">`CreateDefaultBuilder` aggiunge un' <xref:Microsoft.AspNetCore.Hosting.Server.IServer> istanza di chiamando il <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> metodo per avviare il [CoreCLR](/dotnet/standard/glossary#coreclr) e ospitare l'app all'interno del processo di lavoro IIS ( `w3wp.exe` o `iisexpress.exe` ).</span><span class="sxs-lookup"><span data-stu-id="2d29c-122">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (`w3wp.exe` or `iisexpress.exe`).</span></span> <span data-ttu-id="2d29c-123">I test delle prestazioni indicano che l'hosting di un'app .NET Core in-process offre una velocità effettiva delle richieste significativamente superiore rispetto all'hosting dell'app out-of-process o all'inoltro delle richieste a [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="2d29c-123">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="2d29c-124">Le app pubblicate come un singolo file eseguibile non possono essere caricate dal modello di hosting in-process.</span><span class="sxs-lookup"><span data-stu-id="2d29c-124">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="2d29c-125">Configurazione dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="2d29c-125">Application configuration</span></span>

<span data-ttu-id="2d29c-126">Per configurare le opzioni di IIS includere una configurazione del servizio per <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span><span class="sxs-lookup"><span data-stu-id="2d29c-126">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="2d29c-127">L'esempio seguente disabilita AutomaticAuthentication:</span><span class="sxs-lookup"><span data-stu-id="2d29c-127">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="2d29c-128">Opzione</span><span class="sxs-lookup"><span data-stu-id="2d29c-128">Option</span></span> | <span data-ttu-id="2d29c-129">Predefinito</span><span class="sxs-lookup"><span data-stu-id="2d29c-129">Default</span></span> | <span data-ttu-id="2d29c-130">Impostazione</span><span class="sxs-lookup"><span data-stu-id="2d29c-130">Setting</span></span> |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | <span data-ttu-id="2d29c-131">Se `true`, IIS Server imposta l'utente `HttpContext.User` autenticato tramite l'[autenticazione di Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="2d29c-131">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="2d29c-132">Se `false`, il server fornisce solo un'identità per `HttpContext.User` e risponde alle richieste esplicite di `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="2d29c-132">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="2d29c-133">Per il funzionamento di `AutomaticAuthentication` l’autenticazione di Windows deve essere abilitata in IIS.</span><span class="sxs-lookup"><span data-stu-id="2d29c-133">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="2d29c-134">Per altre informazioni, vedere [Autenticazione di Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="2d29c-134">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName` | `null` | <span data-ttu-id="2d29c-135">Imposta il nome visualizzato dagli utenti nelle pagine di accesso.</span><span class="sxs-lookup"><span data-stu-id="2d29c-135">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO` | `false` | <span data-ttu-id="2d29c-136">Indica se l'i/O sincrono è consentito per `HttpContext.Request` e `HttpContext.Response` .</span><span class="sxs-lookup"><span data-stu-id="2d29c-136">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize` | `30000000` | <span data-ttu-id="2d29c-137">Ottiene o imposta la dimensione massima del corpo della richiesta per `HttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="2d29c-137">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="2d29c-138">Notare che IIS stesso include il limite `maxAllowedContentLength`, che verrà elaborato prima del set `MaxRequestBodySize` in `IISServerOptions`.</span><span class="sxs-lookup"><span data-stu-id="2d29c-138">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="2d29c-139">La modifica di `MaxRequestBodySize` non influisce su `maxAllowedContentLength`.</span><span class="sxs-lookup"><span data-stu-id="2d29c-139">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="2d29c-140">Per aumentare `maxAllowedContentLength` , aggiungere una voce in `web.config` per impostare `maxAllowedContentLength` su un valore più alto.</span><span class="sxs-lookup"><span data-stu-id="2d29c-140">To increase `maxAllowedContentLength`, add an entry in the `web.config` to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="2d29c-141">Per ulteriori informazioni, vedere [Configurazione](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span><span class="sxs-lookup"><span data-stu-id="2d29c-141">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a><span data-ttu-id="2d29c-142">Differenze tra l'hosting in-process e out-of-process</span><span class="sxs-lookup"><span data-stu-id="2d29c-142">Differences between in-process and out-of-process hosting</span></span>

<span data-ttu-id="2d29c-143">In caso di hosting in-process, vengono applicate le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="2d29c-143">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="2d29c-144">Viene usato un server HTTP di IIS (`IISHttpServer`) al posto del server [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="2d29c-144">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="2d29c-145">Per in-process, le [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) chiamate <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> a:</span><span class="sxs-lookup"><span data-stu-id="2d29c-145">For in-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> to:</span></span>

  * <span data-ttu-id="2d29c-146">Registrare `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="2d29c-146">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="2d29c-147">Configurare la porta e il percorso di base su cui il server deve eseguire l'ascolto in caso di esecuzione dietro il modulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2d29c-147">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="2d29c-148">Configurare l'host per l'acquisizione degli errori di avvio.</span><span class="sxs-lookup"><span data-stu-id="2d29c-148">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="2d29c-149">L' [ `requestTimeout` attributo](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) non si applica all'hosting in-process.</span><span class="sxs-lookup"><span data-stu-id="2d29c-149">The [`requestTimeout` attribute](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="2d29c-150">Non è supportata la condivisione di un pool di app tra le app.</span><span class="sxs-lookup"><span data-stu-id="2d29c-150">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="2d29c-151">Usare un pool di app per ogni app.</span><span class="sxs-lookup"><span data-stu-id="2d29c-151">Use one app pool per app.</span></span>

* <span data-ttu-id="2d29c-152">L'architettura, vale a dire il numero di bit dell'app, e il runtime installato (x64 o x86) devono corrispondere all'architettura del pool di app.</span><span class="sxs-lookup"><span data-stu-id="2d29c-152">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span> <span data-ttu-id="2d29c-153">Ad esempio, per le app pubblicate per 32 bit (x86) è necessario che siano abilitate 32 bit per i pool di applicazioni IIS.</span><span class="sxs-lookup"><span data-stu-id="2d29c-153">For example, apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="2d29c-154">Per ulteriori informazioni, vedere la sezione [creare il sito IIS](xref:tutorials/publish-to-iis#create-the-iis-site) .</span><span class="sxs-lookup"><span data-stu-id="2d29c-154">For more information, see the [Create the IIS site](xref:tutorials/publish-to-iis#create-the-iis-site) section.</span></span>

* <span data-ttu-id="2d29c-155">Le disconnessioni del client vengono rilevate.</span><span class="sxs-lookup"><span data-stu-id="2d29c-155">Client disconnects are detected.</span></span> <span data-ttu-id="2d29c-156">Il [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) token di annullamento viene annullato quando il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="2d29c-156">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="2d29c-157">In caso di hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> non viene chiamato internamente per inizializzare un utente.</span><span class="sxs-lookup"><span data-stu-id="2d29c-157">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> isn't called internally to initialize a user.</span></span> <span data-ttu-id="2d29c-158">Pertanto, un'implementazione di <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usate per trasformare le attestazioni dopo ogni autenticazione non viene attivata per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="2d29c-158">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="2d29c-159">Quando si trasformano le attestazioni con un'implementazione di <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, chiamare <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> per aggiungere i servizi di autenticazione:</span><span class="sxs-lookup"><span data-stu-id="2d29c-159">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
* <span data-ttu-id="2d29c-160">Le [distribuzioni di pacchetti Web (file singolo)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) non sono supportate.</span><span class="sxs-lookup"><span data-stu-id="2d29c-160">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>
