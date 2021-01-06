---
title: Hosting out-of-process con IIS e ASP.NET Core
author: rick-anderson
description: Informazioni sull'hosting out-of-process con IIS e il modulo ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
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
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 78ead27bd1373237d1c0a48655d73a41a0a72279
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060417"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="ede57-103">Hosting out-of-process con IIS e ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ede57-103">Out-of-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="ede57-104">Poiché le app ASP.NET Core vengono eseguite in un processo separato dal processo di lavoro IIS, il modulo ASP.NET Core gestisce la gestione dei processi.</span><span class="sxs-lookup"><span data-stu-id="ede57-104">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="ede57-105">Il modulo avvia il processo per l'app ASP.NET Core quando arriva la prima richiesta e riavvia l'app se viene arrestata o si arresta in modo anomalo.</span><span class="sxs-lookup"><span data-stu-id="ede57-105">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="ede57-106">Si tratta essenzialmente dello stesso comportamento delle app eseguite in-process e gestite dal [servizio Attivazione processo Windows](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="ede57-106">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="ede57-107">Il diagramma seguente illustra la relazione tra IIS, il modulo ASP.NET Core e un'app ospitata out-of-process:</span><span class="sxs-lookup"><span data-stu-id="ede57-107">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Modulo ASP.NET Core nello scenario di hosting out-of-process](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="ede57-109">Le richieste arrivano dal Web al driver HTTP.sys in modalità kernel.</span><span class="sxs-lookup"><span data-stu-id="ede57-109">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="ede57-110">Il driver instrada le richieste a IIS sulla porta configurata del sito Web.</span><span class="sxs-lookup"><span data-stu-id="ede57-110">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="ede57-111">La porta configurata è generalmente 80 (HTTP) o 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ede57-111">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="ede57-112">Il modulo Invia le richieste a gheppio su una porta casuale per l'app.</span><span class="sxs-lookup"><span data-stu-id="ede57-112">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="ede57-113">La porta casuale non è 80 o 443.</span><span class="sxs-lookup"><span data-stu-id="ede57-113">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="ede57-114">Il modulo ASP.NET Core specifica la porta tramite una variabile di ambiente all'avvio.</span><span class="sxs-lookup"><span data-stu-id="ede57-114">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="ede57-115">L' <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> estensione configura il server per l'ascolto `http://localhost:{PORT}` .</span><span class="sxs-lookup"><span data-stu-id="ede57-115">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="ede57-116">Vengono eseguiti controlli aggiuntivi e le richieste che non provengono dal modulo vengono rifiutate.</span><span class="sxs-lookup"><span data-stu-id="ede57-116">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="ede57-117">Il modulo non supporta l'invio HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ede57-117">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="ede57-118">Le richieste vengono inviate tramite HTTP anche se ricevute da IIS su HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ede57-118">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="ede57-119">Quando il gheppio preleva la richiesta dal modulo, la richiesta viene trasmessa alla pipeline del middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ede57-119">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="ede57-120">La pipeline middleware gestisce la richiesta e la passa come istanza di `HttpContext` alla logica dell'app.</span><span class="sxs-lookup"><span data-stu-id="ede57-120">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="ede57-121">Il middleware aggiunto dall'integrazione di IIS aggiorna lo schema, l'IP remoto e il percorso di base all'account per l'inoltro della richiesta a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ede57-121">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="ede57-122">La risposta dell'app viene passata di nuovo a IIS, che la inoltra al client HTTP che ha avviato la richiesta.</span><span class="sxs-lookup"><span data-stu-id="ede57-122">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="ede57-123">Per indicazioni sulla configurazione del modulo ASP.NET Core, vedere <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="ede57-123">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="ede57-124">Per altre informazioni sull'hosting, vedere [Hosting in ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="ede57-124">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="ede57-125">Configurazione dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="ede57-125">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="ede57-126">Abilitare i componenti IISIntegration</span><span class="sxs-lookup"><span data-stu-id="ede57-126">Enable the IISIntegration components</span></span>

<span data-ttu-id="ede57-127">Quando si compila un host in `CreateHostBuilder` ( `Program.cs` ), chiamare <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> per abilitare l'integrazione con IIS:</span><span class="sxs-lookup"><span data-stu-id="ede57-127">When building a host in `CreateHostBuilder` (`Program.cs`), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="ede57-128">Per altre informazioni su `CreateDefaultBuilder`, vedere <xref:fundamentals/host/generic-host#default-builder-settings>.</span><span class="sxs-lookup"><span data-stu-id="ede57-128">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>


<span data-ttu-id="ede57-129">**Modello di hosting out-of-process**</span><span class="sxs-lookup"><span data-stu-id="ede57-129">**Out-of-process hosting model**</span></span>

<span data-ttu-id="ede57-130">Per configurare le opzioni di IIS includere una configurazione del servizio per <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span><span class="sxs-lookup"><span data-stu-id="ede57-130">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="ede57-131">Nell'esempio seguente si impedisce all'app di popolare `HttpContext.Connection.ClientCertificate`:</span><span class="sxs-lookup"><span data-stu-id="ede57-131">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="ede57-132">Opzione</span><span class="sxs-lookup"><span data-stu-id="ede57-132">Option</span></span>                         | <span data-ttu-id="ede57-133">Predefinito</span><span class="sxs-lookup"><span data-stu-id="ede57-133">Default</span></span> | <span data-ttu-id="ede57-134">Impostazione</span><span class="sxs-lookup"><span data-stu-id="ede57-134">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="ede57-135">Se `true`, il [middleware di integrazione IIS](#enable-the-iisintegration-components) imposta `HttpContext.User` autenticato tramite l'[autenticazione di Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="ede57-135">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="ede57-136">Se `false`, il middleware fornisce solo un'identità per `HttpContext.User` e risponde solo alle richieste esplicite di `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="ede57-136">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="ede57-137">Per il funzionamento di `AutomaticAuthentication` l’autenticazione di Windows deve essere abilitata in IIS.</span><span class="sxs-lookup"><span data-stu-id="ede57-137">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="ede57-138">Per altre informazioni, vedere l'argomento [Autenticazione di Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="ede57-138">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="ede57-139">Imposta il nome visualizzato dagli utenti nelle pagine di accesso.</span><span class="sxs-lookup"><span data-stu-id="ede57-139">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="ede57-140">Se è `true` ed è presente l’intestazione della richiesta `MS-ASPNETCORE-CLIENTCERT`, `HttpContext.Connection.ClientCertificate` viene popolato.</span><span class="sxs-lookup"><span data-stu-id="ede57-140">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |


### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="ede57-141">Scenari con server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="ede57-141">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="ede57-142">Il [middleware di integrazione IIS](#enable-the-iisintegration-components) e il modulo ASP.NET Core sono configurati per l'invio di:</span><span class="sxs-lookup"><span data-stu-id="ede57-142">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="ede57-143">Schema (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ede57-143">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="ede57-144">Indirizzo IP remoto da cui ha avuto origine la richiesta.</span><span class="sxs-lookup"><span data-stu-id="ede57-144">Remote IP address where the request originated.</span></span>

<span data-ttu-id="ede57-145">Il [middleware di integrazione di IIS](#enable-the-iisintegration-components) configura il middleware delle intestazioni con inoltri.</span><span class="sxs-lookup"><span data-stu-id="ede57-145">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="ede57-146">Potrebbero essere necessari interventi di configurazione aggiuntivi per le app ospitate dietro ulteriori server proxy e servizi di bilanciamento del carico.</span><span class="sxs-lookup"><span data-stu-id="ede57-146">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="ede57-147">Per altre informazioni, vedere [Configurare ASP.NET Core per l'utilizzo di server proxy e servizi di bilanciamento del carico](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="ede57-147">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>


### <a name="out-of-process-hosting-model"></a><span data-ttu-id="ede57-148">Modello di hosting out-of-process</span><span class="sxs-lookup"><span data-stu-id="ede57-148">Out-of-process hosting model</span></span>

<span data-ttu-id="ede57-149">Per configurare un'app per l'hosting out-of-process, impostare il valore della `<AspNetCoreHostingModel>` proprietà su `OutOfProcess` nel file di progetto ( `.csproj` ):</span><span class="sxs-lookup"><span data-stu-id="ede57-149">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="ede57-150">L'hosting in-process è impostato su `InProcess` , che corrisponde al valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="ede57-150">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="ede57-151">Il valore di non `<AspNetCoreHostingModel>` fa distinzione tra maiuscole e minuscole, pertanto `inprocess` e `outofprocess` sono valori validi.</span><span class="sxs-lookup"><span data-stu-id="ede57-151">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="ede57-152">Viene usato il server [Kestrel](xref:fundamentals/servers/kestrel) al posto di un server HTTP di IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="ede57-152">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="ede57-153">Per out-of-process, le [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) chiamate <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> a:</span><span class="sxs-lookup"><span data-stu-id="ede57-153">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> to:</span></span>

* <span data-ttu-id="ede57-154">Configurare la porta e il percorso di base su cui il server deve eseguire l'ascolto in caso di esecuzione dietro il modulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ede57-154">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="ede57-155">Configurare l'host per l'acquisizione degli errori di avvio.</span><span class="sxs-lookup"><span data-stu-id="ede57-155">Configure the host to capture startup errors.</span></span>

### <a name="process-name"></a><span data-ttu-id="ede57-156">Nome del processo</span><span class="sxs-lookup"><span data-stu-id="ede57-156">Process name</span></span>

<span data-ttu-id="ede57-157">`Process.GetCurrentProcess().ProcessName` dichiara `w3wp`/`iisexpress` (In-Process) o `dotnet` (out-of-process).</span><span class="sxs-lookup"><span data-stu-id="ede57-157">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="ede57-158">Molti moduli nativi, ad esempio l'autenticazione di Windows, rimangono attivi.</span><span class="sxs-lookup"><span data-stu-id="ede57-158">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="ede57-159">Per altre informazioni sui moduli IIS attivi con il modulo ASP.NET Core, vedere <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="ede57-159">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="ede57-160">Il modulo ASP.NET Core può anche:</span><span class="sxs-lookup"><span data-stu-id="ede57-160">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="ede57-161">Impostare variabili di ambiente per il processo di lavoro.</span><span class="sxs-lookup"><span data-stu-id="ede57-161">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="ede57-162">Registrare output stdout in una risorsa di archiviazione di file per la risoluzione di problemi di avvio.</span><span class="sxs-lookup"><span data-stu-id="ede57-162">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="ede57-163">Inoltrare token di autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="ede57-163">Forward Windows authentication tokens.</span></span>
