---
title: Applicare HTTPS in ASP.NET Core
author: rick-anderson
description: Informazioni su come richiedere HTTPS/TLS in un'app Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
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
uid: security/enforcing-ssl
ms.openlocfilehash: e473da9a7cbd91a601ad4af0c7c02c7f576f348c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051122"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="e1a49-103">Applicare HTTPS in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e1a49-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="e1a49-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e1a49-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e1a49-105">Questo documento illustra come:</span><span class="sxs-lookup"><span data-stu-id="e1a49-105">This document shows how to:</span></span>

* <span data-ttu-id="e1a49-106">Richiedi HTTPS per tutte le richieste.</span><span class="sxs-lookup"><span data-stu-id="e1a49-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="e1a49-107">Reindirizzare tutte le richieste HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e1a49-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="e1a49-108">Nessuna API può impedire a un client di inviare dati sensibili alla prima richiesta.</span><span class="sxs-lookup"><span data-stu-id="e1a49-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="e1a49-109">Progetti API</span><span class="sxs-lookup"><span data-stu-id="e1a49-109">API projects</span></span>
>
> <span data-ttu-id="e1a49-110">**Non** usare [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) su API Web che ricevono informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="e1a49-110">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="e1a49-111">`RequireHttpsAttribute` USA i codici di stato HTTP per reindirizzare i browser da HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e1a49-111">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="e1a49-112">I client API potrebbero non comprendere o obbedire ai reindirizzamenti da HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e1a49-112">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="e1a49-113">Tali client possono inviare informazioni tramite HTTP.</span><span class="sxs-lookup"><span data-stu-id="e1a49-113">Such clients may send information over HTTP.</span></span> <span data-ttu-id="e1a49-114">Le API Web devono:</span><span class="sxs-lookup"><span data-stu-id="e1a49-114">Web APIs should either:</span></span>
>
> * <span data-ttu-id="e1a49-115">Non è in ascolto su HTTP.</span><span class="sxs-lookup"><span data-stu-id="e1a49-115">Not listen on HTTP.</span></span>
> * <span data-ttu-id="e1a49-116">Chiudere la connessione con il codice di stato 400 (richiesta non valida) e non soddisfare la richiesta.</span><span class="sxs-lookup"><span data-stu-id="e1a49-116">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
>
> ## <a name="hsts-and-api-projects"></a><span data-ttu-id="e1a49-117">HSTS e progetti API</span><span class="sxs-lookup"><span data-stu-id="e1a49-117">HSTS and API projects</span></span>
>
> <span data-ttu-id="e1a49-118">I progetti API predefiniti non includono [HSTS](#hsts) perché HSTS è in genere un'istruzione solo del browser.</span><span class="sxs-lookup"><span data-stu-id="e1a49-118">The default API projects don't include [HSTS](#hsts) because HSTS is generally a browser only instruction.</span></span> <span data-ttu-id="e1a49-119">Altri chiamanti, ad esempio le applicazioni per telefoni o desktop, **non** rispettano l'istruzione.</span><span class="sxs-lookup"><span data-stu-id="e1a49-119">Other callers, such as phone or desktop apps, do **not** obey the instruction.</span></span> <span data-ttu-id="e1a49-120">Anche all'interno dei browser, una singola chiamata autenticata a un'API su HTTP presenta rischi per le reti non sicure.</span><span class="sxs-lookup"><span data-stu-id="e1a49-120">Even within browsers, a single authenticated call to an API over HTTP has risks on insecure networks.</span></span> <span data-ttu-id="e1a49-121">L'approccio sicuro consiste nel configurare i progetti API in modo che ascoltino e rispondono solo tramite HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e1a49-121">The secure approach is to configure API projects to only listen to and respond over HTTPS.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="e1a49-122">Progetti API</span><span class="sxs-lookup"><span data-stu-id="e1a49-122">API projects</span></span>
>
> <span data-ttu-id="e1a49-123">**Non** usare [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) su API Web che ricevono informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="e1a49-123">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="e1a49-124">`RequireHttpsAttribute` USA i codici di stato HTTP per reindirizzare i browser da HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e1a49-124">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="e1a49-125">I client API potrebbero non comprendere o obbedire ai reindirizzamenti da HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e1a49-125">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="e1a49-126">Tali client possono inviare informazioni tramite HTTP.</span><span class="sxs-lookup"><span data-stu-id="e1a49-126">Such clients may send information over HTTP.</span></span> <span data-ttu-id="e1a49-127">Le API Web devono:</span><span class="sxs-lookup"><span data-stu-id="e1a49-127">Web APIs should either:</span></span>
>
> * <span data-ttu-id="e1a49-128">Non è in ascolto su HTTP.</span><span class="sxs-lookup"><span data-stu-id="e1a49-128">Not listen on HTTP.</span></span>
> * <span data-ttu-id="e1a49-129">Chiudere la connessione con il codice di stato 400 (richiesta non valida) e non soddisfare la richiesta.</span><span class="sxs-lookup"><span data-stu-id="e1a49-129">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>

::: moniker-end

## <a name="require-https"></a><span data-ttu-id="e1a49-130">Richiedere HTTPS</span><span class="sxs-lookup"><span data-stu-id="e1a49-130">Require HTTPS</span></span>

<span data-ttu-id="e1a49-131">Si consiglia di usare le app Web ASP.NET Core di produzione:</span><span class="sxs-lookup"><span data-stu-id="e1a49-131">We recommend that production ASP.NET Core web apps use:</span></span>

* <span data-ttu-id="e1a49-132">Middleware di reindirizzamento HTTPS ( <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> ) per reindirizzare le richieste HTTP a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e1a49-132">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="e1a49-133">HSTS middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) per inviare intestazioni HTTP Strict Transport Security Protocol (HSTS) ai client.</span><span class="sxs-lookup"><span data-stu-id="e1a49-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="e1a49-134">Le app distribuite in una configurazione di proxy inverso consentono al proxy di gestire la sicurezza della connessione (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="e1a49-134">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="e1a49-135">Se il proxy gestisce anche il reindirizzamento HTTPS, non è necessario usare il middleware di reindirizzamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e1a49-135">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="e1a49-136">Se il server proxy gestisce anche la scrittura delle intestazioni HSTS, ad esempio il [supporto nativo di HSTS in IIS 10,0 (1709) o versioni successive](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support), il middleware HSTS non è richiesto dall'app.</span><span class="sxs-lookup"><span data-stu-id="e1a49-136">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="e1a49-137">Per altre informazioni, vedere [rifiutare esplicitamente HTTPS/HSTS durante la creazione del progetto](#opt-out-of-httpshsts-on-project-creation).</span><span class="sxs-lookup"><span data-stu-id="e1a49-137">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="e1a49-138">UseHttpsRedirection</span><span class="sxs-lookup"><span data-stu-id="e1a49-138">UseHttpsRedirection</span></span>

<span data-ttu-id="e1a49-139">Il codice seguente chiama `UseHttpsRedirection` nella `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="e1a49-139">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

<span data-ttu-id="e1a49-140">Il codice evidenziato precedente:</span><span class="sxs-lookup"><span data-stu-id="e1a49-140">The preceding highlighted code:</span></span>

* <span data-ttu-id="e1a49-141">Usa il valore predefinito [HttpsRedirectionOptions. RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span><span class="sxs-lookup"><span data-stu-id="e1a49-141">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="e1a49-142">Usa il valore predefinito [HttpsRedirectionOptions. HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null), a meno che non venga sottoposto a override dalla `ASPNETCORE_HTTPS_PORT` variabile di ambiente o da [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span><span class="sxs-lookup"><span data-stu-id="e1a49-142">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="e1a49-143">È consigliabile usare reindirizzamenti temporanei anziché reindirizzamenti permanenti.</span><span class="sxs-lookup"><span data-stu-id="e1a49-143">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="e1a49-144">La memorizzazione nella cache dei collegamenti può causare un comportamento instabile negli ambienti di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="e1a49-144">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="e1a49-145">Se si preferisce inviare un codice di stato di reindirizzamento permanente quando l'app si trova in un ambiente non di sviluppo, vedere la sezione [configurare reindirizzamenti permanenti in produzione](#configure-permanent-redirects-in-production) .</span><span class="sxs-lookup"><span data-stu-id="e1a49-145">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="e1a49-146">È consigliabile usare [HSTS](#http-strict-transport-security-protocol-hsts) per segnalare ai client che solo le richieste di risorse protette devono essere inviate all'app (solo nell'ambiente di produzione).</span><span class="sxs-lookup"><span data-stu-id="e1a49-146">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="e1a49-147">Configurazione delle porte</span><span class="sxs-lookup"><span data-stu-id="e1a49-147">Port configuration</span></span>

<span data-ttu-id="e1a49-148">Una porta deve essere disponibile affinché il middleware reindirizzi una richiesta non sicura a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e1a49-148">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="e1a49-149">Se non è disponibile alcuna porta:</span><span class="sxs-lookup"><span data-stu-id="e1a49-149">If no port is available:</span></span>

* <span data-ttu-id="e1a49-150">Il reindirizzamento a HTTPS non viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="e1a49-150">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="e1a49-151">Il middleware registra l'avviso "Impossibile determinare la porta HTTPS per il reindirizzamento".</span><span class="sxs-lookup"><span data-stu-id="e1a49-151">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="e1a49-152">Specificare la porta HTTPS usando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="e1a49-152">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="e1a49-153">Impostare [HttpsRedirectionOptions. HttpsPort](#options).</span><span class="sxs-lookup"><span data-stu-id="e1a49-153">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="e1a49-154">Impostare l' `https_port` [impostazione host](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#https_port):</span><span class="sxs-lookup"><span data-stu-id="e1a49-154">Set the `https_port` [host setting](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#https_port):</span></span>

  * <span data-ttu-id="e1a49-155">Nella configurazione host.</span><span class="sxs-lookup"><span data-stu-id="e1a49-155">In host configuration.</span></span>
  * <span data-ttu-id="e1a49-156">Impostando la `ASPNETCORE_HTTPS_PORT` variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="e1a49-156">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="e1a49-157">Aggiungendo una voce di primo livello in *:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="e1a49-157">By adding a top-level entry in *:::no-loc(appsettings.json):::* :</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/:::no-loc(appsettings.json):::?highlight=2)]

* <span data-ttu-id="e1a49-158">Indicare una porta con lo schema protetto usando la [variabile di ambiente ASPNETCORE_URLS](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#urls).</span><span class="sxs-lookup"><span data-stu-id="e1a49-158">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#urls).</span></span> <span data-ttu-id="e1a49-159">La variabile di ambiente configura il server.</span><span class="sxs-lookup"><span data-stu-id="e1a49-159">The environment variable configures the server.</span></span> <span data-ttu-id="e1a49-160">Il middleware individua indirettamente la porta HTTPS tramite <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> .</span><span class="sxs-lookup"><span data-stu-id="e1a49-160">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="e1a49-161">Questo approccio non funziona nelle distribuzioni di proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="e1a49-161">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* <span data-ttu-id="e1a49-162">Impostare l' `https_port` [impostazione host](xref:fundamentals/host/web-host#https-port):</span><span class="sxs-lookup"><span data-stu-id="e1a49-162">Set the `https_port` [host setting](xref:fundamentals/host/web-host#https-port):</span></span>

  * <span data-ttu-id="e1a49-163">Nella configurazione host.</span><span class="sxs-lookup"><span data-stu-id="e1a49-163">In host configuration.</span></span>
  * <span data-ttu-id="e1a49-164">Impostando la `ASPNETCORE_HTTPS_PORT` variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="e1a49-164">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="e1a49-165">Aggiungendo una voce di primo livello in *:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="e1a49-165">By adding a top-level entry in *:::no-loc(appsettings.json):::* :</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/:::no-loc(appsettings.json):::?highlight=2)]

* <span data-ttu-id="e1a49-166">Indicare una porta con lo schema protetto usando la [variabile di ambiente ASPNETCORE_URLS](xref:fundamentals/host/web-host#server-urls).</span><span class="sxs-lookup"><span data-stu-id="e1a49-166">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](xref:fundamentals/host/web-host#server-urls).</span></span> <span data-ttu-id="e1a49-167">La variabile di ambiente configura il server.</span><span class="sxs-lookup"><span data-stu-id="e1a49-167">The environment variable configures the server.</span></span> <span data-ttu-id="e1a49-168">Il middleware individua indirettamente la porta HTTPS tramite <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> .</span><span class="sxs-lookup"><span data-stu-id="e1a49-168">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="e1a49-169">Questo approccio non funziona nelle distribuzioni di proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="e1a49-169">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

* <span data-ttu-id="e1a49-170">In sviluppo impostare un URL HTTPS in *launchsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="e1a49-170">In development, set an HTTPS URL in *launchsettings.json* .</span></span> <span data-ttu-id="e1a49-171">Abilitare HTTPS quando viene usato IIS Express.</span><span class="sxs-lookup"><span data-stu-id="e1a49-171">Enable HTTPS when IIS Express is used.</span></span>

* <span data-ttu-id="e1a49-172">Configurare un endpoint URL HTTPS per una distribuzione perimetrale pubblica del server [gheppio](xref:fundamentals/servers/kestrel) o del server [HTTP.sys](xref:fundamentals/servers/httpsys) .</span><span class="sxs-lookup"><span data-stu-id="e1a49-172">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) server or [HTTP.sys](xref:fundamentals/servers/httpsys) server.</span></span> <span data-ttu-id="e1a49-173">L'app usa solo **una porta HTTPS** .</span><span class="sxs-lookup"><span data-stu-id="e1a49-173">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="e1a49-174">Il middleware individua la porta tramite <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> .</span><span class="sxs-lookup"><span data-stu-id="e1a49-174">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="e1a49-175">Quando un'app viene eseguita in una configurazione del proxy inverso, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> non è disponibile.</span><span class="sxs-lookup"><span data-stu-id="e1a49-175">When an app is run in a reverse proxy configuration, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> isn't available.</span></span> <span data-ttu-id="e1a49-176">Impostare la porta usando uno degli altri approcci descritti in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="e1a49-176">Set the port using one of the other approaches described in this section.</span></span>

### <a name="edge-deployments"></a><span data-ttu-id="e1a49-177">Distribuzioni Edge</span><span class="sxs-lookup"><span data-stu-id="e1a49-177">Edge deployments</span></span> 

<span data-ttu-id="e1a49-178">Quando gheppio o HTTP.sys viene usato come server perimetrale pubblico, gheppio o HTTP.sys deve essere configurato in modo da restare in ascolto su entrambi:</span><span class="sxs-lookup"><span data-stu-id="e1a49-178">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="e1a49-179">La porta protetta in cui viene reindirizzato il client, in genere 443 in produzione e 5001 in fase di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="e1a49-179">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="e1a49-180">Porta non sicura, in genere 80 in produzione e 5000 in fase di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="e1a49-180">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="e1a49-181">Per consentire all'app di ricevere una richiesta non sicura e reindirizzare il client alla porta protetta, la porta non protetta deve essere accessibile dal client.</span><span class="sxs-lookup"><span data-stu-id="e1a49-181">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="e1a49-182">Per ulteriori informazioni, vedere [configurazione dell'endpoint gheppio](xref:fundamentals/servers/kestrel#endpoint-configuration) o <xref:fundamentals/servers/httpsys> .</span><span class="sxs-lookup"><span data-stu-id="e1a49-182">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="e1a49-183">Scenari di distribuzione</span><span class="sxs-lookup"><span data-stu-id="e1a49-183">Deployment scenarios</span></span>

<span data-ttu-id="e1a49-184">Qualsiasi firewall tra il client e il server deve avere anche porte di comunicazione aperte per il traffico.</span><span class="sxs-lookup"><span data-stu-id="e1a49-184">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="e1a49-185">Se le richieste vengono inviate in una configurazione del proxy inverso, usare il [middleware delle intestazioni inoltro](xref:host-and-deploy/proxy-load-balancer) prima di chiamare il middleware di reindirizzamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e1a49-185">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="e1a49-186">Il middleware intestazioni inoltri Aggiorna `Request.Scheme` , usando l' `X-Forwarded-Proto` intestazione.</span><span class="sxs-lookup"><span data-stu-id="e1a49-186">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="e1a49-187">Il middleware consente l'uso corretto degli URI di reindirizzamento e di altri criteri di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="e1a49-187">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="e1a49-188">Quando il middleware delle intestazioni inoltrate non viene usato, l'app back-end potrebbe non ricevere lo schema corretto e finire in un ciclo di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="e1a49-188">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="e1a49-189">Un messaggio di errore dell'utente finale comune è che si sono verificati troppi reindirizzamenti.</span><span class="sxs-lookup"><span data-stu-id="e1a49-189">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="e1a49-190">Quando si esegue la distribuzione nel servizio app Azure, seguire le istruzioni riportate in [esercitazione: associare un certificato SSL personalizzato esistente ad app Web di Azure](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="e1a49-190">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="e1a49-191">Opzioni</span><span class="sxs-lookup"><span data-stu-id="e1a49-191">Options</span></span>

<span data-ttu-id="e1a49-192">Il codice evidenziato seguente chiama [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) per configurare le opzioni del middleware:</span><span class="sxs-lookup"><span data-stu-id="e1a49-192">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


<span data-ttu-id="e1a49-193">`AddHttpsRedirection`La chiamata a è necessaria solo per modificare i valori di `HttpsPort` o `RedirectStatusCode` .</span><span class="sxs-lookup"><span data-stu-id="e1a49-193">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="e1a49-194">Il codice evidenziato precedente:</span><span class="sxs-lookup"><span data-stu-id="e1a49-194">The preceding highlighted code:</span></span>

* <span data-ttu-id="e1a49-195">Imposta [HttpsRedirectionOptions. RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) su <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect> , che rappresenta il valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="e1a49-195">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="e1a49-196">Usare i campi della <xref:Microsoft.AspNetCore.Http.StatusCodes> classe per le assegnazioni a `RedirectStatusCode` .</span><span class="sxs-lookup"><span data-stu-id="e1a49-196">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="e1a49-197">Imposta la porta HTTPS su 5001.</span><span class="sxs-lookup"><span data-stu-id="e1a49-197">Sets the HTTPS port to 5001.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="e1a49-198">Configurare reindirizzamenti permanenti nell'ambiente di produzione</span><span class="sxs-lookup"><span data-stu-id="e1a49-198">Configure permanent redirects in production</span></span>

<span data-ttu-id="e1a49-199">Per impostazione predefinita, il middleware Invia un [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) con tutti i reindirizzamenti.</span><span class="sxs-lookup"><span data-stu-id="e1a49-199">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="e1a49-200">Se si preferisce inviare un codice di stato di reindirizzamento permanente quando l'app si trova in un ambiente non di sviluppo, eseguire il wrapping della configurazione delle opzioni middleware in un controllo condizionale per un ambiente non di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="e1a49-200">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e1a49-201">Quando si configurano i servizi in *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="e1a49-201">When configuring services in *Startup.cs* :</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="e1a49-202">Quando si configurano i servizi in *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="e1a49-202">When configuring services in *Startup.cs* :</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="e1a49-203">Approccio alternativo del middleware di reindirizzamento HTTPS</span><span class="sxs-lookup"><span data-stu-id="e1a49-203">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="e1a49-204">Un'alternativa all'utilizzo del middleware di reindirizzamento HTTPS ( `UseHttpsRedirection` ) consiste nell'utilizzare il middleware di riscrittura URL ( `AddRedirectToHttps` ).</span><span class="sxs-lookup"><span data-stu-id="e1a49-204">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="e1a49-205">`AddRedirectToHttps` consente inoltre di impostare il codice di stato e la porta quando viene eseguito il reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="e1a49-205">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="e1a49-206">Per altre informazioni, vedere [middleware riscrittura URL](xref:fundamentals/url-rewriting).</span><span class="sxs-lookup"><span data-stu-id="e1a49-206">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="e1a49-207">Quando si reindirizza a HTTPS senza la necessità di altre regole di reindirizzamento, è consigliabile usare il middleware di reindirizzamento HTTPS ( `UseHttpsRedirection` ) descritto in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="e1a49-207">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="e1a49-208">Protocollo di sicurezza del trasporto HTTP Strict (HSTS)</span><span class="sxs-lookup"><span data-stu-id="e1a49-208">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="e1a49-209">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [http Strict Transport Security (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) è un miglioramento della sicurezza esplicito che viene specificato da un'app Web tramite l'uso di un'intestazione di risposta.</span><span class="sxs-lookup"><span data-stu-id="e1a49-209">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="e1a49-210">Quando un [browser che supporta HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) riceve questa intestazione:</span><span class="sxs-lookup"><span data-stu-id="e1a49-210">When a [browser that supports HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) receives this header:</span></span>

* <span data-ttu-id="e1a49-211">Il browser archivia la configurazione per il dominio che impedisce l'invio di comunicazioni tramite HTTP.</span><span class="sxs-lookup"><span data-stu-id="e1a49-211">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="e1a49-212">Il browser forza tutte le comunicazioni su HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e1a49-212">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="e1a49-213">Il browser impedisce all'utente di usare certificati non attendibili o non validi.</span><span class="sxs-lookup"><span data-stu-id="e1a49-213">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="e1a49-214">Il browser Disabilita i prompt che consentono a un utente di considerare temporaneamente attendibile tale certificato.</span><span class="sxs-lookup"><span data-stu-id="e1a49-214">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="e1a49-215">Poiché HSTS viene applicato dal client, presenta alcune limitazioni:</span><span class="sxs-lookup"><span data-stu-id="e1a49-215">Because HSTS is enforced by the client, it has some limitations:</span></span>

* <span data-ttu-id="e1a49-216">Il client deve supportare HSTS.</span><span class="sxs-lookup"><span data-stu-id="e1a49-216">The client must support HSTS.</span></span>
* <span data-ttu-id="e1a49-217">HSTS richiede almeno una richiesta HTTPS corretta per stabilire il criterio HSTS.</span><span class="sxs-lookup"><span data-stu-id="e1a49-217">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="e1a49-218">L'applicazione deve controllare ogni richiesta HTTP e reindirizzare o rifiutare la richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="e1a49-218">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="e1a49-219">ASP.NET Core 2,1 e versioni successive implementa HSTS con il `UseHsts` metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="e1a49-219">ASP.NET Core 2.1 and later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="e1a49-220">Il codice seguente chiama `UseHsts` quando l'app non è in [modalità di sviluppo](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="e1a49-220">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

<span data-ttu-id="e1a49-221">`UseHsts` non è consigliato per lo sviluppo perché le impostazioni HSTS sono altamente memorizzabili nella cache dai browser.</span><span class="sxs-lookup"><span data-stu-id="e1a49-221">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="e1a49-222">Per impostazione predefinita, `UseHsts` esclude l'indirizzo di loopback locale.</span><span class="sxs-lookup"><span data-stu-id="e1a49-222">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="e1a49-223">Per gli ambienti di produzione che implementano HTTPS per la prima volta, impostare il valore iniziale [HstsOptions. MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) su un valore ridotto utilizzando uno dei <xref:System.TimeSpan> metodi.</span><span class="sxs-lookup"><span data-stu-id="e1a49-223">For production environments that are implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="e1a49-224">Impostare il valore da ore a non più di un singolo giorno nel caso in cui sia necessario ripristinare l'infrastruttura HTTPS su HTTP.</span><span class="sxs-lookup"><span data-stu-id="e1a49-224">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="e1a49-225">Una volta che si è certi della sostenibilità della configurazione HTTPS, aumentare il valore di HSTS `max-age` . un valore comunemente usato è un anno.</span><span class="sxs-lookup"><span data-stu-id="e1a49-225">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS `max-age` value; a commonly used value is one year.</span></span>

<span data-ttu-id="e1a49-226">Il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="e1a49-226">The following code:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* <span data-ttu-id="e1a49-227">Imposta il parametro preload dell' `Strict-Transport-Security` intestazione.</span><span class="sxs-lookup"><span data-stu-id="e1a49-227">Sets the preload parameter of the `Strict-Transport-Security` header.</span></span> <span data-ttu-id="e1a49-228">Il precaricamento non fa parte della [specifica RFC HSTS](https://tools.ietf.org/html/rfc6797), ma è supportato dai Web browser per precaricare i siti HSTS in una nuova installazione.</span><span class="sxs-lookup"><span data-stu-id="e1a49-228">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="e1a49-229">Per altre informazioni, vedere [https://hstspreload.org/](https://hstspreload.org/).</span><span class="sxs-lookup"><span data-stu-id="e1a49-229">For more information, see [https://hstspreload.org/](https://hstspreload.org/).</span></span>
* <span data-ttu-id="e1a49-230">Abilita [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), che applica il criterio HSTS ai sottodomini host.</span><span class="sxs-lookup"><span data-stu-id="e1a49-230">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="e1a49-231">Imposta in modo esplicito il `max-age` parametro dell' `Strict-Transport-Security` intestazione su 60 giorni.</span><span class="sxs-lookup"><span data-stu-id="e1a49-231">Explicitly sets the `max-age` parameter of the `Strict-Transport-Security` header to 60 days.</span></span> <span data-ttu-id="e1a49-232">Se non è impostato, il valore predefinito è 30 giorni.</span><span class="sxs-lookup"><span data-stu-id="e1a49-232">If not set, defaults to 30 days.</span></span> <span data-ttu-id="e1a49-233">Per ulteriori informazioni, vedere la [direttiva max-age](https://tools.ietf.org/html/rfc6797#section-6.1.1).</span><span class="sxs-lookup"><span data-stu-id="e1a49-233">For more information, see the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1).</span></span>
* <span data-ttu-id="e1a49-234">Aggiunge `example.com` all'elenco di host da escludere.</span><span class="sxs-lookup"><span data-stu-id="e1a49-234">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="e1a49-235">`UseHsts` esclude gli host di loopback seguenti:</span><span class="sxs-lookup"><span data-stu-id="e1a49-235">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="e1a49-236">`localhost` : Indirizzo di loopback IPv4.</span><span class="sxs-lookup"><span data-stu-id="e1a49-236">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="e1a49-237">`127.0.0.1` : Indirizzo di loopback IPv4.</span><span class="sxs-lookup"><span data-stu-id="e1a49-237">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="e1a49-238">`[::1]` : Indirizzo di loopback IPv6.</span><span class="sxs-lookup"><span data-stu-id="e1a49-238">`[::1]` : The IPv6 loopback address.</span></span>

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="e1a49-239">Rifiutare esplicitamente il protocollo HTTPS/HSTS durante la creazione del progetto</span><span class="sxs-lookup"><span data-stu-id="e1a49-239">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="e1a49-240">In alcuni scenari di servizi back-end in cui la sicurezza della connessione viene gestita al bordo pubblico della rete, non è necessario configurare la sicurezza della connessione in ogni nodo.</span><span class="sxs-lookup"><span data-stu-id="e1a49-240">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="e1a49-241">Le app Web generate dai modelli in Visual Studio o dal comando [DotNet New](/dotnet/core/tools/dotnet-new) abilitano il [reindirizzamento HTTPS](#require-https) e [HSTS](#http-strict-transport-security-protocol-hsts).</span><span class="sxs-lookup"><span data-stu-id="e1a49-241">Web apps that are generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="e1a49-242">Per le distribuzioni che non richiedono questi scenari, è possibile rifiutare esplicitamente HTTPS/HSTS quando l'app viene creata dal modello.</span><span class="sxs-lookup"><span data-stu-id="e1a49-242">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="e1a49-243">Per rifiutare esplicitamente il protocollo HTTPS/HSTS:</span><span class="sxs-lookup"><span data-stu-id="e1a49-243">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e1a49-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e1a49-244">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="e1a49-245">Deselezionare la casella **di controllo Configura per HTTPS** .</span><span class="sxs-lookup"><span data-stu-id="e1a49-245">Uncheck the **Configure for HTTPS** check box.</span></span>

::: moniker range=">= aspnetcore-3.0"

![Finestra di dialogo nuova applicazione Web ASP.NET Core che mostra la casella di controllo Configura per HTTPS deselezionata.](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![Finestra di dialogo nuova applicazione Web ASP.NET Core che mostra la casella di controllo Configura per HTTPS deselezionata.](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="e1a49-248">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="e1a49-248">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="e1a49-249">Usare l'opzione `--no-https`.</span><span class="sxs-lookup"><span data-stu-id="e1a49-249">Use the `--no-https` option.</span></span> <span data-ttu-id="e1a49-250">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="e1a49-250">For example</span></span>

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="e1a49-251">Considera attendibile il certificato di sviluppo HTTPS ASP.NET Core in Windows e macOS</span><span class="sxs-lookup"><span data-stu-id="e1a49-251">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="e1a49-252">Il .NET Core SDK include un certificato di sviluppo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e1a49-252">The .NET Core SDK includes an HTTPS development certificate.</span></span> <span data-ttu-id="e1a49-253">Il certificato viene installato come parte dell'esperienza di prima esecuzione.</span><span class="sxs-lookup"><span data-stu-id="e1a49-253">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="e1a49-254">Ad esempio, `dotnet --info` produce una variante dell'output seguente:</span><span class="sxs-lookup"><span data-stu-id="e1a49-254">For example, `dotnet --info` produces a variation of the following output:</span></span>

```
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="e1a49-255">L'installazione di .NET Core SDK include l'installazione del certificato di sviluppo HTTPS ASP.NET Core nell'archivio certificati utente locale.</span><span class="sxs-lookup"><span data-stu-id="e1a49-255">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="e1a49-256">Il certificato è stato installato, ma non è attendibile.</span><span class="sxs-lookup"><span data-stu-id="e1a49-256">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="e1a49-257">Per considerare attendibile il certificato, eseguire il passaggio monouso per eseguire lo `dev-certs` strumento DotNet:</span><span class="sxs-lookup"><span data-stu-id="e1a49-257">To trust the certificate, perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="e1a49-258">Il comando seguente consente di visualizzare informazioni della Guida sullo strumento `dev-certs`:</span><span class="sxs-lookup"><span data-stu-id="e1a49-258">The following command provides help on the `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="e1a49-259">Come configurare un certificato per sviluppatori per Docker</span><span class="sxs-lookup"><span data-stu-id="e1a49-259">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="e1a49-260">Vedere [il problema in GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/6199).</span><span class="sxs-lookup"><span data-stu-id="e1a49-260">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6199).</span></span>

<a name="ssl-linux"></a>

## <a name="trust-https-certificate-on-linux"></a><span data-ttu-id="e1a49-261">Attendibilità del certificato HTTPS in Linux</span><span class="sxs-lookup"><span data-stu-id="e1a49-261">Trust HTTPS certificate on Linux</span></span>

<!-- Instructions to be updated by engineering team after 5.0 RTM. -->

<span data-ttu-id="e1a49-262">Per istruzioni su Linux, vedere la documentazione relativa alla distribuzione.</span><span class="sxs-lookup"><span data-stu-id="e1a49-262">For instructions on Linux, refer to the distribution documentation.</span></span>

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a><span data-ttu-id="e1a49-263">Considera attendibile il certificato HTTPS del sottosistema Windows per Linux</span><span class="sxs-lookup"><span data-stu-id="e1a49-263">Trust HTTPS certificate from Windows Subsystem for Linux</span></span>

<span data-ttu-id="e1a49-264">Il sottosistema Windows per Linux (WSL) genera un certificato autofirmato HTTPS. Per configurare l'archivio certificati di Windows per considerare attendibile il certificato WSL:</span><span class="sxs-lookup"><span data-stu-id="e1a49-264">The Windows Subsystem for Linux (WSL) generates an HTTPS self-signed cert. To configure the Windows certificate store to trust the WSL certificate:</span></span>

* <span data-ttu-id="e1a49-265">Eseguire il comando seguente per esportare il certificato generato da WSL:</span><span class="sxs-lookup"><span data-stu-id="e1a49-265">Run the following command to export the WSL-generated certificate:</span></span>

  ```
  dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>
  ```
* <span data-ttu-id="e1a49-266">In una finestra di WSL eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="e1a49-266">In a WSL window, run the following command:</span></span>

  ```
    ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" 
    ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx
    dotnet watch run
  ```

  <span data-ttu-id="e1a49-267">Il comando precedente imposta le variabili di ambiente in modo che Linux usi il certificato attendibile di Windows.</span><span class="sxs-lookup"><span data-stu-id="e1a49-267">The preceding command sets the environment variables so Linux uses the Windows trusted certificate.</span></span>

## <a name="troubleshoot-certificate-problems"></a><span data-ttu-id="e1a49-268">Risolvere i problemi relativi ai certificati</span><span class="sxs-lookup"><span data-stu-id="e1a49-268">Troubleshoot certificate problems</span></span>

<span data-ttu-id="e1a49-269">Questa sezione fornisce assistenza quando il certificato di sviluppo ASP.NET Core HTTPS è stato [installato e considerato attendibile](#trust), ma sono ancora presenti avvisi del browser che non sono attendibili per il certificato.</span><span class="sxs-lookup"><span data-stu-id="e1a49-269">This section provides help when the ASP.NET Core HTTPS development certificate has been [installed and trusted](#trust), but you still have browser warnings that the certificate is not trusted.</span></span> <span data-ttu-id="e1a49-270">Il certificato di sviluppo HTTPS ASP.NET Core viene usato da [gheppio](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="e1a49-270">The ASP.NET Core HTTPS development certificate is used by [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

### <a name="all-platforms---certificate-not-trusted"></a><span data-ttu-id="e1a49-271">Tutte le piattaforme-certificato non attendibile</span><span class="sxs-lookup"><span data-stu-id="e1a49-271">All platforms - certificate not trusted</span></span>

<span data-ttu-id="e1a49-272">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="e1a49-272">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="e1a49-273">Chiude tutte le istanze del browser aperte.</span><span class="sxs-lookup"><span data-stu-id="e1a49-273">Close any browser instances open.</span></span> <span data-ttu-id="e1a49-274">Aprire una nuova finestra del browser per l'app.</span><span class="sxs-lookup"><span data-stu-id="e1a49-274">Open a new browser window to app.</span></span> <span data-ttu-id="e1a49-275">Il trust tra certificati viene memorizzato nella cache dai browser.</span><span class="sxs-lookup"><span data-stu-id="e1a49-275">Certificate trust is cached by browsers.</span></span>

<span data-ttu-id="e1a49-276">I comandi precedenti risolvono la maggior parte dei problemi di attendibilità del browser.</span><span class="sxs-lookup"><span data-stu-id="e1a49-276">The preceding commands solve most browser trust issues.</span></span> <span data-ttu-id="e1a49-277">Se il browser non considera ancora attendibile il certificato, attenersi ai suggerimenti specifici della piattaforma seguenti.</span><span class="sxs-lookup"><span data-stu-id="e1a49-277">If the browser is still not trusting the certificate, follow the platform-specific suggestions that follow.</span></span>

### <a name="docker---certificate-not-trusted"></a><span data-ttu-id="e1a49-278">Docker: certificato non attendibile</span><span class="sxs-lookup"><span data-stu-id="e1a49-278">Docker - certificate not trusted</span></span>

* <span data-ttu-id="e1a49-279">Eliminare la cartella *C:\Users \{ utente} \AppData\Roaming\ASP.NET\Https*</span><span class="sxs-lookup"><span data-stu-id="e1a49-279">Delete the *C:\Users\{USER}\AppData\Roaming\ASP.NET\Https* folder.</span></span>
* <span data-ttu-id="e1a49-280">Pulire la soluzione.</span><span class="sxs-lookup"><span data-stu-id="e1a49-280">Clean the solution.</span></span> <span data-ttu-id="e1a49-281">Eliminare le cartelle *bin* e *obj* .</span><span class="sxs-lookup"><span data-stu-id="e1a49-281">Delete the *bin* and *obj* folders.</span></span>
* <span data-ttu-id="e1a49-282">Riavviare lo strumento di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="e1a49-282">Restart the development tool.</span></span> <span data-ttu-id="e1a49-283">Ad esempio, Visual Studio, Visual Studio Code o Visual Studio per Mac.</span><span class="sxs-lookup"><span data-stu-id="e1a49-283">For example, Visual Studio, Visual Studio Code, or Visual Studio for Mac.</span></span>

### <a name="windows---certificate-not-trusted"></a><span data-ttu-id="e1a49-284">Windows: certificato non attendibile</span><span class="sxs-lookup"><span data-stu-id="e1a49-284">Windows - certificate not trusted</span></span>

* <span data-ttu-id="e1a49-285">Controllare i certificati nell'archivio certificati.</span><span class="sxs-lookup"><span data-stu-id="e1a49-285">Check the certificates in the certificate store.</span></span> <span data-ttu-id="e1a49-286">Deve essere presente un `localhost` certificato con il `ASP.NET Core HTTPS development certificate` nome descrittivo in `Current User > Personal > Certificates` e `Current User > Trusted root certification authorities > Certificates`</span><span class="sxs-lookup"><span data-stu-id="e1a49-286">There should be a `localhost` certificate with the `ASP.NET Core HTTPS development certificate` friendly name both under `Current User > Personal > Certificates` and `Current User > Trusted root certification authorities > Certificates`</span></span>
* <span data-ttu-id="e1a49-287">Rimuovere tutti i certificati trovati dalle autorità di certificazione radice personali e attendibili.</span><span class="sxs-lookup"><span data-stu-id="e1a49-287">Remove all the found certificates from both Personal and Trusted root certification authorities.</span></span> <span data-ttu-id="e1a49-288">Non **rimuovere il** certificato localhost IIS Express.</span><span class="sxs-lookup"><span data-stu-id="e1a49-288">Do **not** remove the IIS Express localhost certificate.</span></span>
* <span data-ttu-id="e1a49-289">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="e1a49-289">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="e1a49-290">Chiude tutte le istanze del browser aperte.</span><span class="sxs-lookup"><span data-stu-id="e1a49-290">Close any browser instances open.</span></span> <span data-ttu-id="e1a49-291">Aprire una nuova finestra del browser per l'app.</span><span class="sxs-lookup"><span data-stu-id="e1a49-291">Open a new browser window to app.</span></span>

### <a name="os-x---certificate-not-trusted"></a><span data-ttu-id="e1a49-292">OS X-certificato non attendibile</span><span class="sxs-lookup"><span data-stu-id="e1a49-292">OS X - certificate not trusted</span></span>

* <span data-ttu-id="e1a49-293">Aprire l'accesso keychain.</span><span class="sxs-lookup"><span data-stu-id="e1a49-293">Open KeyChain Access.</span></span>
* <span data-ttu-id="e1a49-294">Selezionare il keychain di sistema.</span><span class="sxs-lookup"><span data-stu-id="e1a49-294">Select the System keychain.</span></span>
* <span data-ttu-id="e1a49-295">Verificare la presenza di un certificato localhost.</span><span class="sxs-lookup"><span data-stu-id="e1a49-295">Check for the presence of a localhost certificate.</span></span>
* <span data-ttu-id="e1a49-296">Verificare che contenga un `+` simbolo sull'icona per indicare che è attendibile per tutti gli utenti.</span><span class="sxs-lookup"><span data-stu-id="e1a49-296">Check that it contains a `+` symbol on the icon to indicate it's trusted for all users.</span></span>
* <span data-ttu-id="e1a49-297">Rimuovere il certificato dal keychain di sistema.</span><span class="sxs-lookup"><span data-stu-id="e1a49-297">Remove the certificate from the system keychain.</span></span>
* <span data-ttu-id="e1a49-298">Eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="e1a49-298">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="e1a49-299">Chiude tutte le istanze del browser aperte.</span><span class="sxs-lookup"><span data-stu-id="e1a49-299">Close any browser instances open.</span></span> <span data-ttu-id="e1a49-300">Aprire una nuova finestra del browser per l'app.</span><span class="sxs-lookup"><span data-stu-id="e1a49-300">Open a new browser window to app.</span></span>

<span data-ttu-id="e1a49-301">Per la risoluzione dei problemi relativi ai certificati con Visual Studio, vedere [errore HTTPS con IIS Express (DotNet/AspNetCore #16892)](https://github.com/dotnet/AspNetCore/issues/16892) .</span><span class="sxs-lookup"><span data-stu-id="e1a49-301">See [HTTPS Error using IIS Express (dotnet/AspNetCore #16892)](https://github.com/dotnet/AspNetCore/issues/16892) for troubleshooting certificate issues with Visual Studio.</span></span>

### <a name="iis-express-ssl-certificate-used-with-visual-studio"></a><span data-ttu-id="e1a49-302">IIS Express certificato SSL usato con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e1a49-302">IIS Express SSL certificate used with Visual Studio</span></span>

<span data-ttu-id="e1a49-303">Per risolvere i problemi relativi al certificato IIS Express, selezionare **Ripristina** dal programma di installazione di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e1a49-303">To fix problems with the IIS Express certificate, select **Repair** from the Visual Studio installer.</span></span> <span data-ttu-id="e1a49-304">Per altre informazioni, vedere [questo problema di GitHub](https://github.com/dotnet/aspnetcore/issues/16892).</span><span class="sxs-lookup"><span data-stu-id="e1a49-304">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/16892).</span></span>

## <a name="additional-information"></a><span data-ttu-id="e1a49-305">Informazioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="e1a49-305">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="e1a49-306">Ospitare ASP.NET Core in Linux con Apache: configurazione HTTPS</span><span class="sxs-lookup"><span data-stu-id="e1a49-306">Host ASP.NET Core on Linux with Apache: HTTPS configuration</span></span>](xref:host-and-deploy/linux-apache#https-configuration)
* [<span data-ttu-id="e1a49-307">ASP.NET Core host in Linux con Nginx: configurazione HTTPS</span><span class="sxs-lookup"><span data-stu-id="e1a49-307">Host ASP.NET Core on Linux with Nginx: HTTPS configuration</span></span>](xref:host-and-deploy/linux-nginx#https-configuration)
* [<span data-ttu-id="e1a49-308">Come configurare SSL in IIS</span><span class="sxs-lookup"><span data-stu-id="e1a49-308">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="e1a49-309">Supporto del browser OWASP HSTS</span><span class="sxs-lookup"><span data-stu-id="e1a49-309">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)