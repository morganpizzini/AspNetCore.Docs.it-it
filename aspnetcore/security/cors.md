---
title: Abilitare le richieste tra le origini (CORS) in ASP.NET Core
author: rick-anderson
description: Informazioni su come CORS come standard per consentire o rifiutare le richieste tra origini in un'app ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
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
uid: security/cors
ms.openlocfilehash: 03008f40fc1c4b323535d08a1bb4c4007bc145f7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060820"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="b7a75-103">Abilitare le richieste tra le origini (CORS) in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b7a75-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b7a75-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="b7a75-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="b7a75-105">Questo articolo illustra come abilitare CORS in un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b7a75-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="b7a75-106">La sicurezza del browser impedisce a una pagina Web di eseguire richieste a un dominio diverso da quello che ha gestito la pagina Web.</span><span class="sxs-lookup"><span data-stu-id="b7a75-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="b7a75-107">Questa restrizione è detta *criterio della stessa origine* .</span><span class="sxs-lookup"><span data-stu-id="b7a75-107">This restriction is called the *same-origin policy* .</span></span> <span data-ttu-id="b7a75-108">Il criterio della stessa origine impedisce a un sito dannoso di leggere dati sensibili da un altro sito.</span><span class="sxs-lookup"><span data-stu-id="b7a75-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="b7a75-109">In alcuni casi potrebbe essere necessario consentire ad altri siti di effettuare richieste tra le origini per l'app.</span><span class="sxs-lookup"><span data-stu-id="b7a75-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="b7a75-110">Per altre informazioni, vedere l' [articolo relativo a Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="b7a75-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="b7a75-111">[Condivisione risorse tra le origini](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="b7a75-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="b7a75-112">È uno standard W3C che consente a un server di ridurre i criteri della stessa origine.</span><span class="sxs-lookup"><span data-stu-id="b7a75-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="b7a75-113">**Non** è una funzionalità di sicurezza, CORS rilassa la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="b7a75-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="b7a75-114">Un'API non è più sicura consentendo CORS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="b7a75-115">Per ulteriori informazioni, vedere [funzionamento di CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="b7a75-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="b7a75-116">Consente a un server di consentire in modo esplicito alcune richieste tra origini rifiutando altre.</span><span class="sxs-lookup"><span data-stu-id="b7a75-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="b7a75-117">È più sicuro e flessibile rispetto alle tecniche precedenti, ad esempio [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="b7a75-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="b7a75-118">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b7a75-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="b7a75-119">Stessa origine</span><span class="sxs-lookup"><span data-stu-id="b7a75-119">Same origin</span></span>

<span data-ttu-id="b7a75-120">Due URL hanno la stessa origine se hanno schemi, host e porte identici ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="b7a75-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="b7a75-121">Questi due URL hanno la stessa origine:</span><span class="sxs-lookup"><span data-stu-id="b7a75-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="b7a75-122">Questi URL hanno origini diverse da quelle dei due URL precedenti:</span><span class="sxs-lookup"><span data-stu-id="b7a75-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="b7a75-123">`https://example.net`: Dominio diverso</span><span class="sxs-lookup"><span data-stu-id="b7a75-123">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="b7a75-124">`https://www.example.com/foo.html`: Sottodominio diverso</span><span class="sxs-lookup"><span data-stu-id="b7a75-124">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="b7a75-125">`http://example.com/foo.html`: Schema diverso</span><span class="sxs-lookup"><span data-stu-id="b7a75-125">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="b7a75-126">`https://example.com:9000/foo.html`: Porta diversa</span><span class="sxs-lookup"><span data-stu-id="b7a75-126">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="b7a75-127">Abilitare CORS</span><span class="sxs-lookup"><span data-stu-id="b7a75-127">Enable CORS</span></span>

<span data-ttu-id="b7a75-128">Sono disponibili tre modi per abilitare CORS:</span><span class="sxs-lookup"><span data-stu-id="b7a75-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="b7a75-129">Nel middleware usando un criterio [denominato](#np) o un [criterio predefinito](#dp).</span><span class="sxs-lookup"><span data-stu-id="b7a75-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="b7a75-130">Uso del [routing degli endpoint](#ecors).</span><span class="sxs-lookup"><span data-stu-id="b7a75-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="b7a75-131">Con l'attributo [[EnableCors]](#attr) .</span><span class="sxs-lookup"><span data-stu-id="b7a75-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="b7a75-132">L'utilizzo dell'attributo [[EnableCors]](#attr) con un criterio denominato fornisce il controllo più raffinato per limitare gli endpoint che supportano CORS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

> [!WARNING]
> <span data-ttu-id="b7a75-133"><xref:Owin.CorsExtensions.UseCors%2A> deve essere chiamato prima di <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> quando si usa `UseResponseCaching` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-133"><xref:Owin.CorsExtensions.UseCors%2A> must be called before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> when using `UseResponseCaching`.</span></span>

<span data-ttu-id="b7a75-134">Ogni approccio è descritto in dettaglio nelle sezioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="b7a75-134">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="b7a75-135">CORS con criteri e middleware denominati</span><span class="sxs-lookup"><span data-stu-id="b7a75-135">CORS with named policy and middleware</span></span>

<span data-ttu-id="b7a75-136">Il middleware CORS gestisce le richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="b7a75-136">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="b7a75-137">Il codice seguente applica un criterio CORS a tutti gli endpoint dell'app con le origini specificate:</span><span class="sxs-lookup"><span data-stu-id="b7a75-137">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,32)]

<span data-ttu-id="b7a75-138">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="b7a75-138">The preceding code:</span></span>

* <span data-ttu-id="b7a75-139">Imposta il nome dei criteri su `_myAllowSpecificOrigins` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-139">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="b7a75-140">Il nome del criterio è arbitrario.</span><span class="sxs-lookup"><span data-stu-id="b7a75-140">The policy name is arbitrary.</span></span>
* <span data-ttu-id="b7a75-141">Chiama il <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodo di estensione e specifica il  `_myAllowSpecificOrigins` criterio CORS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-141">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="b7a75-142">`UseCors` aggiunge il middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-142">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="b7a75-143">La chiamata a `UseCors` deve essere posizionata dopo `UseRouting` , ma prima di `UseAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-143">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="b7a75-144">Per ulteriori informazioni, vedere l' [ordine del middleware](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="b7a75-144">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="b7a75-145">Chiama <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> con un' [espressione lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="b7a75-145">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="b7a75-146">L'espressione lambda accetta un <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> oggetto.</span><span class="sxs-lookup"><span data-stu-id="b7a75-146">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="b7a75-147">Le [Opzioni di configurazione](#cors-policy-options), ad esempio `WithOrigins` , sono descritte più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="b7a75-147">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="b7a75-148">Abilita il `_myAllowSpecificOrigins` criterio CORS per tutti gli endpoint del controller.</span><span class="sxs-lookup"><span data-stu-id="b7a75-148">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="b7a75-149">Vedere [endpoint routing](#ecors) per applicare un criterio CORS a endpoint specifici.</span><span class="sxs-lookup"><span data-stu-id="b7a75-149">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>
* <span data-ttu-id="b7a75-150">Quando si usa il [middleware di memorizzazione nella cache delle risposte](xref:performance/caching/middleware), chiamare <xref:Owin.CorsExtensions.UseCors%2A> prima <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> .</span><span class="sxs-lookup"><span data-stu-id="b7a75-150">When using [Response Caching Middleware](xref:performance/caching/middleware), call <xref:Owin.CorsExtensions.UseCors%2A> before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A>.</span></span>

<span data-ttu-id="b7a75-151">Con il routing dell'endpoint è **necessario** configurare il middleware CORS per l'esecuzione tra le chiamate a `UseRouting` e `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-151">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="b7a75-152">Vedere [test CORS](#testc) per istruzioni sul test di codice simile al codice precedente.</span><span class="sxs-lookup"><span data-stu-id="b7a75-152">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="b7a75-153">La <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chiamata al metodo aggiunge i servizi CORS al contenitore del servizio dell'app:</span><span class="sxs-lookup"><span data-stu-id="b7a75-153">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="b7a75-154">Per ulteriori informazioni, vedere [Opzioni dei criteri CORS](#cpo) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="b7a75-154">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="b7a75-155">I <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> metodi possono essere concatenati, come illustrato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="b7a75-155">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="b7a75-156">Nota: l'URL specificato **non** deve contenere una barra finale ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="b7a75-156">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="b7a75-157">Se l'URL termina con `/` , il confronto restituisce `false` e non viene restituita alcuna intestazione.</span><span class="sxs-lookup"><span data-stu-id="b7a75-157">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="b7a75-158">CORS con criteri e middleware predefiniti</span><span class="sxs-lookup"><span data-stu-id="b7a75-158">CORS with default policy and middleware</span></span>

<span data-ttu-id="b7a75-159">Il codice evidenziato seguente Abilita i criteri predefiniti di CORS:</span><span class="sxs-lookup"><span data-stu-id="b7a75-159">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="b7a75-160">Il codice precedente applica i criteri predefiniti di CORS a tutti gli endpoint del controller.</span><span class="sxs-lookup"><span data-stu-id="b7a75-160">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="b7a75-161">Abilitare cors con routing degli endpoint</span><span class="sxs-lookup"><span data-stu-id="b7a75-161">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="b7a75-162">L'abilitazione di CORS in base all'endpoint `RequireCors` attualmente in **uso non** supporta [le richieste preliminari automatiche](#apf).</span><span class="sxs-lookup"><span data-stu-id="b7a75-162">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="b7a75-163">Per ulteriori informazioni, vedere [questo problema di GitHub](https://github.com/dotnet/aspnetcore/issues/20709) e [testare cors con endpoint routing e [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="b7a75-163">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="b7a75-164">Con il routing degli endpoint, è possibile abilitare CORS in base all'endpoint usando il <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set di metodi di estensione:</span><span class="sxs-lookup"><span data-stu-id="b7a75-164">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="b7a75-165">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="b7a75-165">In the preceding code:</span></span>

* <span data-ttu-id="b7a75-166">`app.UseCors` Abilita il middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-166">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="b7a75-167">Poiché un criterio predefinito non è stato configurato, `app.UseCors()` da solo non Abilita CORS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-167">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="b7a75-168">Gli `/echo` endpoint del controller e consentono richieste tra le origini usando i criteri specificati.</span><span class="sxs-lookup"><span data-stu-id="b7a75-168">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="b7a75-169">Gli `/echo2` :::no-loc(Razor)::: endpoint delle pagine e non **not** consentono richieste tra le origini perché non è stato specificato alcun criterio predefinito.</span><span class="sxs-lookup"><span data-stu-id="b7a75-169">The `/echo2` and :::no-loc(Razor)::: Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="b7a75-170">L'attributo [[DisableCors]](#dc) non **Disabilita CORS**  che è stato abilitato dal routing di endpoint con `RequireCors` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-170">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="b7a75-171">Per istruzioni sul test del codice simile a quello precedente, vedere [test CORS with endpoint routing e [HttpOptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="b7a75-171">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="b7a75-172">Abilita CORS con attributi</span><span class="sxs-lookup"><span data-stu-id="b7a75-172">Enable CORS with attributes</span></span>

<span data-ttu-id="b7a75-173">L'abilitazione di CORS con l'attributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) e l'applicazione di un criterio denominato solo agli endpoint che richiedono CORS fornisce il controllo migliore.</span><span class="sxs-lookup"><span data-stu-id="b7a75-173">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="b7a75-174">L'attributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) rappresenta un'alternativa all'applicazione di CORS a livello globale.</span><span class="sxs-lookup"><span data-stu-id="b7a75-174">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="b7a75-175">L' `[EnableCors]` attributo Abilita CORS per gli endpoint selezionati, anziché tutti gli endpoint:</span><span class="sxs-lookup"><span data-stu-id="b7a75-175">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="b7a75-176">`[EnableCors]` Specifica i criteri predefiniti.</span><span class="sxs-lookup"><span data-stu-id="b7a75-176">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="b7a75-177">`[EnableCors("{Policy String}")]` Specifica un criterio denominato.</span><span class="sxs-lookup"><span data-stu-id="b7a75-177">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="b7a75-178">L' `[EnableCors]` attributo può essere applicato a:</span><span class="sxs-lookup"><span data-stu-id="b7a75-178">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="b7a75-179">:::no-loc(Razor)::: Pagina `PageModel`</span><span class="sxs-lookup"><span data-stu-id="b7a75-179">:::no-loc(Razor)::: Page `PageModel`</span></span>
* <span data-ttu-id="b7a75-180">Controller</span><span class="sxs-lookup"><span data-stu-id="b7a75-180">Controller</span></span>
* <span data-ttu-id="b7a75-181">Metodo di azione del controller</span><span class="sxs-lookup"><span data-stu-id="b7a75-181">Controller action method</span></span>

<span data-ttu-id="b7a75-182">È possibile applicare criteri diversi ai controller, ai modelli di pagina o ai metodi di azione con l' `[EnableCors]` attributo.</span><span class="sxs-lookup"><span data-stu-id="b7a75-182">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="b7a75-183">Quando l' `[EnableCors]` attributo viene applicato a un controller, a un modello di pagina o a un metodo di azione e CORS è abilitato nel middleware, vengono applicati **entrambi** i criteri.</span><span class="sxs-lookup"><span data-stu-id="b7a75-183">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="b7a75-184">**È consigliabile evitare di combinare i criteri. Usare l'** `[EnableCors]` **attributo o il middleware, non entrambi nella stessa app.**</span><span class="sxs-lookup"><span data-stu-id="b7a75-184">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="b7a75-185">Il codice seguente applica un criterio diverso a ogni metodo:</span><span class="sxs-lookup"><span data-stu-id="b7a75-185">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="b7a75-186">Il codice seguente crea due criteri CORS:</span><span class="sxs-lookup"><span data-stu-id="b7a75-186">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="b7a75-187">Per il controllo più raffinato della limitazione delle richieste CORS:</span><span class="sxs-lookup"><span data-stu-id="b7a75-187">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="b7a75-188">Usare `[EnableCors("MyPolicy")]` con un criterio denominato.</span><span class="sxs-lookup"><span data-stu-id="b7a75-188">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="b7a75-189">Non definire un criterio predefinito.</span><span class="sxs-lookup"><span data-stu-id="b7a75-189">Don't define a default policy.</span></span>
* <span data-ttu-id="b7a75-190">Non usare il [routing degli endpoint](#ecors).</span><span class="sxs-lookup"><span data-stu-id="b7a75-190">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="b7a75-191">Il codice nella sezione successiva soddisfa l'elenco precedente.</span><span class="sxs-lookup"><span data-stu-id="b7a75-191">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="b7a75-192">Vedere [test CORS](#testc) per istruzioni sul test di codice simile al codice precedente.</span><span class="sxs-lookup"><span data-stu-id="b7a75-192">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="b7a75-193">Disabilitare CORS</span><span class="sxs-lookup"><span data-stu-id="b7a75-193">Disable CORS</span></span>

<span data-ttu-id="b7a75-194">L'attributo [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) non **Disabilita CORS**  che è stato abilitato dal routing dell' [endpoint](#ecors).</span><span class="sxs-lookup"><span data-stu-id="b7a75-194">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="b7a75-195">Il codice seguente definisce i criteri CORS `"MyPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="b7a75-195">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="b7a75-196">Il codice seguente Disabilita CORS per l' `GetValues2` azione:</span><span class="sxs-lookup"><span data-stu-id="b7a75-196">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="b7a75-197">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="b7a75-197">The preceding code:</span></span>

* <span data-ttu-id="b7a75-198">Non Abilita CORS con [routing endpoint](#ecors).</span><span class="sxs-lookup"><span data-stu-id="b7a75-198">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="b7a75-199">Non definisce un [criterio CORS predefinito](#dp).</span><span class="sxs-lookup"><span data-stu-id="b7a75-199">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="b7a75-200">USA [[EnableCors ("criteri")]](#attr) per abilitare il `"MyPolicy"` criterio CORS per il controller.</span><span class="sxs-lookup"><span data-stu-id="b7a75-200">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="b7a75-201">Disabilita CORS per il `GetValues2` metodo.</span><span class="sxs-lookup"><span data-stu-id="b7a75-201">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="b7a75-202">Vedere [test CORS](#testc) per istruzioni sul test del codice precedente.</span><span class="sxs-lookup"><span data-stu-id="b7a75-202">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="b7a75-203">Opzioni dei criteri di CORS</span><span class="sxs-lookup"><span data-stu-id="b7a75-203">CORS policy options</span></span>

<span data-ttu-id="b7a75-204">In questa sezione vengono descritte le varie opzioni che è possibile impostare in un criterio CORS:</span><span class="sxs-lookup"><span data-stu-id="b7a75-204">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="b7a75-205">Imposta le origini consentite</span><span class="sxs-lookup"><span data-stu-id="b7a75-205">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="b7a75-206">Impostare i metodi HTTP consentiti</span><span class="sxs-lookup"><span data-stu-id="b7a75-206">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="b7a75-207">Impostare le intestazioni della richiesta consentita</span><span class="sxs-lookup"><span data-stu-id="b7a75-207">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="b7a75-208">Impostare le intestazioni di risposta esposte</span><span class="sxs-lookup"><span data-stu-id="b7a75-208">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="b7a75-209">Credenziali nelle richieste tra le origini</span><span class="sxs-lookup"><span data-stu-id="b7a75-209">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="b7a75-210">Imposta la data di scadenza preliminare</span><span class="sxs-lookup"><span data-stu-id="b7a75-210">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="b7a75-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> viene chiamato in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b7a75-212">Per alcune opzioni, può essere utile leggere prima la sezione [come funziona CORS](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="b7a75-212">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="b7a75-213">Imposta le origini consentite</span><span class="sxs-lookup"><span data-stu-id="b7a75-213">Set the allowed origins</span></span>

<span data-ttu-id="b7a75-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Consente le richieste CORS da tutte le origini con qualsiasi schema ( `http` o `https` ).</span><span class="sxs-lookup"><span data-stu-id="b7a75-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="b7a75-215">`AllowAnyOrigin` non è sicuro perché *qualsiasi sito Web* può effettuare richieste tra origini per l'app.</span><span class="sxs-lookup"><span data-stu-id="b7a75-215">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="b7a75-216">La definizione di `AllowAnyOrigin` e `AllowCredentials` è una configurazione non sicura e può comportare la falsificazione della richiesta tra siti.</span><span class="sxs-lookup"><span data-stu-id="b7a75-216">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="b7a75-217">Il servizio CORS restituisce una risposta CORS non valida quando un'app è configurata con entrambi i metodi.</span><span class="sxs-lookup"><span data-stu-id="b7a75-217">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="b7a75-218">`AllowAnyOrigin` influiscono sulle richieste preliminari e sull' `Access-Control-Allow-Origin` intestazione.</span><span class="sxs-lookup"><span data-stu-id="b7a75-218">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="b7a75-219">Per ulteriori informazioni, vedere la sezione [preflight requests](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="b7a75-219">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="b7a75-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Imposta la <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> proprietà dei criteri in modo che sia una funzione che consente alle origini di corrispondere a un dominio con caratteri jolly configurato durante la valutazione se l'origine è consentita.</span><span class="sxs-lookup"><span data-stu-id="b7a75-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="b7a75-221">Impostare i metodi HTTP consentiti</span><span class="sxs-lookup"><span data-stu-id="b7a75-221">Set the allowed HTTP methods</span></span>

<span data-ttu-id="b7a75-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="b7a75-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="b7a75-223">Consente qualsiasi metodo HTTP:</span><span class="sxs-lookup"><span data-stu-id="b7a75-223">Allows any HTTP method:</span></span>
* <span data-ttu-id="b7a75-224">Influiscono sulle richieste preliminari e sull' `Access-Control-Allow-Methods` intestazione.</span><span class="sxs-lookup"><span data-stu-id="b7a75-224">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="b7a75-225">Per ulteriori informazioni, vedere la sezione [preflight requests](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="b7a75-225">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="b7a75-226">Impostare le intestazioni della richiesta consentita</span><span class="sxs-lookup"><span data-stu-id="b7a75-226">Set the allowed request headers</span></span>

<span data-ttu-id="b7a75-227">Per consentire l'invio di intestazioni specifiche in una richiesta CORS, denominate [intestazioni richiesta autore](https://xhr.spec.whatwg.org/#request), chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> e specificare le intestazioni consentite:</span><span class="sxs-lookup"><span data-stu-id="b7a75-227">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="b7a75-228">Per consentire tutte le [intestazioni di richiesta di autore](https://www.w3.org/TR/cors/#author-request-headers), chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="b7a75-228">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="b7a75-229">`AllowAnyHeader` influiscono sulle richieste preliminari e sull'intestazione [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) .</span><span class="sxs-lookup"><span data-stu-id="b7a75-229">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="b7a75-230">Per ulteriori informazioni, vedere la sezione [preflight requests](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="b7a75-230">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="b7a75-231">Un criterio middleware CORS corrisponde a intestazioni specifiche specificate da `WithHeaders` è possibile solo quando le intestazioni inviate `Access-Control-Request-Headers` corrispondono esattamente alle intestazioni indicate in `WithHeaders` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-231">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="b7a75-232">Si consideri, ad esempio, un'app configurata come segue:</span><span class="sxs-lookup"><span data-stu-id="b7a75-232">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="b7a75-233">Il middleware CORS rifiuta una richiesta preliminare con la seguente intestazione di richiesta perché `Content-Language` ([HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) non è elencato in `WithHeaders` :</span><span class="sxs-lookup"><span data-stu-id="b7a75-233">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="b7a75-234">L'app restituisce una risposta *200 OK* , ma non invia le intestazioni CORS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-234">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="b7a75-235">Pertanto, il browser non tenta di eseguire la richiesta tra origini.</span><span class="sxs-lookup"><span data-stu-id="b7a75-235">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="b7a75-236">Impostare le intestazioni di risposta esposte</span><span class="sxs-lookup"><span data-stu-id="b7a75-236">Set the exposed response headers</span></span>

<span data-ttu-id="b7a75-237">Per impostazione predefinita, il browser non espone tutte le intestazioni di risposta all'app.</span><span class="sxs-lookup"><span data-stu-id="b7a75-237">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="b7a75-238">Per altre informazioni, vedere la pagina relativa alla [condivisione delle risorse tra le origini W3C (terminologia): intestazione della risposta semplice](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="b7a75-238">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="b7a75-239">Le intestazioni di risposta disponibili per impostazione predefinita sono:</span><span class="sxs-lookup"><span data-stu-id="b7a75-239">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="b7a75-240">La specifica CORS chiama queste intestazioni di *risposta semplici* .</span><span class="sxs-lookup"><span data-stu-id="b7a75-240">The CORS specification calls these headers *simple response headers* .</span></span> <span data-ttu-id="b7a75-241">Per rendere disponibili altre intestazioni per l'app, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="b7a75-241">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="b7a75-242">Credenziali nelle richieste tra le origini</span><span class="sxs-lookup"><span data-stu-id="b7a75-242">Credentials in cross-origin requests</span></span>

<span data-ttu-id="b7a75-243">Le credenziali richiedono una gestione speciale in una richiesta CORS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-243">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="b7a75-244">Per impostazione predefinita, il browser non invia le credenziali con una richiesta tra le origini.</span><span class="sxs-lookup"><span data-stu-id="b7a75-244">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="b7a75-245">Le credenziali includono :::no-loc(cookie)::: s e gli schemi di autenticazione HTTP.</span><span class="sxs-lookup"><span data-stu-id="b7a75-245">Credentials include :::no-loc(cookie):::s and HTTP authentication schemes.</span></span> <span data-ttu-id="b7a75-246">Per inviare le credenziali con una richiesta tra le origini, il client deve impostare `XMLHttpRequest.withCredentials` su `true` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-246">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="b7a75-247">Utilizzando `XMLHttpRequest` direttamente:</span><span class="sxs-lookup"><span data-stu-id="b7a75-247">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="b7a75-248">Uso di jQuery:</span><span class="sxs-lookup"><span data-stu-id="b7a75-248">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="b7a75-249">Uso dell' [API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="b7a75-249">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="b7a75-250">Il server deve consentire le credenziali.</span><span class="sxs-lookup"><span data-stu-id="b7a75-250">The server must allow the credentials.</span></span> <span data-ttu-id="b7a75-251">Per consentire le credenziali tra le origini, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="b7a75-251">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="b7a75-252">La risposta HTTP include un' `Access-Control-Allow-Credentials` intestazione che indica al browser che il server consente le credenziali per una richiesta tra le origini.</span><span class="sxs-lookup"><span data-stu-id="b7a75-252">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="b7a75-253">Se il browser invia le credenziali, ma la risposta non include un' `Access-Control-Allow-Credentials` intestazione valida, il browser non espone la risposta all'app e la richiesta tra le origini ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="b7a75-253">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="b7a75-254">Consentire le credenziali tra le origini costituisce un rischio per la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="b7a75-254">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="b7a75-255">Un sito Web in un altro dominio può inviare le credenziali dell'utente che ha eseguito l'accesso all'app per conto dell'utente senza la conoscenza dell'utente.</span><span class="sxs-lookup"><span data-stu-id="b7a75-255">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="b7a75-256">La specifica CORS indica inoltre che l'impostazione delle origini su `"*"` (tutte le origini) non è valida se l' `Access-Control-Allow-Credentials` intestazione è presente.</span><span class="sxs-lookup"><span data-stu-id="b7a75-256">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="b7a75-257">Richieste preliminari</span><span class="sxs-lookup"><span data-stu-id="b7a75-257">Preflight requests</span></span>

<span data-ttu-id="b7a75-258">Per alcune richieste CORS, il browser invia una richiesta di [Opzioni](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) aggiuntive prima di effettuare la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="b7a75-258">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="b7a75-259">Questa richiesta è detta [richiesta preliminare](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span><span class="sxs-lookup"><span data-stu-id="b7a75-259">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="b7a75-260">Il browser può ignorare la richiesta preliminare se si verificano **tutte** le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="b7a75-260">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="b7a75-261">Il metodo di richiesta è GET, HEAD o POST.</span><span class="sxs-lookup"><span data-stu-id="b7a75-261">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="b7a75-262">L'app non imposta intestazioni di richiesta diverse da `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` o `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-262">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="b7a75-263">L' `Content-Type` intestazione, se impostata, presenta uno dei valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="b7a75-263">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="b7a75-264">La regola sulle intestazioni di richiesta impostate per la richiesta del client si applica alle intestazioni impostate dall'app chiamando `setRequestHeader` sull' `XMLHttpRequest` oggetto.</span><span class="sxs-lookup"><span data-stu-id="b7a75-264">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="b7a75-265">La specifica CORS chiama queste intestazioni di [richiesta autore](https://www.w3.org/TR/cors/#author-request-headers)intestazioni.</span><span class="sxs-lookup"><span data-stu-id="b7a75-265">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="b7a75-266">La regola non si applica alle intestazioni che il browser può impostare, ad esempio `User-Agent` , `Host` o `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-266">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="b7a75-267">Di seguito è riportato un esempio di risposta simile alla richiesta preliminare eseguita dal pulsante **[Put test]** nella sezione [test CORS](#testc) di questo documento.</span><span class="sxs-lookup"><span data-stu-id="b7a75-267">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-:::no-loc(Cookie):::: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="b7a75-268">La richiesta preliminare usa il metodo delle [Opzioni http](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) .</span><span class="sxs-lookup"><span data-stu-id="b7a75-268">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="b7a75-269">Può includere le intestazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="b7a75-269">It may include the following headers:</span></span>

* <span data-ttu-id="b7a75-270">[Access-Control-request-method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): il metodo HTTP che verrà usato per la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="b7a75-270">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="b7a75-271">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): elenco di intestazioni di richiesta impostate dall'app sulla richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="b7a75-271">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="b7a75-272">Come indicato in precedenza, non include le intestazioni impostate dal browser, ad esempio `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-272">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="b7a75-273">Access-Control-Allow-Methods</span><span class="sxs-lookup"><span data-stu-id="b7a75-273">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="b7a75-274">Se la richiesta preliminare viene negata, l'app restituisce una `200 OK` risposta ma non imposta le intestazioni CORS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-274">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="b7a75-275">Pertanto, il browser non tenta di eseguire la richiesta tra origini.</span><span class="sxs-lookup"><span data-stu-id="b7a75-275">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="b7a75-276">Per un esempio di richiesta preliminare negata, vedere la sezione [test CORS](#testc) di questo documento.</span><span class="sxs-lookup"><span data-stu-id="b7a75-276">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="b7a75-277">Usando gli strumenti F12, l'app console Visualizza un errore simile a uno dei seguenti, a seconda del browser:</span><span class="sxs-lookup"><span data-stu-id="b7a75-277">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="b7a75-278">Firefox: richiesta tra le origini bloccata: gli stessi criteri di origine non consentono la lettura della risorsa remota in `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-278">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="b7a75-279">(Motivo: richiesta CORS non riuscita).</span><span class="sxs-lookup"><span data-stu-id="b7a75-279">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="b7a75-280">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="b7a75-280">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="b7a75-281">Basata su cromo: l'accesso al recupero in ' https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 ' dall'origine ' https://cors3.azurewebsites.net ' è stato bloccato dal criterio CORS: la risposta alla richiesta preliminare non supera il controllo di accesso: non è presente alcuna intestazione ' Access-Control-Allow-Origin ' nella risorsa richiesta.</span><span class="sxs-lookup"><span data-stu-id="b7a75-281">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="b7a75-282">Se si ritiene soddisfacente una risposta opaca, impostare la modalità della richiesta 'no-cors' per recuperare la risorsa con CORS disabilitato.</span><span class="sxs-lookup"><span data-stu-id="b7a75-282">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="b7a75-283">Per consentire intestazioni specifiche, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="b7a75-283">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="b7a75-284">Per consentire tutte le [intestazioni di richiesta di autore](https://www.w3.org/TR/cors/#author-request-headers), chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="b7a75-284">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="b7a75-285">I browser non sono coerenti con la modalità di impostazione `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-285">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="b7a75-286">Se uno:</span><span class="sxs-lookup"><span data-stu-id="b7a75-286">If either:</span></span>

* <span data-ttu-id="b7a75-287">Le intestazioni sono impostate su un valore diverso da `"*"`</span><span class="sxs-lookup"><span data-stu-id="b7a75-287">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="b7a75-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> viene chiamato: includere almeno `Accept` , `Content-Type` e `Origin` , più eventuali intestazioni personalizzate che si desidera supportare.</span><span class="sxs-lookup"><span data-stu-id="b7a75-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="b7a75-289">Codice richiesta preliminare automatica</span><span class="sxs-lookup"><span data-stu-id="b7a75-289">Automatic preflight request code</span></span>

<span data-ttu-id="b7a75-290">Quando viene applicato il criterio CORS:</span><span class="sxs-lookup"><span data-stu-id="b7a75-290">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="b7a75-291">A livello globale, chiamando il `app.UseCors` `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-291">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="b7a75-292">Utilizzando l' `[EnableCors]` attributo.</span><span class="sxs-lookup"><span data-stu-id="b7a75-292">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="b7a75-293">ASP.NET Core risponde alla richiesta di opzioni preliminari.</span><span class="sxs-lookup"><span data-stu-id="b7a75-293">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="b7a75-294">L'abilitazione di CORS in base all'endpoint `RequireCors` attualmente in **uso non** supporta le richieste preliminari automatiche.</span><span class="sxs-lookup"><span data-stu-id="b7a75-294">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="b7a75-295">Questo comportamento viene illustrato nella sezione [test CORS](#testc) di questo documento.</span><span class="sxs-lookup"><span data-stu-id="b7a75-295">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="b7a75-296">Attributo [HttpOptions] per le richieste preliminari</span><span class="sxs-lookup"><span data-stu-id="b7a75-296">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="b7a75-297">Quando CORS è abilitato con i criteri appropriati, ASP.NET Core risponde in genere alle richieste preliminari CORS automaticamente.</span><span class="sxs-lookup"><span data-stu-id="b7a75-297">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="b7a75-298">In alcuni scenari questo potrebbe non essere il caso.</span><span class="sxs-lookup"><span data-stu-id="b7a75-298">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="b7a75-299">Ad esempio, usando [cors con il routing degli endpoint](#ecors).</span><span class="sxs-lookup"><span data-stu-id="b7a75-299">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="b7a75-300">Il codice seguente usa l'attributo [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) per creare endpoint per le richieste di opzioni:</span><span class="sxs-lookup"><span data-stu-id="b7a75-300">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="b7a75-301">Per istruzioni sul test del codice precedente [, vedere test CORS with endpoint routing e [HttpOptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="b7a75-301">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="b7a75-302">Imposta la data di scadenza preliminare</span><span class="sxs-lookup"><span data-stu-id="b7a75-302">Set the preflight expiration time</span></span>

<span data-ttu-id="b7a75-303">L' `Access-Control-Max-Age` intestazione specifica per quanto tempo la risposta alla richiesta preliminare può essere memorizzata nella cache.</span><span class="sxs-lookup"><span data-stu-id="b7a75-303">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="b7a75-304">Per impostare questa intestazione, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="b7a75-304">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="b7a75-305">Funzionamento di CORS</span><span class="sxs-lookup"><span data-stu-id="b7a75-305">How CORS works</span></span>

<span data-ttu-id="b7a75-306">In questa sezione viene descritto cosa accade in una richiesta [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) al livello dei messaggi HTTP.</span><span class="sxs-lookup"><span data-stu-id="b7a75-306">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="b7a75-307">CORS **non** è una funzionalità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="b7a75-307">CORS is **not** a security feature.</span></span> <span data-ttu-id="b7a75-308">CORS è uno standard W3C che consente a un server di ridurre i criteri della stessa origine.</span><span class="sxs-lookup"><span data-stu-id="b7a75-308">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="b7a75-309">Ad esempio, un attore malintenzionato potrebbe usare [lo scripting tra siti (XSS)](xref:security/cross-site-scripting) per il sito ed eseguire una richiesta tra siti al sito abilitato per CORS per rubare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="b7a75-309">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="b7a75-310">Un'API non è più sicura consentendo CORS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-310">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="b7a75-311">Il client (browser) deve applicare CORS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-311">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="b7a75-312">Il server esegue la richiesta e restituisce la risposta, ovvero il client che restituisce un errore e blocca la risposta.</span><span class="sxs-lookup"><span data-stu-id="b7a75-312">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="b7a75-313">Uno degli strumenti seguenti, ad esempio, visualizzerà la risposta del server:</span><span class="sxs-lookup"><span data-stu-id="b7a75-313">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="b7a75-314">Fiddler</span><span class="sxs-lookup"><span data-stu-id="b7a75-314">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="b7a75-315">Postman</span><span class="sxs-lookup"><span data-stu-id="b7a75-315">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="b7a75-316">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="b7a75-316">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="b7a75-317">Un Web browser immettendo l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="b7a75-317">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="b7a75-318">Si tratta di un modo per consentire a un server di consentire ai browser di eseguire una richiesta dell' [API di recupero](https://developer.mozilla.org/docs/Web/API/Fetch_API) o di [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) tra le origini, che altrimenti sarebbe proibita.</span><span class="sxs-lookup"><span data-stu-id="b7a75-318">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="b7a75-319">I browser senza CORS non possono eseguire richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="b7a75-319">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="b7a75-320">Prima di CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) è stato usato per aggirare questa restrizione.</span><span class="sxs-lookup"><span data-stu-id="b7a75-320">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="b7a75-321">JSONP non usa XHR, usa il `<script>` tag per ricevere la risposta.</span><span class="sxs-lookup"><span data-stu-id="b7a75-321">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="b7a75-322">Gli script possono essere caricati tra le origini.</span><span class="sxs-lookup"><span data-stu-id="b7a75-322">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="b7a75-323">La [specifica CORS](https://www.w3.org/TR/cors/) ha introdotto diverse nuove intestazioni HTTP che consentono richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="b7a75-323">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="b7a75-324">Se un browser supporta CORS, queste intestazioni vengono impostate automaticamente per le richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="b7a75-324">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="b7a75-325">Il codice JavaScript personalizzato non è necessario per abilitare CORS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-325">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="b7a75-326">[Pulsante Put test](https://cors3.azurewebsites.net/test) nell' [esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) distribuito</span><span class="sxs-lookup"><span data-stu-id="b7a75-326">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="b7a75-327">Di seguito è riportato un esempio di richiesta tra più origini dal pulsante test [valori](https://cors3.azurewebsites.net/) a `https://cors1.azurewebsites.net/api/values` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-327">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="b7a75-328">`Origin`Intestazione:</span><span class="sxs-lookup"><span data-stu-id="b7a75-328">The `Origin` header:</span></span>

* <span data-ttu-id="b7a75-329">Fornisce il dominio del sito che sta effettuando la richiesta.</span><span class="sxs-lookup"><span data-stu-id="b7a75-329">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="b7a75-330">È obbligatorio e deve essere diverso dall'host.</span><span class="sxs-lookup"><span data-stu-id="b7a75-330">Is required and must be different from the host.</span></span>

<span data-ttu-id="b7a75-331">**Intestazioni generali**</span><span class="sxs-lookup"><span data-stu-id="b7a75-331">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="b7a75-332">**Intestazioni della risposta**</span><span class="sxs-lookup"><span data-stu-id="b7a75-332">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-:::no-loc(Cookie):::: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="b7a75-333">**Intestazioni della richiesta**</span><span class="sxs-lookup"><span data-stu-id="b7a75-333">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

<span data-ttu-id="b7a75-334">Nelle `OPTIONS` richieste, il server imposta l'intestazione delle **intestazioni di risposta** `Access-Control-Allow-Origin: {allowed origin}` nella risposta.</span><span class="sxs-lookup"><span data-stu-id="b7a75-334">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="b7a75-335">Ad esempio, la richiesta del pulsante [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) di [esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)distribuito `OPTIONS` contiene le intestazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="b7a75-335">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="b7a75-336">**Intestazioni generali**</span><span class="sxs-lookup"><span data-stu-id="b7a75-336">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="b7a75-337">**Intestazioni della risposta**</span><span class="sxs-lookup"><span data-stu-id="b7a75-337">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-:::no-loc(Cookie):::: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="b7a75-338">**Intestazioni della richiesta**</span><span class="sxs-lookup"><span data-stu-id="b7a75-338">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="b7a75-339">Nelle intestazioni di **risposta** precedenti, il server imposta l'intestazione [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) nella risposta.</span><span class="sxs-lookup"><span data-stu-id="b7a75-339">In the preceding **Response headers** , the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="b7a75-340">Il `https://cors1.azurewebsites.net` valore di questa intestazione corrisponde all' `Origin` intestazione della richiesta.</span><span class="sxs-lookup"><span data-stu-id="b7a75-340">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="b7a75-341">Se <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> viene chiamato, `Access-Control-Allow-Origin: *` viene restituito il valore del carattere jolly.</span><span class="sxs-lookup"><span data-stu-id="b7a75-341">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="b7a75-342">`AllowAnyOrigin` consente qualsiasi origine.</span><span class="sxs-lookup"><span data-stu-id="b7a75-342">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="b7a75-343">Se la risposta non include l' `Access-Control-Allow-Origin` intestazione, la richiesta tra le origini ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="b7a75-343">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="b7a75-344">In particolare, il browser non consente la richiesta.</span><span class="sxs-lookup"><span data-stu-id="b7a75-344">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="b7a75-345">Anche se il server restituisce una risposta con esito positivo, il browser non rende disponibile la risposta all'app client.</span><span class="sxs-lookup"><span data-stu-id="b7a75-345">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="b7a75-346">Richieste di opzioni di visualizzazione</span><span class="sxs-lookup"><span data-stu-id="b7a75-346">Display OPTIONS requests</span></span>

<span data-ttu-id="b7a75-347">Per impostazione predefinita, i browser Chrome e Edge non visualizzano le richieste OPTIONS nella scheda rete degli strumenti F12.</span><span class="sxs-lookup"><span data-stu-id="b7a75-347">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="b7a75-348">Per visualizzare le richieste di opzioni in questi browser:</span><span class="sxs-lookup"><span data-stu-id="b7a75-348">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="b7a75-349">`chrome://flags/#out-of-blink-cors` o `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="b7a75-349">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="b7a75-350">disabilitare il flag.</span><span class="sxs-lookup"><span data-stu-id="b7a75-350">disable the flag.</span></span>
* <span data-ttu-id="b7a75-351">Riavvia.</span><span class="sxs-lookup"><span data-stu-id="b7a75-351">restart.</span></span>

<span data-ttu-id="b7a75-352">Per impostazione predefinita, Firefox Visualizza le richieste OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-352">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="b7a75-353">CORS in IIS</span><span class="sxs-lookup"><span data-stu-id="b7a75-353">CORS in IIS</span></span>

<span data-ttu-id="b7a75-354">Quando si esegue la distribuzione in IIS, CORS deve essere eseguito prima dell'autenticazione di Windows se il server non è configurato per consentire l'accesso anonimo.</span><span class="sxs-lookup"><span data-stu-id="b7a75-354">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="b7a75-355">Per supportare questo scenario, è necessario installare e configurare il [modulo cors di IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) per l'app.</span><span class="sxs-lookup"><span data-stu-id="b7a75-355">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="b7a75-356">Testare CORS</span><span class="sxs-lookup"><span data-stu-id="b7a75-356">Test CORS</span></span>

<span data-ttu-id="b7a75-357">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) contiene codice per testare CORS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-357">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="b7a75-358">Vedere [come scaricare un esempio](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="b7a75-358">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="b7a75-359">L'esempio è un progetto API con :::no-loc(Razor)::: pagine aggiunte:</span><span class="sxs-lookup"><span data-stu-id="b7a75-359">The sample is an API project with :::no-loc(Razor)::: Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="b7a75-360">`WithOrigins("https://localhost:<port>");` deve essere usato solo per il test di un'app di esempio simile al [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)per il download.</span><span class="sxs-lookup"><span data-stu-id="b7a75-360">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="b7a75-361">Di seguito sono riportati `ValuesController` gli endpoint per i test:</span><span class="sxs-lookup"><span data-stu-id="b7a75-361">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="b7a75-362">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) viene fornito dal pacchetto NuGet [Rick.Docs. Samples. RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) e visualizza le informazioni sulla route.</span><span class="sxs-lookup"><span data-stu-id="b7a75-362">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="b7a75-363">Testare il codice di esempio precedente usando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="b7a75-363">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="b7a75-364">Usare l'app di esempio distribuita all'indirizzo [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="b7a75-364">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="b7a75-365">Non è necessario scaricare l'esempio.</span><span class="sxs-lookup"><span data-stu-id="b7a75-365">There is no need to download the sample.</span></span>
* <span data-ttu-id="b7a75-366">Eseguire l'esempio con `dotnet run` utilizzando l'URL predefinito `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-366">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="b7a75-367">Eseguire l'esempio da Visual Studio con la porta impostata su 44398 per un URL di `https://localhost:44398` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-367">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="b7a75-368">Uso di un browser con gli strumenti F12:</span><span class="sxs-lookup"><span data-stu-id="b7a75-368">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="b7a75-369">Selezionare il pulsante **valori** ed esaminare le intestazioni nella scheda **rete** .</span><span class="sxs-lookup"><span data-stu-id="b7a75-369">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="b7a75-370">Selezionare il pulsante **put test (Put test** ).</span><span class="sxs-lookup"><span data-stu-id="b7a75-370">Select the **PUT test** button.</span></span> <span data-ttu-id="b7a75-371">Per istruzioni sulla visualizzazione della richiesta OPTIONS, vedere [visualizzare le richieste di opzioni](#options) .</span><span class="sxs-lookup"><span data-stu-id="b7a75-371">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="b7a75-372">Il **test put** crea due richieste, una richiesta preliminare Options e la richiesta PUT.</span><span class="sxs-lookup"><span data-stu-id="b7a75-372">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="b7a75-373">Selezionare il **`GetValues2 [DisableCors]`** pulsante per attivare una richiesta CORS non riuscita.</span><span class="sxs-lookup"><span data-stu-id="b7a75-373">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="b7a75-374">Come indicato nel documento, la risposta restituisce 200 esito positivo, ma la richiesta CORS non viene eseguita.</span><span class="sxs-lookup"><span data-stu-id="b7a75-374">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="b7a75-375">Selezionare la scheda **console** per visualizzare l'errore CORS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-375">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="b7a75-376">A seconda del browser, viene visualizzato un errore simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="b7a75-376">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="b7a75-377">L'accesso al recupero `'https://cors1.azurewebsites.net/api/values/GetValues2'` dall'origine `'https://cors3.azurewebsites.net'` è stato bloccato dal criterio CORS: non è presente alcuna intestazione ' Access-Control-Allow-Origin ' nella risorsa richiesta.</span><span class="sxs-lookup"><span data-stu-id="b7a75-377">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="b7a75-378">Se si ritiene soddisfacente una risposta opaca, impostare la modalità della richiesta 'no-cors' per recuperare la risorsa con CORS disabilitato.</span><span class="sxs-lookup"><span data-stu-id="b7a75-378">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="b7a75-379">Gli endpoint abilitati per CORS possono essere testati con uno strumento, ad esempio [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)o [postazione](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="b7a75-379">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="b7a75-380">Quando si utilizza uno strumento, l'origine della richiesta specificata dall' `Origin` intestazione deve essere diversa dall'host che riceve la richiesta.</span><span class="sxs-lookup"><span data-stu-id="b7a75-380">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="b7a75-381">Se la richiesta non è *tra origini* basate sul valore dell' `Origin` intestazione:</span><span class="sxs-lookup"><span data-stu-id="b7a75-381">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="b7a75-382">Non è necessario che il middleware CORS elabori la richiesta.</span><span class="sxs-lookup"><span data-stu-id="b7a75-382">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="b7a75-383">Le intestazioni CORS non vengono restituite nella risposta.</span><span class="sxs-lookup"><span data-stu-id="b7a75-383">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="b7a75-384">Il comando seguente usa `curl` per emettere una richiesta OPTIONS con informazioni:</span><span class="sxs-lookup"><span data-stu-id="b7a75-384">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="b7a75-385">Test di CORS con routing endpoint e [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="b7a75-385">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="b7a75-386">L'abilitazione di CORS in base all'endpoint `RequireCors` attualmente in **uso non** supporta [le richieste preliminari automatiche](#apf).</span><span class="sxs-lookup"><span data-stu-id="b7a75-386">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="b7a75-387">Si consideri il codice seguente che usa il [routing dell'endpoint per abilitare CORS](#ecors):</span><span class="sxs-lookup"><span data-stu-id="b7a75-387">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="b7a75-388">Di seguito sono riportati gli `TodoItems1Controller` endpoint per i test:</span><span class="sxs-lookup"><span data-stu-id="b7a75-388">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="b7a75-389">Testare il codice precedente dalla [pagina di test](https://cors1.azurewebsites.net/test?number=1) dell' [esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)distribuito.</span><span class="sxs-lookup"><span data-stu-id="b7a75-389">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="b7a75-390">I pulsanti **Delete [EnableCors]** e **Get [EnableCors]** sono riusciti perché gli endpoint hanno `[EnableCors]` e rispondono alle richieste preliminari.</span><span class="sxs-lookup"><span data-stu-id="b7a75-390">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="b7a75-391">Gli altri endpoint hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="b7a75-391">The other endpoints fails.</span></span> <span data-ttu-id="b7a75-392">Il pulsante **Get** ha esito negativo perché il [codice JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) Invia:</span><span class="sxs-lookup"><span data-stu-id="b7a75-392">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="b7a75-393">Di seguito `TodoItems2Controller` vengono forniti endpoint simili, ma è incluso codice esplicito per rispondere alle richieste di opzioni:</span><span class="sxs-lookup"><span data-stu-id="b7a75-393">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="b7a75-394">Testare il codice precedente dalla [pagina di test](https://cors1.azurewebsites.net/test?number=2) dell'esempio distribuito.</span><span class="sxs-lookup"><span data-stu-id="b7a75-394">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="b7a75-395">Nell'elenco a discesa **controller** selezionare **preliminare** e quindi **impostare controller** .</span><span class="sxs-lookup"><span data-stu-id="b7a75-395">In the **Controller** drop down list, select **Preflight** and then **Set Controller** .</span></span> <span data-ttu-id="b7a75-396">Tutte le chiamate CORS agli `TodoItems2Controller` endpoint hanno esito positivo.</span><span class="sxs-lookup"><span data-stu-id="b7a75-396">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b7a75-397">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b7a75-397">Additional resources</span></span>

* [<span data-ttu-id="b7a75-398">Condivisione risorse tra le origini (CORS)</span><span class="sxs-lookup"><span data-stu-id="b7a75-398">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="b7a75-399">Introduzione al modulo CORS di IIS</span><span class="sxs-lookup"><span data-stu-id="b7a75-399">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b7a75-400">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b7a75-400">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b7a75-401">Questo articolo illustra come abilitare CORS in un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b7a75-401">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="b7a75-402">La sicurezza del browser impedisce a una pagina Web di eseguire richieste a un dominio diverso da quello che ha gestito la pagina Web.</span><span class="sxs-lookup"><span data-stu-id="b7a75-402">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="b7a75-403">Questa restrizione è detta *criterio della stessa origine* .</span><span class="sxs-lookup"><span data-stu-id="b7a75-403">This restriction is called the *same-origin policy* .</span></span> <span data-ttu-id="b7a75-404">Il criterio della stessa origine impedisce a un sito dannoso di leggere dati sensibili da un altro sito.</span><span class="sxs-lookup"><span data-stu-id="b7a75-404">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="b7a75-405">In alcuni casi potrebbe essere necessario consentire ad altri siti di effettuare richieste tra le origini all'app.</span><span class="sxs-lookup"><span data-stu-id="b7a75-405">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="b7a75-406">Per altre informazioni, vedere l' [articolo relativo a Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="b7a75-406">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="b7a75-407">[Condivisione risorse tra le origini](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="b7a75-407">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="b7a75-408">È uno standard W3C che consente a un server di ridurre i criteri della stessa origine.</span><span class="sxs-lookup"><span data-stu-id="b7a75-408">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="b7a75-409">**Non** è una funzionalità di sicurezza, CORS rilassa la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="b7a75-409">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="b7a75-410">Un'API non è più sicura consentendo CORS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-410">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="b7a75-411">Per ulteriori informazioni, vedere [funzionamento di CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="b7a75-411">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="b7a75-412">Consente a un server di consentire in modo esplicito alcune richieste tra origini rifiutando altre.</span><span class="sxs-lookup"><span data-stu-id="b7a75-412">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="b7a75-413">È più sicuro e flessibile rispetto alle tecniche precedenti, ad esempio [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="b7a75-413">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="b7a75-414">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b7a75-414">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="b7a75-415">Stessa origine</span><span class="sxs-lookup"><span data-stu-id="b7a75-415">Same origin</span></span>

<span data-ttu-id="b7a75-416">Due URL hanno la stessa origine se hanno schemi, host e porte identici ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="b7a75-416">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="b7a75-417">Questi due URL hanno la stessa origine:</span><span class="sxs-lookup"><span data-stu-id="b7a75-417">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="b7a75-418">Questi URL hanno origini diverse da quelle dei due URL precedenti:</span><span class="sxs-lookup"><span data-stu-id="b7a75-418">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="b7a75-419">`https://example.net`: Dominio diverso</span><span class="sxs-lookup"><span data-stu-id="b7a75-419">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="b7a75-420">`https://www.example.com/foo.html`: Sottodominio diverso</span><span class="sxs-lookup"><span data-stu-id="b7a75-420">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="b7a75-421">`http://example.com/foo.html`: Schema diverso</span><span class="sxs-lookup"><span data-stu-id="b7a75-421">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="b7a75-422">`https://example.com:9000/foo.html`: Porta diversa</span><span class="sxs-lookup"><span data-stu-id="b7a75-422">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="b7a75-423">Internet Explorer non considera la porta durante il confronto delle origini.</span><span class="sxs-lookup"><span data-stu-id="b7a75-423">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="b7a75-424">CORS con criteri e middleware denominati</span><span class="sxs-lookup"><span data-stu-id="b7a75-424">CORS with named policy and middleware</span></span>

<span data-ttu-id="b7a75-425">Il middleware CORS gestisce le richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="b7a75-425">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="b7a75-426">Il codice seguente Abilita CORS per l'intera app con l'origine specificata:</span><span class="sxs-lookup"><span data-stu-id="b7a75-426">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="b7a75-427">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="b7a75-427">The preceding code:</span></span>

* <span data-ttu-id="b7a75-428">Imposta il nome del criterio su " \_ myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="b7a75-428">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="b7a75-429">Il nome del criterio è arbitrario.</span><span class="sxs-lookup"><span data-stu-id="b7a75-429">The policy name is arbitrary.</span></span>
* <span data-ttu-id="b7a75-430">Chiama il <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodo di estensione, che Abilita CORS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-430">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="b7a75-431">Chiama <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> con un' [espressione lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="b7a75-431">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="b7a75-432">L'espressione lambda accetta un <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> oggetto.</span><span class="sxs-lookup"><span data-stu-id="b7a75-432">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="b7a75-433">Le [Opzioni di configurazione](#cors-policy-options), ad esempio `WithOrigins` , sono descritte più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="b7a75-433">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="b7a75-434">La <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chiamata al metodo aggiunge i servizi CORS al contenitore del servizio dell'app:</span><span class="sxs-lookup"><span data-stu-id="b7a75-434">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="b7a75-435">Per ulteriori informazioni, vedere [Opzioni dei criteri CORS](#cpo) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="b7a75-435">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="b7a75-436">Il <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> metodo può concatenare i metodi, come illustrato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="b7a75-436">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="b7a75-437">Nota: l'URL **non** deve contenere una barra finale ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="b7a75-437">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="b7a75-438">Se l'URL termina con `/` , il confronto restituisce `false` e non viene restituita alcuna intestazione.</span><span class="sxs-lookup"><span data-stu-id="b7a75-438">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="b7a75-439">Il codice seguente applica i criteri di CORS a tutti gli endpoint di app tramite il middleware CORS:</span><span class="sxs-lookup"><span data-stu-id="b7a75-439">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
<span data-ttu-id="b7a75-440">Nota: `UseCors` è necessario chiamare prima `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-440">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="b7a75-441">Vedere [abilitare CORS in :::no-loc(Razor)::: pagine, controller e metodi di azione](#ecors) per applicare i criteri di CORS a livello di pagina/controller/azione.</span><span class="sxs-lookup"><span data-stu-id="b7a75-441">See [Enable CORS in :::no-loc(Razor)::: Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="b7a75-442">Vedere [test CORS](#test) per istruzioni sul test di codice simile al codice precedente.</span><span class="sxs-lookup"><span data-stu-id="b7a75-442">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="b7a75-443">Abilita CORS con attributi</span><span class="sxs-lookup"><span data-stu-id="b7a75-443">Enable CORS with attributes</span></span>

<span data-ttu-id="b7a75-444">L'attributo [ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornisce un'alternativa all'applicazione di CORS a livello globale.</span><span class="sxs-lookup"><span data-stu-id="b7a75-444">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="b7a75-445">L' `[EnableCors]` attributo Abilita CORS per i punti finali selezionati, anziché tutti i punti finali.</span><span class="sxs-lookup"><span data-stu-id="b7a75-445">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="b7a75-446">Usare `[EnableCors]` per specificare i criteri predefiniti e `[EnableCors("{Policy String}")]` per specificare un criterio.</span><span class="sxs-lookup"><span data-stu-id="b7a75-446">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="b7a75-447">L' `[EnableCors]` attributo può essere applicato a:</span><span class="sxs-lookup"><span data-stu-id="b7a75-447">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="b7a75-448">:::no-loc(Razor)::: Pagina `PageModel`</span><span class="sxs-lookup"><span data-stu-id="b7a75-448">:::no-loc(Razor)::: Page `PageModel`</span></span>
* <span data-ttu-id="b7a75-449">Controller</span><span class="sxs-lookup"><span data-stu-id="b7a75-449">Controller</span></span>
* <span data-ttu-id="b7a75-450">Metodo di azione del controller</span><span class="sxs-lookup"><span data-stu-id="b7a75-450">Controller action method</span></span>

<span data-ttu-id="b7a75-451">È possibile applicare criteri diversi a controller/pagina-modello/azione con l'  `[EnableCors]` attributo.</span><span class="sxs-lookup"><span data-stu-id="b7a75-451">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="b7a75-452">Quando l' `[EnableCors]` attributo viene applicato a un metodo di azione/modello di pagina, mentre CORS è abilitato nel middleware, vengono applicati **entrambi** i criteri.</span><span class="sxs-lookup"><span data-stu-id="b7a75-452">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="b7a75-453">Si consiglia di **non** combinare i criteri.</span><span class="sxs-lookup"><span data-stu-id="b7a75-453">We recommend **not** combining policies.</span></span> <span data-ttu-id="b7a75-454">Usare l' `[EnableCors]` attributo o il middleware, **non entrambi** .</span><span class="sxs-lookup"><span data-stu-id="b7a75-454">Use the `[EnableCors]` attribute or middleware, **not both** .</span></span> <span data-ttu-id="b7a75-455">Quando `[EnableCors]` si usa, **non** definire criteri predefiniti.</span><span class="sxs-lookup"><span data-stu-id="b7a75-455">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="b7a75-456">Il codice seguente applica un criterio diverso a ogni metodo:</span><span class="sxs-lookup"><span data-stu-id="b7a75-456">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="b7a75-457">Il codice seguente crea un criterio predefinito CORS e un criterio denominato `"AnotherPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="b7a75-457">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="b7a75-458">Disabilitare CORS</span><span class="sxs-lookup"><span data-stu-id="b7a75-458">Disable CORS</span></span>

<span data-ttu-id="b7a75-459">L'attributo [ &lbrack; DISABLECORS &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) Disabilita CORS per il controller, il modello di pagina o l'azione.</span><span class="sxs-lookup"><span data-stu-id="b7a75-459">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="b7a75-460">Opzioni dei criteri di CORS</span><span class="sxs-lookup"><span data-stu-id="b7a75-460">CORS policy options</span></span>

<span data-ttu-id="b7a75-461">In questa sezione vengono descritte le varie opzioni che è possibile impostare in un criterio CORS:</span><span class="sxs-lookup"><span data-stu-id="b7a75-461">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="b7a75-462">Imposta le origini consentite</span><span class="sxs-lookup"><span data-stu-id="b7a75-462">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="b7a75-463">Impostare i metodi HTTP consentiti</span><span class="sxs-lookup"><span data-stu-id="b7a75-463">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="b7a75-464">Impostare le intestazioni della richiesta consentita</span><span class="sxs-lookup"><span data-stu-id="b7a75-464">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="b7a75-465">Impostare le intestazioni di risposta esposte</span><span class="sxs-lookup"><span data-stu-id="b7a75-465">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="b7a75-466">Credenziali nelle richieste tra le origini</span><span class="sxs-lookup"><span data-stu-id="b7a75-466">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="b7a75-467">Imposta la data di scadenza preliminare</span><span class="sxs-lookup"><span data-stu-id="b7a75-467">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="b7a75-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> viene chiamato in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b7a75-469">Per alcune opzioni, può essere utile leggere prima la sezione [come funziona CORS](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="b7a75-469">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="b7a75-470">Imposta le origini consentite</span><span class="sxs-lookup"><span data-stu-id="b7a75-470">Set the allowed origins</span></span>

<span data-ttu-id="b7a75-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Consente le richieste CORS da tutte le origini con qualsiasi schema ( `http` o `https` ).</span><span class="sxs-lookup"><span data-stu-id="b7a75-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="b7a75-472">`AllowAnyOrigin` non è sicuro perché *qualsiasi sito Web* può effettuare richieste tra origini per l'app.</span><span class="sxs-lookup"><span data-stu-id="b7a75-472">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="b7a75-473">La definizione di `AllowAnyOrigin` e `AllowCredentials` è una configurazione non sicura e può comportare la falsificazione della richiesta tra siti.</span><span class="sxs-lookup"><span data-stu-id="b7a75-473">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="b7a75-474">Per un'app sicura, specificare un elenco esatto di origini se il client deve autorizzare se stesso ad accedere alle risorse del server.</span><span class="sxs-lookup"><span data-stu-id="b7a75-474">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="b7a75-475">`AllowAnyOrigin` influiscono sulle richieste preliminari e sull' `Access-Control-Allow-Origin` intestazione.</span><span class="sxs-lookup"><span data-stu-id="b7a75-475">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="b7a75-476">Per ulteriori informazioni, vedere la sezione [preflight requests](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="b7a75-476">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="b7a75-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Imposta la <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> proprietà dei criteri in modo che sia una funzione che consente alle origini di corrispondere a un dominio con caratteri jolly configurato durante la valutazione se l'origine è consentita.</span><span class="sxs-lookup"><span data-stu-id="b7a75-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="b7a75-478">Impostare i metodi HTTP consentiti</span><span class="sxs-lookup"><span data-stu-id="b7a75-478">Set the allowed HTTP methods</span></span>

<span data-ttu-id="b7a75-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="b7a75-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="b7a75-480">Consente qualsiasi metodo HTTP:</span><span class="sxs-lookup"><span data-stu-id="b7a75-480">Allows any HTTP method:</span></span>
* <span data-ttu-id="b7a75-481">Influiscono sulle richieste preliminari e sull' `Access-Control-Allow-Methods` intestazione.</span><span class="sxs-lookup"><span data-stu-id="b7a75-481">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="b7a75-482">Per ulteriori informazioni, vedere la sezione [preflight requests](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="b7a75-482">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="b7a75-483">Impostare le intestazioni della richiesta consentita</span><span class="sxs-lookup"><span data-stu-id="b7a75-483">Set the allowed request headers</span></span>

<span data-ttu-id="b7a75-484">Per consentire l'invio di intestazioni specifiche in una richiesta CORS, denominate *intestazioni richiesta autore* , chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> e specificare le intestazioni consentite:</span><span class="sxs-lookup"><span data-stu-id="b7a75-484">To allow specific headers to be sent in a CORS request, called *author request headers* , call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="b7a75-485">Per consentire tutte le intestazioni di richiesta di autore, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="b7a75-485">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="b7a75-486">Questa impostazione influiscono sulle richieste preliminari e sull' `Access-Control-Request-Headers` intestazione.</span><span class="sxs-lookup"><span data-stu-id="b7a75-486">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="b7a75-487">Per ulteriori informazioni, vedere la sezione [preflight requests](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="b7a75-487">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="b7a75-488">Il middleware CORS consente sempre l'invio di quattro intestazioni in, `Access-Control-Request-Headers` indipendentemente dai valori configurati in CorsPolicy. Headers.</span><span class="sxs-lookup"><span data-stu-id="b7a75-488">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="b7a75-489">Questo elenco di intestazioni include:</span><span class="sxs-lookup"><span data-stu-id="b7a75-489">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="b7a75-490">Si consideri, ad esempio, un'app configurata come segue:</span><span class="sxs-lookup"><span data-stu-id="b7a75-490">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="b7a75-491">Il middleware CORS risponde correttamente a una richiesta preliminare con la seguente intestazione di richiesta perché `Content-Language` è sempre consentita:</span><span class="sxs-lookup"><span data-stu-id="b7a75-491">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always permitted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="b7a75-492">Impostare le intestazioni di risposta esposte</span><span class="sxs-lookup"><span data-stu-id="b7a75-492">Set the exposed response headers</span></span>

<span data-ttu-id="b7a75-493">Per impostazione predefinita, il browser non espone tutte le intestazioni di risposta all'app.</span><span class="sxs-lookup"><span data-stu-id="b7a75-493">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="b7a75-494">Per altre informazioni, vedere la pagina relativa alla [condivisione delle risorse tra le origini W3C (terminologia): intestazione della risposta semplice](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="b7a75-494">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="b7a75-495">Le intestazioni di risposta disponibili per impostazione predefinita sono:</span><span class="sxs-lookup"><span data-stu-id="b7a75-495">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="b7a75-496">La specifica CORS chiama queste intestazioni di *risposta semplici* .</span><span class="sxs-lookup"><span data-stu-id="b7a75-496">The CORS specification calls these headers *simple response headers* .</span></span> <span data-ttu-id="b7a75-497">Per rendere disponibili altre intestazioni per l'app, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="b7a75-497">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="b7a75-498">Credenziali nelle richieste tra le origini</span><span class="sxs-lookup"><span data-stu-id="b7a75-498">Credentials in cross-origin requests</span></span>

<span data-ttu-id="b7a75-499">Le credenziali richiedono una gestione speciale in una richiesta CORS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-499">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="b7a75-500">Per impostazione predefinita, il browser non invia le credenziali con una richiesta tra le origini.</span><span class="sxs-lookup"><span data-stu-id="b7a75-500">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="b7a75-501">Le credenziali includono :::no-loc(cookie)::: s e gli schemi di autenticazione HTTP.</span><span class="sxs-lookup"><span data-stu-id="b7a75-501">Credentials include :::no-loc(cookie):::s and HTTP authentication schemes.</span></span> <span data-ttu-id="b7a75-502">Per inviare le credenziali con una richiesta tra le origini, il client deve impostare `XMLHttpRequest.withCredentials` su `true` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-502">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="b7a75-503">Utilizzando `XMLHttpRequest` direttamente:</span><span class="sxs-lookup"><span data-stu-id="b7a75-503">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="b7a75-504">Uso di jQuery:</span><span class="sxs-lookup"><span data-stu-id="b7a75-504">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="b7a75-505">Uso dell' [API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="b7a75-505">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="b7a75-506">Il server deve consentire le credenziali.</span><span class="sxs-lookup"><span data-stu-id="b7a75-506">The server must allow the credentials.</span></span> <span data-ttu-id="b7a75-507">Per consentire le credenziali tra le origini, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="b7a75-507">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="b7a75-508">La risposta HTTP include un' `Access-Control-Allow-Credentials` intestazione che indica al browser che il server consente le credenziali per una richiesta tra le origini.</span><span class="sxs-lookup"><span data-stu-id="b7a75-508">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="b7a75-509">Se il browser invia le credenziali, ma la risposta non include un' `Access-Control-Allow-Credentials` intestazione valida, il browser non espone la risposta all'app e la richiesta tra le origini ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="b7a75-509">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="b7a75-510">Consentire le credenziali tra le origini costituisce un rischio per la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="b7a75-510">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="b7a75-511">Un sito Web in un altro dominio può inviare le credenziali dell'utente che ha eseguito l'accesso all'app per conto dell'utente senza la conoscenza dell'utente.</span><span class="sxs-lookup"><span data-stu-id="b7a75-511">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="b7a75-512">La specifica CORS indica inoltre che l'impostazione delle origini su `"*"` (tutte le origini) non è valida se l' `Access-Control-Allow-Credentials` intestazione è presente.</span><span class="sxs-lookup"><span data-stu-id="b7a75-512">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="b7a75-513">Richieste preliminari</span><span class="sxs-lookup"><span data-stu-id="b7a75-513">Preflight requests</span></span>

<span data-ttu-id="b7a75-514">Per alcune richieste CORS, il browser invia una richiesta aggiuntiva prima di effettuare la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="b7a75-514">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="b7a75-515">Questa richiesta è detta *richiesta preliminare* .</span><span class="sxs-lookup"><span data-stu-id="b7a75-515">This request is called a *preflight request* .</span></span> <span data-ttu-id="b7a75-516">Il browser può ignorare la richiesta preliminare se vengono soddisfatte le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="b7a75-516">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="b7a75-517">Il metodo di richiesta è GET, HEAD o POST.</span><span class="sxs-lookup"><span data-stu-id="b7a75-517">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="b7a75-518">L'app non imposta intestazioni di richiesta diverse da `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` o `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-518">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="b7a75-519">L' `Content-Type` intestazione, se impostata, presenta uno dei valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="b7a75-519">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="b7a75-520">La regola sulle intestazioni di richiesta impostate per la richiesta del client si applica alle intestazioni impostate dall'app chiamando `setRequestHeader` sull' `XMLHttpRequest` oggetto.</span><span class="sxs-lookup"><span data-stu-id="b7a75-520">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="b7a75-521">La specifica CORS chiama queste intestazioni di *richiesta autore* intestazioni.</span><span class="sxs-lookup"><span data-stu-id="b7a75-521">The CORS specification calls these headers *author request headers* .</span></span> <span data-ttu-id="b7a75-522">La regola non si applica alle intestazioni che il browser può impostare, ad esempio `User-Agent` , `Host` o `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-522">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="b7a75-523">Di seguito è riportato un esempio di una richiesta preliminare:</span><span class="sxs-lookup"><span data-stu-id="b7a75-523">The following is an example of a preflight request:</span></span>

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="b7a75-524">La richiesta pre-flight usa il metodo delle opzioni HTTP.</span><span class="sxs-lookup"><span data-stu-id="b7a75-524">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="b7a75-525">Sono incluse due intestazioni speciali:</span><span class="sxs-lookup"><span data-stu-id="b7a75-525">It includes two special headers:</span></span>

* <span data-ttu-id="b7a75-526">`Access-Control-Request-Method`: Metodo HTTP che verrà usato per la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="b7a75-526">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="b7a75-527">`Access-Control-Request-Headers`: Elenco di intestazioni di richiesta impostate dall'app sulla richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="b7a75-527">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="b7a75-528">Come indicato in precedenza, non include le intestazioni impostate dal browser, ad esempio `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-528">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="b7a75-529">Quando CORS è abilitato con i criteri appropriati, ASP.NET Core in genere risponde automaticamente alle richieste preliminari di CORS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-529">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="b7a75-530">Vedere l' [attributo [HttpOptions] per le richieste preliminari](#pro).</span><span class="sxs-lookup"><span data-stu-id="b7a75-530">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="b7a75-531">Una richiesta preliminare CORS può includere un' `Access-Control-Request-Headers` intestazione, che indica al server le intestazioni inviate con la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="b7a75-531">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="b7a75-532">Per consentire intestazioni specifiche, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="b7a75-532">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="b7a75-533">Per consentire tutte le intestazioni di richiesta di autore, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="b7a75-533">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="b7a75-534">I browser non sono completamente coerenti nel modo in cui vengono impostati `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="b7a75-534">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="b7a75-535">Se si impostano le intestazioni su un valore diverso da `"*"` (o si utilizza <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> ), è necessario includere almeno `Accept` , `Content-Type` e `Origin` , più eventuali intestazioni personalizzate che si desidera supportare.</span><span class="sxs-lookup"><span data-stu-id="b7a75-535">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="b7a75-536">Di seguito è riportato un esempio di risposta alla richiesta preliminare (presupponendo che il server consenta la richiesta):</span><span class="sxs-lookup"><span data-stu-id="b7a75-536">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="b7a75-537">La risposta include un' `Access-Control-Allow-Methods` intestazione che elenca i metodi consentiti e, facoltativamente `Access-Control-Allow-Headers` , un'intestazione, che elenca le intestazioni consentite.</span><span class="sxs-lookup"><span data-stu-id="b7a75-537">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="b7a75-538">Se la richiesta preliminare ha esito positivo, il browser invia la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="b7a75-538">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="b7a75-539">Se la richiesta preliminare viene negata, l'app restituisce una risposta *200 OK* , ma non invia le intestazioni CORS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-539">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="b7a75-540">Pertanto, il browser non tenta di eseguire la richiesta tra origini.</span><span class="sxs-lookup"><span data-stu-id="b7a75-540">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="b7a75-541">Imposta la data di scadenza preliminare</span><span class="sxs-lookup"><span data-stu-id="b7a75-541">Set the preflight expiration time</span></span>

<span data-ttu-id="b7a75-542">L' `Access-Control-Max-Age` intestazione specifica per quanto tempo la risposta alla richiesta preliminare può essere memorizzata nella cache.</span><span class="sxs-lookup"><span data-stu-id="b7a75-542">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="b7a75-543">Per impostare questa intestazione, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="b7a75-543">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="b7a75-544">Funzionamento di CORS</span><span class="sxs-lookup"><span data-stu-id="b7a75-544">How CORS works</span></span>

<span data-ttu-id="b7a75-545">In questa sezione viene descritto cosa accade in una richiesta [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) al livello dei messaggi HTTP.</span><span class="sxs-lookup"><span data-stu-id="b7a75-545">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="b7a75-546">CORS **non** è una funzionalità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="b7a75-546">CORS is **not** a security feature.</span></span> <span data-ttu-id="b7a75-547">CORS è uno standard W3C che consente a un server di ridurre i criteri della stessa origine.</span><span class="sxs-lookup"><span data-stu-id="b7a75-547">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="b7a75-548">Ad esempio, un attore malintenzionato potrebbe usare [Impedisci lo scripting tra siti (XSS)](xref:security/cross-site-scripting) per il sito ed eseguire una richiesta tra siti al sito abilitato per CORS per rubare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="b7a75-548">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="b7a75-549">L'API non è più sicura consentendo CORS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-549">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="b7a75-550">Il client (browser) deve applicare CORS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-550">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="b7a75-551">Il server esegue la richiesta e restituisce la risposta, ovvero il client che restituisce un errore e blocca la risposta.</span><span class="sxs-lookup"><span data-stu-id="b7a75-551">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="b7a75-552">Uno degli strumenti seguenti, ad esempio, visualizzerà la risposta del server:</span><span class="sxs-lookup"><span data-stu-id="b7a75-552">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="b7a75-553">Fiddler</span><span class="sxs-lookup"><span data-stu-id="b7a75-553">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="b7a75-554">Postman</span><span class="sxs-lookup"><span data-stu-id="b7a75-554">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="b7a75-555">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="b7a75-555">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="b7a75-556">Un Web browser immettendo l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="b7a75-556">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="b7a75-557">Si tratta di un modo per consentire a un server di consentire ai browser di eseguire una richiesta dell' [API di recupero](https://developer.mozilla.org/docs/Web/API/Fetch_API) o di [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) tra le origini, che altrimenti sarebbe proibita.</span><span class="sxs-lookup"><span data-stu-id="b7a75-557">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="b7a75-558">I browser (senza CORS) non possono eseguire richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="b7a75-558">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="b7a75-559">Prima di CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) è stato usato per aggirare questa restrizione.</span><span class="sxs-lookup"><span data-stu-id="b7a75-559">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="b7a75-560">JSONP non usa XHR, usa il `<script>` tag per ricevere la risposta.</span><span class="sxs-lookup"><span data-stu-id="b7a75-560">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="b7a75-561">Gli script possono essere caricati tra le origini.</span><span class="sxs-lookup"><span data-stu-id="b7a75-561">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="b7a75-562">La [specifica CORS](https://www.w3.org/TR/cors/) ha introdotto diverse nuove intestazioni HTTP che consentono richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="b7a75-562">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="b7a75-563">Se un browser supporta CORS, queste intestazioni vengono impostate automaticamente per le richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="b7a75-563">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="b7a75-564">Il codice JavaScript personalizzato non è necessario per abilitare CORS.</span><span class="sxs-lookup"><span data-stu-id="b7a75-564">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="b7a75-565">Di seguito è riportato un esempio di richiesta tra le origini.</span><span class="sxs-lookup"><span data-stu-id="b7a75-565">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="b7a75-566">L' `Origin` intestazione fornisce il dominio del sito che sta effettuando la richiesta.</span><span class="sxs-lookup"><span data-stu-id="b7a75-566">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="b7a75-567">L' `Origin` intestazione è obbligatoria e deve essere diversa dall'host.</span><span class="sxs-lookup"><span data-stu-id="b7a75-567">The `Origin` header is required and must be different from the host.</span></span>

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="b7a75-568">Se il server consente la richiesta, imposta l' `Access-Control-Allow-Origin` intestazione nella risposta.</span><span class="sxs-lookup"><span data-stu-id="b7a75-568">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="b7a75-569">Il valore di questa intestazione corrisponde all' `Origin` intestazione della richiesta o è il valore del carattere jolly `"*"` , ovvero qualsiasi origine è consentita:</span><span class="sxs-lookup"><span data-stu-id="b7a75-569">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="b7a75-570">Se la risposta non include l' `Access-Control-Allow-Origin` intestazione, la richiesta tra le origini ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="b7a75-570">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="b7a75-571">In particolare, il browser non consente la richiesta.</span><span class="sxs-lookup"><span data-stu-id="b7a75-571">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="b7a75-572">Anche se il server restituisce una risposta con esito positivo, il browser non rende disponibile la risposta all'app client.</span><span class="sxs-lookup"><span data-stu-id="b7a75-572">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="b7a75-573">Testare CORS</span><span class="sxs-lookup"><span data-stu-id="b7a75-573">Test CORS</span></span>

<span data-ttu-id="b7a75-574">Per testare CORS:</span><span class="sxs-lookup"><span data-stu-id="b7a75-574">To test CORS:</span></span>

1. <span data-ttu-id="b7a75-575">[Creare un progetto API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="b7a75-575">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="b7a75-576">In alternativa, è possibile [scaricare l'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="b7a75-576">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="b7a75-577">Abilitare CORS usando uno degli approcci descritti in questo documento.</span><span class="sxs-lookup"><span data-stu-id="b7a75-577">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="b7a75-578">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b7a75-578">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="b7a75-579">`WithOrigins("https://localhost:<port>");` deve essere usato solo per il test di un'app di esempio simile al [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)per il download.</span><span class="sxs-lookup"><span data-stu-id="b7a75-579">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="b7a75-580">Creare un progetto di app Web ( :::no-loc(Razor)::: pagine o MVC).</span><span class="sxs-lookup"><span data-stu-id="b7a75-580">Create a web app project (:::no-loc(Razor)::: Pages or MVC).</span></span> <span data-ttu-id="b7a75-581">Nell'esempio vengono utilizzate le :::no-loc(Razor)::: pagine.</span><span class="sxs-lookup"><span data-stu-id="b7a75-581">The sample uses :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="b7a75-582">È possibile creare l'app Web nella stessa soluzione del progetto API.</span><span class="sxs-lookup"><span data-stu-id="b7a75-582">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="b7a75-583">Aggiungere il codice evidenziato seguente al file *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b7a75-583">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-cshtml[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="b7a75-584">Nel codice precedente sostituire `url: 'https://<web app>.azurewebsites.net/api/values/1',` con l'URL dell'app distribuita.</span><span class="sxs-lookup"><span data-stu-id="b7a75-584">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="b7a75-585">Distribuire il progetto API.</span><span class="sxs-lookup"><span data-stu-id="b7a75-585">Deploy the API project.</span></span> <span data-ttu-id="b7a75-586">Ad esempio, [eseguire la distribuzione in Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="b7a75-586">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="b7a75-587">Eseguire le :::no-loc(Razor)::: pagine o l'app MVC dal desktop e fare clic sul pulsante **test** .</span><span class="sxs-lookup"><span data-stu-id="b7a75-587">Run the :::no-loc(Razor)::: Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="b7a75-588">Usare gli strumenti F12 per esaminare i messaggi di errore.</span><span class="sxs-lookup"><span data-stu-id="b7a75-588">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="b7a75-589">Rimuovere l'origine localhost da `WithOrigins` e distribuire l'app.</span><span class="sxs-lookup"><span data-stu-id="b7a75-589">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="b7a75-590">In alternativa, eseguire l'app client con una porta diversa.</span><span class="sxs-lookup"><span data-stu-id="b7a75-590">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="b7a75-591">Ad esempio, eseguire da Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b7a75-591">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="b7a75-592">Eseguire il test con l'app client.</span><span class="sxs-lookup"><span data-stu-id="b7a75-592">Test with the client app.</span></span> <span data-ttu-id="b7a75-593">Gli errori CORS restituiscono un errore, ma il messaggio di errore non è disponibile per JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b7a75-593">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="b7a75-594">Usare la scheda console negli strumenti F12 per visualizzare l'errore.</span><span class="sxs-lookup"><span data-stu-id="b7a75-594">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="b7a75-595">A seconda del browser, viene ricevuto un errore (nella console strumenti F12) simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="b7a75-595">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="b7a75-596">Uso di Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="b7a75-596">Using Microsoft Edge:</span></span>

     <span data-ttu-id="b7a75-597">**SEC7120: [CORS] l'origine `https://localhost:44375` non è stata trovata `https://localhost:44375` nell'intestazione della risposta Access-Control-Allow-Origin per la risorsa tra le origini in `https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="b7a75-597">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="b7a75-598">Uso di Chrome:</span><span class="sxs-lookup"><span data-stu-id="b7a75-598">Using Chrome:</span></span>

     <span data-ttu-id="b7a75-599">**L'accesso a XMLHttpRequest `https://webapi.azurewebsites.net/api/values/1` da Origin `https://localhost:44375` è stato bloccato dal criterio CORS: non è presente alcuna intestazione ' Access-Control-Allow-Origin ' nella risorsa richiesta.**</span><span class="sxs-lookup"><span data-stu-id="b7a75-599">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="b7a75-600">Gli endpoint abilitati per CORS possono essere testati con uno strumento, ad esempio [Fiddler](https://www.telerik.com/fiddler) o [postazione](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="b7a75-600">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="b7a75-601">Quando si utilizza uno strumento, l'origine della richiesta specificata dall' `Origin` intestazione deve essere diversa dall'host che riceve la richiesta.</span><span class="sxs-lookup"><span data-stu-id="b7a75-601">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="b7a75-602">Se la richiesta non è *tra origini* basate sul valore dell' `Origin` intestazione:</span><span class="sxs-lookup"><span data-stu-id="b7a75-602">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="b7a75-603">Non è necessario che il middleware CORS elabori la richiesta.</span><span class="sxs-lookup"><span data-stu-id="b7a75-603">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="b7a75-604">Le intestazioni CORS non vengono restituite nella risposta.</span><span class="sxs-lookup"><span data-stu-id="b7a75-604">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="b7a75-605">CORS in IIS</span><span class="sxs-lookup"><span data-stu-id="b7a75-605">CORS in IIS</span></span>

<span data-ttu-id="b7a75-606">Quando si esegue la distribuzione in IIS, CORS deve essere eseguito prima dell'autenticazione di Windows se il server non è configurato per consentire l'accesso anonimo.</span><span class="sxs-lookup"><span data-stu-id="b7a75-606">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="b7a75-607">Per supportare questo scenario, è necessario installare e configurare il [modulo cors di IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) per l'app.</span><span class="sxs-lookup"><span data-stu-id="b7a75-607">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b7a75-608">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b7a75-608">Additional resources</span></span>

* [<span data-ttu-id="b7a75-609">Condivisione risorse tra le origini (CORS)</span><span class="sxs-lookup"><span data-stu-id="b7a75-609">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="b7a75-610">Introduzione al modulo CORS di IIS</span><span class="sxs-lookup"><span data-stu-id="b7a75-610">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
