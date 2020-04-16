---
title: Abilitare le richieste cross-origin (CORS) in ASP.NET CoreEnable Cross-Origin Requests (CORS) in ASP.NET Core
author: rick-anderson
description: Scopri come CORS come standard per consentire o rifiutare le richieste tra origini in un'app ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: e7731fd967c206679ac93209fdb84f40367bea37
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440909"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="a9ea5-103">Abilitare le richieste cross-origin (CORS) in ASP.NET CoreEnable Cross-Origin Requests (CORS) in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a9ea5-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a9ea5-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e Kirk [Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="a9ea5-105">Questo articolo illustra come abilitare CORS in un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="a9ea5-106">La sicurezza del browser impedisce a una pagina Web di effettuare richieste a un dominio diverso da quello che ha servito la pagina web.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="a9ea5-107">Questa restrizione è *denominata criterio*della stessa origine .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="a9ea5-108">I criteri della stessa origine impediscono a un sito dannoso di leggere dati sensibili da un altro sito.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="a9ea5-109">In alcuni alcuni punti, potresti voler consentire ad altri siti di effettuare richieste cross-origin alla tua app.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="a9ea5-110">Per ulteriori informazioni, vedere l'articolo di [Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="a9ea5-111">[Condivisione delle risorse tra origini](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="a9ea5-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="a9ea5-112">È uno standard W3C che consente a un server di ridurre la stessa origine.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="a9ea5-113">**Non** è una funzione di sicurezza, CORS rilassa la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="a9ea5-114">Un'API non è più sicura consentendo CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="a9ea5-115">Per ulteriori informazioni, consultate [Funzionamento di CORS.](#how-cors)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="a9ea5-116">Consente a un server di consentire in modo esplicito alcune richieste tra origini rifiutandone altre.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="a9ea5-117">È più sicuro e più flessibile rispetto alle tecniche precedenti, ad esempio [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="a9ea5-118">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a9ea5-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="a9ea5-119">Stessa origine</span><span class="sxs-lookup"><span data-stu-id="a9ea5-119">Same origin</span></span>

<span data-ttu-id="a9ea5-120">Due URL hanno la stessa origine se hanno schemi, host e porte identici ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="a9ea5-121">Questi due URL hanno la stessa origine:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="a9ea5-122">Questi URL hanno origini diverse rispetto ai due URL precedenti:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="a9ea5-123">`https://example.net`&ndash; Dominio diverso</span><span class="sxs-lookup"><span data-stu-id="a9ea5-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="a9ea5-124">`https://www.example.com/foo.html`&ndash; Sottodominio diverso</span><span class="sxs-lookup"><span data-stu-id="a9ea5-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="a9ea5-125">`http://example.com/foo.html`&ndash; Schema diverso</span><span class="sxs-lookup"><span data-stu-id="a9ea5-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="a9ea5-126">`https://example.com:9000/foo.html`&ndash; Porta diversa</span><span class="sxs-lookup"><span data-stu-id="a9ea5-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="a9ea5-127">Abilitare CORS</span><span class="sxs-lookup"><span data-stu-id="a9ea5-127">Enable CORS</span></span>

<span data-ttu-id="a9ea5-128">Esistono tre modi per abilitare CORS:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="a9ea5-129">Nel middleware che utilizza un [criterio denominato](#np) o [predefinito](#dp).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="a9ea5-130">Utilizzo del [routing endpoint](#ecors).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="a9ea5-131">Con l'attributo [[EnableCors].](#attr)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="a9ea5-132">L'utilizzo dell'attributo [[EnableCors]](#attr) con un criterio denominato fornisce il controllo più condizionato per limitare gli endpoint che supportano CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="a9ea5-133">Ogni approccio è descritto in dettaglio nelle sezioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="a9ea5-134">CORS con criteri denominati e middleware</span><span class="sxs-lookup"><span data-stu-id="a9ea5-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="a9ea5-135">IL middleware CORS gestisce le richieste tra origini.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="a9ea5-136">Il codice seguente applica criteri CORS a tutti gli endpoint dell'app con le origini specificate:The following code applies a CORS policy to all the app's endpoints with the specified origins:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="a9ea5-137">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-137">The preceding code:</span></span>

* <span data-ttu-id="a9ea5-138">Imposta il nome `_myAllowSpecificOrigins`del criterio su .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="a9ea5-139">Il nome del criterio è arbitrario.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="a9ea5-140">Chiama <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> il metodo di `_myAllowSpecificOrigins` estensione e specifica il criterio CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="a9ea5-141">`UseCors`aggiunge il middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-141">`UseCors` adds the CORS middleware.</span></span>
* <span data-ttu-id="a9ea5-142">Chiamate <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> con [un'espressione lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-142">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="a9ea5-143">L'espressione <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> lambda accetta un oggetto.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-143">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="a9ea5-144">[Le opzioni](#cors-policy-options)di `WithOrigins`configurazione, ad esempio , sono descritte più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-144">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="a9ea5-145">Abilita `_myAllowSpecificOrigins` i criteri CORS per tutti gli endpoint del controller.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-145">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="a9ea5-146">Vedere [routing degli endpoint](#ecors) per applicare criteri CORS a endpoint specifici.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-146">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="a9ea5-147">Con il routing degli endpoint, il middleware CORS `UseRouting` `UseEndpoints` ***deve*** essere configurato per l'esecuzione tra le chiamate a e .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-147">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="a9ea5-148">Vedere [Test CORS](#testc) per istruzioni sul test di codice simile al codice precedente.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-148">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="a9ea5-149">La <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chiamata al metodo aggiunge servizi CORS al contenitore dei servizi dell'app:The method call adds CORS services to the app's service container:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-149">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="a9ea5-150">Per ulteriori informazioni, vedere [Opzioni dei criteri CORS](#cpo) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-150">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="a9ea5-151">I <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> metodi possono essere concatenati, come illustrato nel codice seguente:The methods can be chained, as shown in the following code:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-151">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="a9ea5-152">Nota: l'URL specificato **non** deve`/`contenere una barra finale ( ).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-152">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="a9ea5-153">Se l'URL `/`termina con `false` , il confronto restituisce e non viene restituita alcuna intestazione.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-153">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="a9ea5-154">CORS con criteri e middleware predefiniti</span><span class="sxs-lookup"><span data-stu-id="a9ea5-154">CORS with default policy and middleware</span></span>

<span data-ttu-id="a9ea5-155">Il codice evidenziato seguente abilita il criterio CORS predefinito:The following highlighted code enables the default CORS policy:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-155">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="a9ea5-156">Il codice precedente applica i criteri CORS predefiniti a tutti gli endpoint del controller.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-156">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="a9ea5-157">Abilitare Cors con routing endpoint</span><span class="sxs-lookup"><span data-stu-id="a9ea5-157">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="a9ea5-158">L'abilitazione di CORS `RequireCors` in base all'endpoint che utilizza attualmente ***non*** supporta le richieste automatiche di [verifica preliminare.](#apf)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-158">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="a9ea5-159">Per ulteriori informazioni, vedere [questo problema di GitHub](https://github.com/dotnet/aspnetcore/issues/20709) e [Test corS con routing degli endpoint e [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-159">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="a9ea5-160">Con il routing degli endpoint, CORS può essere <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> abilitato in base all'endpoint usando il set di metodi di estensione:With endpoint routing, CORS can be enabled on a per-endpoint basis using the set of extension methods:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-160">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="a9ea5-161">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-161">In the preceding code:</span></span>

* <span data-ttu-id="a9ea5-162">`app.UseCors`abilita il middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-162">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="a9ea5-163">Poiché un criterio predefinito non `app.UseCors()` è stato configurato, da solo non abilita CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-163">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="a9ea5-164">Gli `/echo` endpoint e del controller consentono le richieste tra origini utilizzando i criteri specificati.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-164">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="a9ea5-165">Gli `/echo2` endpoint e Razor Pages ***non*** consentono richieste tra origini perché non è stato specificato alcun criterio predefinito.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-165">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="a9ea5-166">L'attributo [[DisableCors]](#dc) ***non*** disabilita CORS abilitato `RequireCors`dal routing degli endpoint con .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-166">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="a9ea5-167">Vedere [Test CORS con routing degli endpoint e [HttpOptions]](#tcer) per istruzioni sul test di codice simile al precedente.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-167">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="a9ea5-168">Abilitare CORS con attributi</span><span class="sxs-lookup"><span data-stu-id="a9ea5-168">Enable CORS with attributes</span></span>

<span data-ttu-id="a9ea5-169">L'abilitazione di CORS con l'attributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) e l'applicazione di un criterio denominato solo agli endpoint che richiedono CORS forniscono il controllo migliore.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-169">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="a9ea5-170">L'attributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornisce un'alternativa all'applicazione di CORS a livello globale.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-170">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="a9ea5-171">L'attributo `[EnableCors]` abilita CORS per gli endpoint selezionati, anziché per tutti gli endpoint:The attribute enables CORS for selected endpoints, rather than all endpoints:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-171">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="a9ea5-172">`[EnableCors]`specifica il criterio predefinito.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-172">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="a9ea5-173">`[EnableCors("{Policy String}")]`specifica un criterio denominato.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-173">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="a9ea5-174">L'attributo `[EnableCors]` può essere applicato a:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-174">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="a9ea5-175">Pagina Razor`PageModel`</span><span class="sxs-lookup"><span data-stu-id="a9ea5-175">Razor Page `PageModel`</span></span>
* <span data-ttu-id="a9ea5-176">Controller</span><span class="sxs-lookup"><span data-stu-id="a9ea5-176">Controller</span></span>
* <span data-ttu-id="a9ea5-177">Metodo di azione del controller</span><span class="sxs-lookup"><span data-stu-id="a9ea5-177">Controller action method</span></span>

<span data-ttu-id="a9ea5-178">È possibile applicare criteri diversi ai controller, `[EnableCors]` ai modelli di pagina o ai metodi di azione con l'attributo.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-178">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="a9ea5-179">Quando `[EnableCors]` l'attributo viene applicato a un controller, a un modello di pagina o a un metodo di azione e CORS è abilitato nel middleware, vengono applicati ***entrambi i*** criteri.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-179">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="a9ea5-180">***Si consiglia di non combinare le politiche. Usa l'attributo*** `[EnableCors]` o il ***middleware, non entrambi nella stessa app.***</span><span class="sxs-lookup"><span data-stu-id="a9ea5-180">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="a9ea5-181">Il codice seguente applica criteri diversi a ogni metodo:The following code applies a different policy to each method:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-181">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="a9ea5-182">Il codice seguente crea due criteri CORS:The following code creates two CORS policies:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-182">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="a9ea5-183">Per il miglior controllo della limitazione delle richieste CORS:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-183">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="a9ea5-184">Utilizzare `[EnableCors("MyPolicy")]` con un criterio denominato.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-184">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="a9ea5-185">Non definire un criterio predefinito.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-185">Don't define a default policy.</span></span>
* <span data-ttu-id="a9ea5-186">Non utilizzare il [routing degli endpoint.](#ecors)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-186">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="a9ea5-187">Il codice nella sezione successiva incontra l'elenco precedente.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-187">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="a9ea5-188">Vedere [Test CORS](#testc) per istruzioni sul test di codice simile al codice precedente.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-188">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="a9ea5-189">Disabilita CORS</span><span class="sxs-lookup"><span data-stu-id="a9ea5-189">Disable CORS</span></span>

<span data-ttu-id="a9ea5-190">L'attributo [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ***non*** disabilita CORS abilitato dal [routing degli endpoint.](#ecors)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-190">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="a9ea5-191">Il codice seguente definisce `"MyPolicy"`i criteri CORS :</span><span class="sxs-lookup"><span data-stu-id="a9ea5-191">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="a9ea5-192">Il codice seguente disabilita CORS per l'azione: `GetValues2`</span><span class="sxs-lookup"><span data-stu-id="a9ea5-192">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="a9ea5-193">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-193">The preceding code:</span></span>

* <span data-ttu-id="a9ea5-194">Non abilita CORS con [routing endpoint](#ecors).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-194">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="a9ea5-195">Non definisce un [criterio CORS predefinito.](#dp)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-195">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="a9ea5-196">Utilizza [[EnableCors("MyPolicy")]](#attr) per `"MyPolicy"` abilitare i criteri CORS per il controller.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-196">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="a9ea5-197">Disabilita CORS per `GetValues2` il metodo.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-197">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="a9ea5-198">Vedere [Test CORS](#testc) per istruzioni sul test del codice precedente.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-198">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="a9ea5-199">Opzioni dei criteri CORS</span><span class="sxs-lookup"><span data-stu-id="a9ea5-199">CORS policy options</span></span>

<span data-ttu-id="a9ea5-200">In questa sezione vengono descritte le varie opzioni che è possibile impostare in un criterio CORS:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-200">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="a9ea5-201">Impostare le origini consentite</span><span class="sxs-lookup"><span data-stu-id="a9ea5-201">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="a9ea5-202">Impostare i metodi HTTP consentiti</span><span class="sxs-lookup"><span data-stu-id="a9ea5-202">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="a9ea5-203">Impostare le intestazioni di richiesta consentite</span><span class="sxs-lookup"><span data-stu-id="a9ea5-203">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="a9ea5-204">Impostare le intestazioni delle risposte esposte</span><span class="sxs-lookup"><span data-stu-id="a9ea5-204">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="a9ea5-205">Credenziali nelle richieste tra originiCredentials in cross-origin requests</span><span class="sxs-lookup"><span data-stu-id="a9ea5-205">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="a9ea5-206">Impostare l'ora di scadenza preliminare</span><span class="sxs-lookup"><span data-stu-id="a9ea5-206">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="a9ea5-207"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>viene chiamato `Startup.ConfigureServices`in .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-207"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a9ea5-208">Per alcune opzioni, può essere utile leggere prima la sezione [Come CORS funziona.](#how-cors)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-208">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="a9ea5-209">Impostare le origini consentite</span><span class="sxs-lookup"><span data-stu-id="a9ea5-209">Set the allowed origins</span></span>

<span data-ttu-id="a9ea5-210"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Consente le richieste CORS da tutte`http` `https`le origini con qualsiasi schema ( o ).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-210"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="a9ea5-211">`AllowAnyOrigin`non è sicuro perché *qualsiasi sito Web* può effettuare richieste cross-origin all'app.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-211">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="a9ea5-212">Specificando `AllowAnyOrigin` ed `AllowCredentials` è una configurazione non sicura e può comportare la falsificazione di richieste intersito.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-212">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="a9ea5-213">Il servizio CORS restituisce una risposta CORS non valida quando un'app è configurata con entrambi i metodi.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-213">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="a9ea5-214">`AllowAnyOrigin`influisce sulle richieste `Access-Control-Allow-Origin` di verifica preliminare e sull'intestazione.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-214">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="a9ea5-215">Per altre informazioni, vedere la sezione [Richieste di verifica preliminare.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-215">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="a9ea5-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Imposta <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> la proprietà del criterio in modo che sia una funzione che consente alle origini di corrispondere a un dominio con caratteri jolly configurato durante la valutazione se l'origine è consentita.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="a9ea5-217">Impostare i metodi HTTP consentiti</span><span class="sxs-lookup"><span data-stu-id="a9ea5-217">Set the allowed HTTP methods</span></span>

<span data-ttu-id="a9ea5-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="a9ea5-219">Consente qualsiasi metodo HTTP:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-219">Allows any HTTP method:</span></span>
* <span data-ttu-id="a9ea5-220">Influisce sulle richieste `Access-Control-Allow-Methods` di verifica preliminare e sull'intestazione.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-220">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="a9ea5-221">Per altre informazioni, vedere la sezione [Richieste di verifica preliminare.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-221">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="a9ea5-222">Impostare le intestazioni di richiesta consentite</span><span class="sxs-lookup"><span data-stu-id="a9ea5-222">Set the allowed request headers</span></span>

<span data-ttu-id="a9ea5-223">Per consentire l'invio di intestazioni specifiche in una richiesta <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> CORS, denominate intestazioni di richiesta [author](https://xhr.spec.whatwg.org/#request), chiamare e specificare le intestazioni consentite:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-223">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="a9ea5-224">Per consentire tutte le <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>intestazioni di [richiesta dell'autore,](https://www.w3.org/TR/cors/#author-request-headers)chiamare :</span><span class="sxs-lookup"><span data-stu-id="a9ea5-224">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="a9ea5-225">`AllowAnyHeader`influisce sulle richieste di verifica preliminare e sull'intestazione [Access-Control-Request-Headers.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-225">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="a9ea5-226">Per altre informazioni, vedere la sezione [Richieste di verifica preliminare.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-226">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="a9ea5-227">Un criterio del middleware CORS `WithHeaders` corrispondente a intestazioni specifiche `Access-Control-Request-Headers` specificate da è `WithHeaders`possibile solo quando le intestazioni inviate corrispondono esattamente alle intestazioni indicate in .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-227">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="a9ea5-228">Ad esempio, si consideri un'app configurata come segue:For example, consider an app configured as follows:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-228">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="a9ea5-229">IL middleware CORS rifiuta una richiesta di `Content-Language` verifica preliminare con la seguente intestazione di richiesta perché ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) non è elencato in `WithHeaders`:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-229">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="a9ea5-230">L'app restituisce una risposta *200 OK* ma non restituisce le intestazioni CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-230">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="a9ea5-231">Pertanto, il browser non tenta la richiesta cross-origin.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-231">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="a9ea5-232">Impostare le intestazioni delle risposte esposte</span><span class="sxs-lookup"><span data-stu-id="a9ea5-232">Set the exposed response headers</span></span>

<span data-ttu-id="a9ea5-233">Per impostazione predefinita, il browser non espone tutte le intestazioni di risposta all'app.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-233">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="a9ea5-234">Per ulteriori informazioni, vedere [W3C Cross-Origin Resource Sharing (Terminologia): Intestazione di risposta semplice](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-234">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="a9ea5-235">Le intestazioni di risposta disponibili per impostazione predefinita sono:The response headers that are available by default are:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-235">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="a9ea5-236">La specifica CORS chiama queste intestazioni intestazioni di *risposta semplici*.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-236">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="a9ea5-237">Per rendere disponibili altre intestazioni <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>per l'app, chiamare :</span><span class="sxs-lookup"><span data-stu-id="a9ea5-237">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="a9ea5-238">Credenziali nelle richieste tra originiCredentials in cross-origin requests</span><span class="sxs-lookup"><span data-stu-id="a9ea5-238">Credentials in cross-origin requests</span></span>

<span data-ttu-id="a9ea5-239">Le credenziali richiedono una gestione speciale in una richiesta CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-239">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="a9ea5-240">Per impostazione predefinita, il browser non invia credenziali con una richiesta cross-origin.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-240">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="a9ea5-241">Le credenziali includono cookie e schemi di autenticazione HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-241">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="a9ea5-242">Per inviare le credenziali con una richiesta `XMLHttpRequest.withCredentials` tra `true`origini diverse, il client deve impostare su .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-242">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="a9ea5-243">Utilizzando `XMLHttpRequest` direttamente:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-243">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="a9ea5-244">Utilizzo di jQuery:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-244">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="a9ea5-245">Utilizzo [dell'API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="a9ea5-245">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="a9ea5-246">Il server deve consentire le credenziali.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-246">The server must allow the credentials.</span></span> <span data-ttu-id="a9ea5-247">Per consentire le credenziali di <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>origine incrociata, chiamare :</span><span class="sxs-lookup"><span data-stu-id="a9ea5-247">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="a9ea5-248">La risposta HTTP `Access-Control-Allow-Credentials` include un'intestazione, che indica al browser che il server consente le credenziali per una richiesta tra origini diverse.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-248">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="a9ea5-249">Se il browser invia le credenziali ma la `Access-Control-Allow-Credentials` risposta non include un'intestazione valida, il browser non espone la risposta all'app e la richiesta tra origini ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-249">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="a9ea5-250">Consentire credenziali tra origini è un rischio per la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-250">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="a9ea5-251">Un sito Web in un altro dominio può inviare le credenziali di un utente connesso all'app per conto dell'utente all'insaputa dell'utente.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-251">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="a9ea5-252">La specifica CORS indica inoltre `"*"` che l'impostazione delle `Access-Control-Allow-Credentials` origini (tutte le origini) non è valida se l'intestazione è presente.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-252">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="a9ea5-253">Richieste di verifica preliminare</span><span class="sxs-lookup"><span data-stu-id="a9ea5-253">Preflight requests</span></span>

<span data-ttu-id="a9ea5-254">Per alcune richieste CORS, il browser invia una richiesta [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) aggiuntiva prima di effettuare la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-254">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="a9ea5-255">Questa richiesta viene definita [richiesta di verifica preliminare.](https://developer.mozilla.org/docs/Glossary/Preflight_request)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-255">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="a9ea5-256">Il browser può ignorare la richiesta di verifica preliminare se ***tutte*** le seguenti condizioni sono vere:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-256">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="a9ea5-257">Il metodo di richiesta è GET, HEAD o POST.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-257">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="a9ea5-258">L'app non imposta intestazioni `Accept`di `Accept-Language` `Content-Language`richiesta `Content-Type`diverse `Last-Event-ID`da , , , o .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-258">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="a9ea5-259">L'intestazione, `Content-Type` se impostata, ha uno dei seguenti valori:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-259">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="a9ea5-260">La regola sulle intestazioni di richiesta impostate per la `setRequestHeader` richiesta `XMLHttpRequest` client si applica alle intestazioni impostate dall'app chiamando sull'oggetto.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-260">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="a9ea5-261">La specifica CORS chiama queste intestazioni [intestazioni di richiesta dell'autore.](https://www.w3.org/TR/cors/#author-request-headers)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-261">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="a9ea5-262">La regola non si applica alle intestazioni che `User-Agent` `Host`il `Content-Length`browser può impostare, ad esempio , o .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-262">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="a9ea5-263">Di seguito è riportata una risposta di esempio simile alla richiesta di verifica preliminare effettuata dal pulsante **[Put test]** nella sezione [Test CORS](#testc) di questo documento.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-263">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
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

<span data-ttu-id="a9ea5-264">La richiesta di verifica preliminare utilizza il metodo [HTTP OPTIONS.](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-264">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="a9ea5-265">Può includere le seguenti intestazioni:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-265">It may include the following headers:</span></span>

* <span data-ttu-id="a9ea5-266">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): il metodo HTTP che verrà utilizzato per la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-266">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="a9ea5-267">[Access-Control-Request-Headers:](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers)elenco di intestazioni di richiesta impostate dall'app nella richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-267">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="a9ea5-268">Come indicato in precedenza, non sono incluse le `User-Agent`intestazioni impostate dal browser, ad esempio .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-268">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="a9ea5-269">Access-Control-Allow-Methods</span><span class="sxs-lookup"><span data-stu-id="a9ea5-269">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="a9ea5-270">Se la richiesta di verifica preliminare `200 OK` viene negata, l'app restituisce una risposta ma non imposta le intestazioni CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-270">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="a9ea5-271">Pertanto, il browser non tenta la richiesta cross-origin.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-271">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="a9ea5-272">Per un esempio di richiesta di verifica preliminare negata, vedere la sezione [Test CORS](#testc) di questo documento.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-272">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="a9ea5-273">Utilizzando gli strumenti F12, l'applicazione console mostra un errore simile a uno dei seguenti, a seconda del browser:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-273">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="a9ea5-274">Firefox: Richiesta cross-origin bloccata: la stessa politica di `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`origine non consente la lettura della risorsa remota all'indirizzo .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-274">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="a9ea5-275">(Motivo: richiesta CORS non riuscita).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-275">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="a9ea5-276">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="a9ea5-276">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="a9ea5-277">Basato su chromium:https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5l'accessohttps://cors3.azurewebsites.netal recupero a ' dall'origine ' è stato bloccato dai criteri CORS: la risposta alla richiesta di verifica preliminare non supera il controllo di accesso: non è presente alcuna intestazione 'Access-Control-Allow-Origin' sulla risorsa richiesta.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-277">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="a9ea5-278">Se si ritiene soddisfacente una risposta opaca, impostare la modalità della richiesta 'no-cors' per recuperare la risorsa con CORS disabilitato.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-278">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="a9ea5-279">Per consentire intestazioni <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>specifiche, chiamare :</span><span class="sxs-lookup"><span data-stu-id="a9ea5-279">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="a9ea5-280">Per consentire tutte le <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>intestazioni di [richiesta dell'autore,](https://www.w3.org/TR/cors/#author-request-headers)chiamare :</span><span class="sxs-lookup"><span data-stu-id="a9ea5-280">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="a9ea5-281">I browser non sono coerenti `Access-Control-Request-Headers`nel modo in cui impostano .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-281">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="a9ea5-282">Se uno dei due:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-282">If either:</span></span>

* <span data-ttu-id="a9ea5-283">Le intestazioni sono impostate su un valore diverso da`"*"`</span><span class="sxs-lookup"><span data-stu-id="a9ea5-283">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="a9ea5-284"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>è chiamato: includere `Accept` `Content-Type`almeno `Origin`, e , oltre a tutte le intestazioni personalizzate che si desidera supportare.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-284"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="a9ea5-285">Codice di richiesta di verifica preliminare automatica</span><span class="sxs-lookup"><span data-stu-id="a9ea5-285">Automatic preflight request code</span></span>

<span data-ttu-id="a9ea5-286">Quando viene applicato il criterio CORS:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-286">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="a9ea5-287">Globalmente chiamando `app.UseCors` `Startup.Configure`in .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-287">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="a9ea5-288">Utilizzo `[EnableCors]` dell'attributo .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-288">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="a9ea5-289">ASP.NET Core risponde alla richiesta OPTIONS di verifica preliminare.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-289">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="a9ea5-290">L'abilitazione di CORS `RequireCors` in base all'endpoint ***non*** supporta attualmente le richieste di verifica preliminare automatiche.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-290">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="a9ea5-291">La sezione [Test CORS](#testc) di questo documento illustra questo comportamento.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-291">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="a9ea5-292">Attributo [HttpOptions] per le richieste di verifica preliminare</span><span class="sxs-lookup"><span data-stu-id="a9ea5-292">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="a9ea5-293">Quando CORS è abilitato con il criterio appropriato, ASP.NET Core risponde in genere automaticamente alle richieste di verifica preliminare CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-293">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="a9ea5-294">In alcuni scenari, questo potrebbe non essere il caso.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-294">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="a9ea5-295">Ad esempio, utilizzando [CORS con il routing degli endpoint](#ecors).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-295">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="a9ea5-296">Il codice seguente usa l'attributo [HttpOptions] per creare endpoint per le richieste OPTIONS:The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-296">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="a9ea5-297">Vedere [Test CORS con routing degli endpoint e [HttpOptions]](#tcer) per istruzioni sul test del codice precedente.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-297">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="a9ea5-298">Impostare l'ora di scadenza preliminare</span><span class="sxs-lookup"><span data-stu-id="a9ea5-298">Set the preflight expiration time</span></span>

<span data-ttu-id="a9ea5-299">L'intestazione `Access-Control-Max-Age` specifica per quanto tempo la risposta alla richiesta di verifica preliminare può essere memorizzata nella cache.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-299">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="a9ea5-300">Per impostare questa <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>intestazione, chiamare :</span><span class="sxs-lookup"><span data-stu-id="a9ea5-300">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="a9ea5-301">Come funziona CORS</span><span class="sxs-lookup"><span data-stu-id="a9ea5-301">How CORS works</span></span>

<span data-ttu-id="a9ea5-302">In questa sezione viene descritto cosa accade in una richiesta [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) a livello dei messaggi HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-302">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="a9ea5-303">CORS **non** è una funzione di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-303">CORS is **not** a security feature.</span></span> <span data-ttu-id="a9ea5-304">CORS è uno standard W3C che consente a un server di ridurre i criteri della stessa origine.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-304">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="a9ea5-305">Ad esempio, un attore malintenzionato potrebbe utilizzare [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) sul tuo sito ed eseguire una richiesta intersito al loro sito abilitato CORS per rubare informazioni.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-305">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="a9ea5-306">Un'API non è più sicura consentendo CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-306">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="a9ea5-307">Spetta al client (browser) applicare CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-307">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="a9ea5-308">Il server esegue la richiesta e restituisce la risposta, è il client che restituisce un errore e blocca la risposta.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-308">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="a9ea5-309">Ad esempio, uno dei seguenti strumenti visualizzerà la risposta del server:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-309">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="a9ea5-310">Fiddler</span><span class="sxs-lookup"><span data-stu-id="a9ea5-310">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="a9ea5-311">Postman</span><span class="sxs-lookup"><span data-stu-id="a9ea5-311">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="a9ea5-312">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="a9ea5-312">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="a9ea5-313">Un browser Web immettendo l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-313">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="a9ea5-314">È un modo per un server per consentire ai browser di eseguire una richiesta [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) o [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) tra origini che altrimenti sarebbe vietata.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-314">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="a9ea5-315">I browser senza CORS non possono eseguire richieste cross-origin.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-315">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="a9ea5-316">Prima di CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) veniva usato per aggirare questa restrizione.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-316">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="a9ea5-317">JSONP non usa XHR, usa `<script>` il tag per ricevere la risposta.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-317">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="a9ea5-318">Gli script possono essere caricati tra origini diverse.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-318">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="a9ea5-319">La [specifica CORS](https://www.w3.org/TR/cors/) ha introdotto diverse nuove intestazioni HTTP che abilitano le richieste tra origini diverse.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-319">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="a9ea5-320">Se un browser supporta CORS, imposta automaticamente queste intestazioni per le richieste tra origini diverse.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-320">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="a9ea5-321">Il codice JavaScript personalizzato non è necessario per abilitare CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-321">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="a9ea5-322">Il [pulsante test PUT](https://cors3.azurewebsites.net/test) nell'esempio distribuito [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-322">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="a9ea5-323">Di seguito è riportato un esempio [Values](https://cors3.azurewebsites.net/) di richiesta `https://cors1.azurewebsites.net/api/values`tra origini dal pulsante di test Valori a .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-323">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="a9ea5-324">L'intestazione: `Origin`</span><span class="sxs-lookup"><span data-stu-id="a9ea5-324">The `Origin` header:</span></span>

* <span data-ttu-id="a9ea5-325">Fornisce il dominio del sito che effettua la richiesta.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-325">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="a9ea5-326">È obbligatorio e deve essere diverso dall'host.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-326">Is required and must be different from the host.</span></span>

<span data-ttu-id="a9ea5-327">**Intestazioni generali**</span><span class="sxs-lookup"><span data-stu-id="a9ea5-327">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="a9ea5-328">**Intestazioni di risposta**</span><span class="sxs-lookup"><span data-stu-id="a9ea5-328">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="a9ea5-329">**Intestazioni della richiesta**</span><span class="sxs-lookup"><span data-stu-id="a9ea5-329">**Request headers**</span></span>

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

<span data-ttu-id="a9ea5-330">Nelle `OPTIONS` richieste, il server imposta l'intestazione **di intestazioni** `Access-Control-Allow-Origin: {allowed origin}` di risposta nella risposta.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-330">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="a9ea5-331">Ad esempio, la richiesta del pulsante `OPTIONS` [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) di [esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)distribuita contiene le intestazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-331">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="a9ea5-332">**Intestazioni generali**</span><span class="sxs-lookup"><span data-stu-id="a9ea5-332">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="a9ea5-333">**Intestazioni di risposta**</span><span class="sxs-lookup"><span data-stu-id="a9ea5-333">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="a9ea5-334">**Intestazioni della richiesta**</span><span class="sxs-lookup"><span data-stu-id="a9ea5-334">**Request headers**</span></span>

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

<span data-ttu-id="a9ea5-335">Nelle intestazioni **Di risposta**precedenti, il server imposta l'intestazione [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) nella risposta.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-335">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="a9ea5-336">Il `https://cors1.azurewebsites.net` valore di questa `Origin` intestazione corrisponde all'intestazione della richiesta.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-336">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="a9ea5-337">Se <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> viene chiamato, viene restituito il `Access-Control-Allow-Origin: *`valore con caratteri jolly .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-337">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="a9ea5-338">`AllowAnyOrigin`permette qualsiasi origine.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-338">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="a9ea5-339">Se la risposta non `Access-Control-Allow-Origin` include l'intestazione, la richiesta cross-origin ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-339">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="a9ea5-340">In particolare, il browser non consente la richiesta.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-340">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="a9ea5-341">Anche se il server restituisce una risposta corretta, il browser non rende disponibile la risposta all'app client.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-341">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="a9ea5-342">Visualizza richieste OPTIONS</span><span class="sxs-lookup"><span data-stu-id="a9ea5-342">Display OPTIONS requests</span></span>

<span data-ttu-id="a9ea5-343">Per impostazione predefinita, i browser Chrome ed Edge non mostrano le richieste OPTIONS nella scheda rete degli strumenti F12.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-343">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="a9ea5-344">Per visualizzare le richieste OPTIONS in questi browser:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-344">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="a9ea5-345">`chrome://flags/#out-of-blink-cors` o `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="a9ea5-345">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="a9ea5-346">disattivare il flag.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-346">disable the flag.</span></span>
* <span data-ttu-id="a9ea5-347">Riavvia.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-347">restart.</span></span>

<span data-ttu-id="a9ea5-348">Firefox mostra le richieste OPTIONS per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-348">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="a9ea5-349">CORS in IIS</span><span class="sxs-lookup"><span data-stu-id="a9ea5-349">CORS in IIS</span></span>

<span data-ttu-id="a9ea5-350">Durante la distribuzione in IIS, CORS deve essere eseguito prima dell'autenticazione di Windows se il server non è configurato per consentire l'accesso anonimo.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-350">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="a9ea5-351">Per supportare questo scenario, il [modulo IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) deve essere installato e configurato per l'app.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-351">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="a9ea5-352">Testare CORS</span><span class="sxs-lookup"><span data-stu-id="a9ea5-352">Test CORS</span></span>

<span data-ttu-id="a9ea5-353">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) include codice per testare CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="a9ea5-354">Vedere [come scaricare un esempio](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-354">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="a9ea5-355">L'esempio è un progetto API con pagine Razor aggiunte:The sample is an API project with Razor Pages added:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-355">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="a9ea5-356">`WithOrigins("https://localhost:<port>");`deve essere usato solo per testare un'app di esempio simile al [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)scaricato.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-356">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="a9ea5-357">Di `ValuesController` seguito sono disponibili gli endpoint per il test:The following provides the endpoints for testing:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-357">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="a9ea5-358">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) viene fornito dal pacchetto [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet e visualizza le informazioni sulla route.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-358">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="a9ea5-359">Testare il codice di esempio precedente utilizzando uno degli approcci seguenti:Test the preceding sample code by using one of the following approaches:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-359">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="a9ea5-360">Usare l'app [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)di esempio distribuita in .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-360">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="a9ea5-361">Non è necessario scaricare l'esempio.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-361">There is no need to download the sample.</span></span>
* <span data-ttu-id="a9ea5-362">Eseguire l'esempio utilizzando `dotnet run` l'URL predefinito di `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-362">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="a9ea5-363">Eseguire l'esempio da Visual Studio con la porta impostata `https://localhost:44398`su 44398 per un URL di .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-363">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="a9ea5-364">Utilizzando un browser con gli strumenti F12:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-364">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="a9ea5-365">Selezionare il pulsante **Valori** ed esaminare le intestazioni nella scheda **Rete.**</span><span class="sxs-lookup"><span data-stu-id="a9ea5-365">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="a9ea5-366">Selezionare il pulsante **TEST PUT.**</span><span class="sxs-lookup"><span data-stu-id="a9ea5-366">Select the **PUT test** button.</span></span> <span data-ttu-id="a9ea5-367">Vedere [Visualizzare le richieste OPTIONS](#options) per istruzioni sulla visualizzazione della richiesta OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-367">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="a9ea5-368">Il **test PUT** crea due richieste, una richiesta di verifica preliminare OPTIONS e la richiesta PUT.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-368">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="a9ea5-369">Selezionare **`GetValues2 [DisableCors]`** il pulsante per attivare una richiesta CORS non riuscita.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-369">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="a9ea5-370">Come accennato nel documento, la risposta restituisce 200 successo, ma la richiesta CORS non viene effettuata.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-370">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="a9ea5-371">Selezionare la scheda **Console** per visualizzare l'errore CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-371">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="a9ea5-372">A seconda del browser, viene visualizzato un errore simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-372">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="a9ea5-373">L'accesso `'https://cors1.azurewebsites.net/api/values/GetValues2'` per `'https://cors3.azurewebsites.net'` recuperare dall'origine è stato bloccato dai criteri CORS: non è presente alcuna intestazione 'Access-Control-Allow-Origin' sulla risorsa richiesta.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-373">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="a9ea5-374">Se si ritiene soddisfacente una risposta opaca, impostare la modalità della richiesta 'no-cors' per recuperare la risorsa con CORS disabilitato.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-374">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="a9ea5-375">Gli endpoint abilitati per CORS possono essere testati con uno strumento, ad esempio [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)o [Postman](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-375">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="a9ea5-376">Quando si utilizza uno strumento, l'origine della richiesta specificata dall'intestazione `Origin` deve essere diversa dall'host che riceve la richiesta.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-376">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="a9ea5-377">Se la richiesta non è *di origine incrociata* in base al valore dell'intestazione: `Origin`</span><span class="sxs-lookup"><span data-stu-id="a9ea5-377">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="a9ea5-378">Non è necessario che il middleware CORS elabori la richiesta.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-378">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="a9ea5-379">Le intestazioni CORS non vengono restituite nella risposta.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-379">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="a9ea5-380">Il comando `curl` seguente utilizza per inviare una richiesta OPTIONS con informazioni:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-380">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="a9ea5-381">Test corS con routing degli endpoint e [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="a9ea5-381">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="a9ea5-382">L'abilitazione di CORS `RequireCors` in base all'endpoint che utilizza attualmente ***non*** supporta le richieste automatiche di [verifica preliminare.](#apf)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-382">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="a9ea5-383">Si consideri il codice seguente che utilizza il [routing degli endpoint per abilitare CORS:](#ecors)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-383">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="a9ea5-384">Di `TodoItems1Controller` seguito sono disponibili gli endpoint per il test:The following provides endpoints for testing:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-384">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="a9ea5-385">Testare il codice precedente dalla pagina di [test](https://cors1.azurewebsites.net/test?number=1) [dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)distribuito.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-385">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="a9ea5-386">I pulsanti **Elimina [EnableCors]** e **GET [EnableCors]** hanno esito positivo, perché gli endpoint hanno `[EnableCors]` e rispondono alle richieste di verifica preliminare.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-386">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="a9ea5-387">Gli altri endpoint hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-387">The other endpoints fails.</span></span> <span data-ttu-id="a9ea5-388">Il pulsante **GET** ha esito negativo perché [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) invia:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-388">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="a9ea5-389">Di `TodoItems2Controller` seguito vengono forniti endpoint simili, ma viene fornito codice esplicito per rispondere alle richieste OPTIONS:The following provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-389">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="a9ea5-390">Testare il codice precedente dalla pagina di [test](https://cors1.azurewebsites.net/test?number=2) dell'esempio distribuito.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-390">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="a9ea5-391">Nell'elenco a discesa **Controller** selezionare **Verifica preliminare** e quindi **Imposta controller**.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-391">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="a9ea5-392">Tutte le chiamate CORS agli `TodoItems2Controller` endpoint hanno esito positivo.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-392">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a9ea5-393">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a9ea5-393">Additional resources</span></span>

* [<span data-ttu-id="a9ea5-394">Condivisione risorse tra le origini (CORS)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-394">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="a9ea5-395">Introduzione al modulo IIS CORS</span><span class="sxs-lookup"><span data-stu-id="a9ea5-395">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a9ea5-396">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-396">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a9ea5-397">Questo articolo illustra come abilitare CORS in un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-397">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="a9ea5-398">La sicurezza del browser impedisce a una pagina Web di effettuare richieste a un dominio diverso da quello che ha servito la pagina web.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-398">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="a9ea5-399">Questa restrizione è *denominata criterio*della stessa origine .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-399">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="a9ea5-400">I criteri della stessa origine impediscono a un sito dannoso di leggere dati sensibili da un altro sito.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-400">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="a9ea5-401">In alcuni altri punti, potresti voler consentire ad altri siti di effettuare richieste cross-origin alla tua app.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-401">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="a9ea5-402">Per ulteriori informazioni, vedere l'articolo di [Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-402">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="a9ea5-403">[Condivisione delle risorse tra origini](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="a9ea5-403">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="a9ea5-404">È uno standard W3C che consente a un server di ridurre la stessa origine.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-404">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="a9ea5-405">**Non** è una funzione di sicurezza, CORS rilassa la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-405">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="a9ea5-406">Un'API non è più sicura consentendo CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-406">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="a9ea5-407">Per ulteriori informazioni, consultate [Funzionamento di CORS.](#how-cors)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-407">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="a9ea5-408">Consente a un server di consentire in modo esplicito alcune richieste tra origini rifiutandone altre.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-408">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="a9ea5-409">È più sicuro e più flessibile rispetto alle tecniche precedenti, ad esempio [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-409">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="a9ea5-410">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a9ea5-410">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="a9ea5-411">Stessa origine</span><span class="sxs-lookup"><span data-stu-id="a9ea5-411">Same origin</span></span>

<span data-ttu-id="a9ea5-412">Due URL hanno la stessa origine se hanno schemi, host e porte identici ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-412">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="a9ea5-413">Questi due URL hanno la stessa origine:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-413">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="a9ea5-414">Questi URL hanno origini diverse rispetto ai due URL precedenti:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-414">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="a9ea5-415">`https://example.net`&ndash; Dominio diverso</span><span class="sxs-lookup"><span data-stu-id="a9ea5-415">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="a9ea5-416">`https://www.example.com/foo.html`&ndash; Sottodominio diverso</span><span class="sxs-lookup"><span data-stu-id="a9ea5-416">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="a9ea5-417">`http://example.com/foo.html`&ndash; Schema diverso</span><span class="sxs-lookup"><span data-stu-id="a9ea5-417">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="a9ea5-418">`https://example.com:9000/foo.html`&ndash; Porta diversa</span><span class="sxs-lookup"><span data-stu-id="a9ea5-418">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="a9ea5-419">Internet Explorer non considera la porta quando si confrontano le origini.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-419">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="a9ea5-420">CORS con criteri denominati e middleware</span><span class="sxs-lookup"><span data-stu-id="a9ea5-420">CORS with named policy and middleware</span></span>

<span data-ttu-id="a9ea5-421">IL middleware CORS gestisce le richieste tra origini.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-421">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="a9ea5-422">Il codice seguente abilita CORS per l'intera app con l'origine specificata:The following code enables CORS for the entire app with the specified origin:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-422">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="a9ea5-423">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-423">The preceding code:</span></span>

* <span data-ttu-id="a9ea5-424">Imposta il nome\_del criterio su " myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="a9ea5-424">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="a9ea5-425">Il nome del criterio è arbitrario.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-425">The policy name is arbitrary.</span></span>
* <span data-ttu-id="a9ea5-426">Chiama <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> il metodo di estensione, che abilita CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-426">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="a9ea5-427">Chiamate <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> con [un'espressione lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-427">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="a9ea5-428">L'espressione <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> lambda accetta un oggetto.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-428">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="a9ea5-429">[Le opzioni](#cors-policy-options)di `WithOrigins`configurazione, ad esempio , sono descritte più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-429">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="a9ea5-430">La <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chiamata al metodo aggiunge servizi CORS al contenitore dei servizi dell'app:The method call adds CORS services to the app's service container:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-430">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="a9ea5-431">Per ulteriori informazioni, vedere [Opzioni dei criteri CORS](#cpo) in questo documento .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-431">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="a9ea5-432">Il <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> metodo può concatenare i metodi, come illustrato nel codice seguente:The method can chain methods, as shown in the following code:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-432">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="a9ea5-433">Nota: l'URL **non** deve`/`contenere una barra finale ( ).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-433">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="a9ea5-434">Se l'URL `/`termina con `false` , il confronto restituisce e non viene restituita alcuna intestazione.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-434">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="a9ea5-435">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-435">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="a9ea5-436">Nota: `UseCors` deve `UseMvc`essere chiamato prima di .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-436">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="a9ea5-437">Vedere [Abilitare CORS in pagine Razor, controller e metodi](#ecors) di azione per applicare i criteri CORS a livello di pagina/controller/azione.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-437">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="a9ea5-438">Vedere [Test CORS](#test) per istruzioni sul test di codice simile al codice precedente.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-438">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="a9ea5-439">Abilitare CORS con attributi</span><span class="sxs-lookup"><span data-stu-id="a9ea5-439">Enable CORS with attributes</span></span>

<span data-ttu-id="a9ea5-440">L'attributo [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornisce un'alternativa all'applicazione di CORS a livello globale.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-440">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="a9ea5-441">L'attributo `[EnableCors]` abilita CORS per i punti finali selezionati, anziché per tutti i punti finali.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-441">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="a9ea5-442">Consente `[EnableCors]` di specificare `[EnableCors("{Policy String}")]` il criterio predefinito e di specificare un criterio.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-442">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="a9ea5-443">L'attributo `[EnableCors]` può essere applicato a:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-443">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="a9ea5-444">Pagina Razor`PageModel`</span><span class="sxs-lookup"><span data-stu-id="a9ea5-444">Razor Page `PageModel`</span></span>
* <span data-ttu-id="a9ea5-445">Controller</span><span class="sxs-lookup"><span data-stu-id="a9ea5-445">Controller</span></span>
* <span data-ttu-id="a9ea5-446">Metodo di azione del controller</span><span class="sxs-lookup"><span data-stu-id="a9ea5-446">Controller action method</span></span>

<span data-ttu-id="a9ea5-447">È possibile applicare criteri diversi a controller/modello `[EnableCors]` di pagina/azione con l'attributo .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-447">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="a9ea5-448">Quando `[EnableCors]` l'attributo viene applicato a un metodo di azione/modello di pagina/controller e CORS è abilitato nel middleware, vengono applicati ***entrambi i*** criteri.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-448">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="a9ea5-449">Si consiglia di ***non*** combinare le politiche.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-449">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="a9ea5-450">Utilizzare `[EnableCors]` l'attributo o il middleware,**non entrambi**.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-450">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="a9ea5-451">Quando `[EnableCors]`si utilizza , **non** definire un criterio predefinito.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-451">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="a9ea5-452">Il codice seguente applica criteri diversi a ogni metodo:The following code applies a different policy to each method:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-452">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="a9ea5-453">Il codice seguente crea un criterio predefinito `"AnotherPolicy"`CORS e un criterio denominato :</span><span class="sxs-lookup"><span data-stu-id="a9ea5-453">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="a9ea5-454">Disabilita CORS</span><span class="sxs-lookup"><span data-stu-id="a9ea5-454">Disable CORS</span></span>

<span data-ttu-id="a9ea5-455">L'attributo [ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) disabilita CORS per il controller/modello di pagina/azione.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-455">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="a9ea5-456">Opzioni dei criteri CORS</span><span class="sxs-lookup"><span data-stu-id="a9ea5-456">CORS policy options</span></span>

<span data-ttu-id="a9ea5-457">In questa sezione vengono descritte le varie opzioni che è possibile impostare in un criterio CORS:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-457">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="a9ea5-458">Impostare le origini consentite</span><span class="sxs-lookup"><span data-stu-id="a9ea5-458">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="a9ea5-459">Impostare i metodi HTTP consentiti</span><span class="sxs-lookup"><span data-stu-id="a9ea5-459">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="a9ea5-460">Impostare le intestazioni di richiesta consentite</span><span class="sxs-lookup"><span data-stu-id="a9ea5-460">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="a9ea5-461">Impostare le intestazioni delle risposte esposte</span><span class="sxs-lookup"><span data-stu-id="a9ea5-461">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="a9ea5-462">Credenziali nelle richieste tra originiCredentials in cross-origin requests</span><span class="sxs-lookup"><span data-stu-id="a9ea5-462">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="a9ea5-463">Impostare l'ora di scadenza preliminare</span><span class="sxs-lookup"><span data-stu-id="a9ea5-463">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="a9ea5-464"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>viene chiamato `Startup.ConfigureServices`in .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-464"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a9ea5-465">Per alcune opzioni, può essere utile leggere prima la sezione [Come CORS funziona.](#how-cors)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-465">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="a9ea5-466">Impostare le origini consentite</span><span class="sxs-lookup"><span data-stu-id="a9ea5-466">Set the allowed origins</span></span>

<span data-ttu-id="a9ea5-467"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Consente le richieste CORS da tutte`http` `https`le origini con qualsiasi schema ( o ).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-467"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="a9ea5-468">`AllowAnyOrigin`non è sicuro perché *qualsiasi sito Web* può effettuare richieste cross-origin all'app.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-468">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="a9ea5-469">Specificando `AllowAnyOrigin` ed `AllowCredentials` è una configurazione non sicura e può comportare la falsificazione di richieste intersito.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-469">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="a9ea5-470">Per un'app sicura, specificare un elenco esatto di origini se il client deve autorizzare se stesso ad accedere alle risorse del server.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-470">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="a9ea5-471">`AllowAnyOrigin`influisce sulle richieste `Access-Control-Allow-Origin` di verifica preliminare e sull'intestazione.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-471">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="a9ea5-472">Per altre informazioni, vedere la sezione [Richieste di verifica preliminare.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-472">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="a9ea5-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Imposta <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> la proprietà del criterio in modo che sia una funzione che consente alle origini di corrispondere a un dominio con caratteri jolly configurato durante la valutazione se l'origine è consentita.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="a9ea5-474">Impostare i metodi HTTP consentiti</span><span class="sxs-lookup"><span data-stu-id="a9ea5-474">Set the allowed HTTP methods</span></span>

<span data-ttu-id="a9ea5-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="a9ea5-476">Consente qualsiasi metodo HTTP:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-476">Allows any HTTP method:</span></span>
* <span data-ttu-id="a9ea5-477">Influisce sulle richieste `Access-Control-Allow-Methods` di verifica preliminare e sull'intestazione.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-477">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="a9ea5-478">Per altre informazioni, vedere la sezione [Richieste di verifica preliminare.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-478">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="a9ea5-479">Impostare le intestazioni di richiesta consentite</span><span class="sxs-lookup"><span data-stu-id="a9ea5-479">Set the allowed request headers</span></span>

<span data-ttu-id="a9ea5-480">Per consentire l'invio di intestazioni specifiche in una richiesta <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> CORS, denominate intestazioni di richiesta *author*, chiamare e specificare le intestazioni consentite:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-480">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="a9ea5-481">Per consentire tutte le <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>intestazioni di richiesta dell'autore, chiamare :</span><span class="sxs-lookup"><span data-stu-id="a9ea5-481">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="a9ea5-482">Questa impostazione influisce sulle `Access-Control-Request-Headers` richieste di verifica preliminare e sull'intestazione.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-482">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="a9ea5-483">Per altre informazioni, vedere la sezione [Richieste di verifica preliminare.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-483">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="a9ea5-484">IL middleware CORS consente `Access-Control-Request-Headers` sempre l'invio di quattro intestazioni nell'oggetto indipendentemente dai valori configurati in CorsPolicy.Headers.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-484">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="a9ea5-485">Questo elenco di intestazioni include:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-485">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="a9ea5-486">Ad esempio, si consideri un'app configurata come segue:For example, consider an app configured as follows:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-486">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="a9ea5-487">IL middleware CORS risponde correttamente a una richiesta `Content-Language` di verifica preliminare con l'intestazione della richiesta seguente perché è sempre inserita nella whitelist:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-487">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="a9ea5-488">Impostare le intestazioni delle risposte esposte</span><span class="sxs-lookup"><span data-stu-id="a9ea5-488">Set the exposed response headers</span></span>

<span data-ttu-id="a9ea5-489">Per impostazione predefinita, il browser non espone tutte le intestazioni di risposta all'app.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-489">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="a9ea5-490">Per ulteriori informazioni, vedere [W3C Cross-Origin Resource Sharing (Terminologia): Intestazione di risposta semplice](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-490">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="a9ea5-491">Le intestazioni di risposta disponibili per impostazione predefinita sono:The response headers that are available by default are:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-491">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="a9ea5-492">La specifica CORS chiama queste intestazioni intestazioni di *risposta semplici*.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-492">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="a9ea5-493">Per rendere disponibili altre intestazioni <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>per l'app, chiamare :</span><span class="sxs-lookup"><span data-stu-id="a9ea5-493">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="a9ea5-494">Credenziali nelle richieste tra originiCredentials in cross-origin requests</span><span class="sxs-lookup"><span data-stu-id="a9ea5-494">Credentials in cross-origin requests</span></span>

<span data-ttu-id="a9ea5-495">Le credenziali richiedono una gestione speciale in una richiesta CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-495">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="a9ea5-496">Per impostazione predefinita, il browser non invia credenziali con una richiesta cross-origin.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-496">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="a9ea5-497">Le credenziali includono cookie e schemi di autenticazione HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-497">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="a9ea5-498">Per inviare le credenziali con una richiesta `XMLHttpRequest.withCredentials` tra `true`origini diverse, il client deve impostare su .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-498">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="a9ea5-499">Utilizzando `XMLHttpRequest` direttamente:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-499">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="a9ea5-500">Utilizzo di jQuery:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-500">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="a9ea5-501">Utilizzo [dell'API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="a9ea5-501">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="a9ea5-502">Il server deve consentire le credenziali.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-502">The server must allow the credentials.</span></span> <span data-ttu-id="a9ea5-503">Per consentire le credenziali di <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>origine incrociata, chiamare :</span><span class="sxs-lookup"><span data-stu-id="a9ea5-503">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="a9ea5-504">La risposta HTTP `Access-Control-Allow-Credentials` include un'intestazione, che indica al browser che il server consente le credenziali per una richiesta tra origini diverse.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-504">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="a9ea5-505">Se il browser invia le credenziali ma la `Access-Control-Allow-Credentials` risposta non include un'intestazione valida, il browser non espone la risposta all'app e la richiesta tra origini ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-505">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="a9ea5-506">Consentire credenziali tra origini è un rischio per la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-506">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="a9ea5-507">Un sito Web in un altro dominio può inviare le credenziali di un utente connesso all'app per conto dell'utente all'insaputa dell'utente.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-507">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="a9ea5-508">La specifica CORS indica inoltre `"*"` che l'impostazione delle `Access-Control-Allow-Credentials` origini (tutte le origini) non è valida se l'intestazione è presente.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-508">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="a9ea5-509">Richieste di verifica preliminare</span><span class="sxs-lookup"><span data-stu-id="a9ea5-509">Preflight requests</span></span>

<span data-ttu-id="a9ea5-510">Per alcune richieste CORS, il browser invia una richiesta aggiuntiva prima di effettuare la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-510">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="a9ea5-511">Questa richiesta viene definita *richiesta di verifica preliminare.*</span><span class="sxs-lookup"><span data-stu-id="a9ea5-511">This request is called a *preflight request*.</span></span> <span data-ttu-id="a9ea5-512">Il browser può ignorare la richiesta di verifica preliminare se sono vere le seguenti condizioni:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-512">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="a9ea5-513">Il metodo di richiesta è GET, HEAD o POST.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-513">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="a9ea5-514">L'app non imposta intestazioni `Accept`di `Accept-Language` `Content-Language`richiesta `Content-Type`diverse `Last-Event-ID`da , , , o .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-514">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="a9ea5-515">L'intestazione, `Content-Type` se impostata, ha uno dei seguenti valori:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-515">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="a9ea5-516">La regola sulle intestazioni di richiesta impostate per la `setRequestHeader` richiesta `XMLHttpRequest` client si applica alle intestazioni impostate dall'app chiamando sull'oggetto.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-516">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="a9ea5-517">La specifica CORS chiama queste intestazioni *intestazioni di richiesta dell'autore.*</span><span class="sxs-lookup"><span data-stu-id="a9ea5-517">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="a9ea5-518">La regola non si applica alle intestazioni che `User-Agent` `Host`il `Content-Length`browser può impostare, ad esempio , o .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-518">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="a9ea5-519">Di seguito è riportato un esempio di richiesta di verifica preliminare:The following is an example of a preflight request:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-519">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="a9ea5-520">La richiesta di verifica preliminare utilizza il metodo HTTP OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-520">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="a9ea5-521">Include due intestazioni speciali:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-521">It includes two special headers:</span></span>

* <span data-ttu-id="a9ea5-522">`Access-Control-Request-Method`: il metodo HTTP che verrà utilizzato per la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-522">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="a9ea5-523">`Access-Control-Request-Headers`: un elenco di intestazioni di richiesta impostate dall'app nella richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-523">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="a9ea5-524">Come indicato in precedenza, non sono incluse le `User-Agent`intestazioni impostate dal browser, ad esempio .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-524">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="a9ea5-525">Quando CORS è abilitato con il criterio appropriato, ASP.NET Core risponde in genere automaticamente alle richieste di verifica preliminare CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-525">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="a9ea5-526">Vedere [l'attributo [HttpOptions] per le richieste di verifica preliminare](#pro).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-526">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="a9ea5-527">Una richiesta di verifica preliminare CORS può includere un'intestazione, `Access-Control-Request-Headers` che indica al server le intestazioni inviate con la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-527">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="a9ea5-528">Per consentire intestazioni <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>specifiche, chiamare :</span><span class="sxs-lookup"><span data-stu-id="a9ea5-528">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="a9ea5-529">Per consentire tutte le <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>intestazioni di richiesta dell'autore, chiamare :</span><span class="sxs-lookup"><span data-stu-id="a9ea5-529">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="a9ea5-530">I browser non sono del tutto `Access-Control-Request-Headers`coerenti nel modo in cui impostano .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-530">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="a9ea5-531">Se si impostano intestazioni `"*"` su <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>un valore diverso `Accept`da `Content-Type`(o utilizzare ), è necessario includere almeno , e `Origin`, oltre a tutte le intestazioni personalizzate che si desidera supportare.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-531">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="a9ea5-532">Di seguito è riportato un esempio di risposta alla richiesta di verifica preliminare (presupponendo che il server consenta la richiesta):</span><span class="sxs-lookup"><span data-stu-id="a9ea5-532">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="a9ea5-533">La risposta `Access-Control-Allow-Methods` include un'intestazione che elenca `Access-Control-Allow-Headers` i metodi consentiti e, facoltativamente, un'intestazione, che elenca le intestazioni consentite.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-533">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="a9ea5-534">Se la richiesta di verifica preliminare ha esito positivo, il browser invia la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-534">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="a9ea5-535">Se la richiesta di verifica preliminare viene negata, l'app restituisce una risposta *200 OK* ma non restituisce le intestazioni CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-535">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="a9ea5-536">Pertanto, il browser non tenta la richiesta cross-origin.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-536">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="a9ea5-537">Impostare l'ora di scadenza preliminare</span><span class="sxs-lookup"><span data-stu-id="a9ea5-537">Set the preflight expiration time</span></span>

<span data-ttu-id="a9ea5-538">L'intestazione `Access-Control-Max-Age` specifica per quanto tempo la risposta alla richiesta di verifica preliminare può essere memorizzata nella cache.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-538">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="a9ea5-539">Per impostare questa <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>intestazione, chiamare :</span><span class="sxs-lookup"><span data-stu-id="a9ea5-539">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="a9ea5-540">Come funziona CORS</span><span class="sxs-lookup"><span data-stu-id="a9ea5-540">How CORS works</span></span>

<span data-ttu-id="a9ea5-541">In questa sezione viene descritto cosa accade in una richiesta [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) a livello dei messaggi HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-541">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="a9ea5-542">CORS **non** è una funzione di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-542">CORS is **not** a security feature.</span></span> <span data-ttu-id="a9ea5-543">CORS è uno standard W3C che consente a un server di ridurre i criteri della stessa origine.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-543">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="a9ea5-544">Ad esempio, un attore malintenzionato potrebbe utilizzare [Impedisci Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) sul tuo sito ed eseguire una richiesta intersito al loro sito abilitato CORS per rubare informazioni.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-544">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="a9ea5-545">La tua API non è più sicura consentendo CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-545">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="a9ea5-546">Spetta al client (browser) applicare CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-546">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="a9ea5-547">Il server esegue la richiesta e restituisce la risposta, è il client che restituisce un errore e blocca la risposta.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-547">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="a9ea5-548">Ad esempio, uno dei seguenti strumenti visualizzerà la risposta del server:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-548">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="a9ea5-549">Fiddler</span><span class="sxs-lookup"><span data-stu-id="a9ea5-549">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="a9ea5-550">Postman</span><span class="sxs-lookup"><span data-stu-id="a9ea5-550">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="a9ea5-551">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="a9ea5-551">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="a9ea5-552">Un browser Web immettendo l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-552">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="a9ea5-553">È un modo per un server per consentire ai browser di eseguire una richiesta [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) o [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) tra origini che altrimenti sarebbe vietata.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-553">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="a9ea5-554">I browser (senza CORS) non possono eseguire richieste cross-origin.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-554">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="a9ea5-555">Prima di CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) veniva usato per aggirare questa restrizione.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-555">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="a9ea5-556">JSONP non usa XHR, usa `<script>` il tag per ricevere la risposta.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-556">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="a9ea5-557">Gli script possono essere caricati tra origini diverse.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-557">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="a9ea5-558">La [specifica CORS](https://www.w3.org/TR/cors/) ha introdotto diverse nuove intestazioni HTTP che abilitano le richieste tra origini diverse.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-558">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="a9ea5-559">Se un browser supporta CORS, imposta automaticamente queste intestazioni per le richieste tra origini diverse.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-559">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="a9ea5-560">Il codice JavaScript personalizzato non è necessario per abilitare CORS.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-560">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="a9ea5-561">Di seguito è riportato un esempio di richiesta tra origini.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-561">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="a9ea5-562">L'intestazione `Origin` fornisce il dominio del sito che effettua la richiesta.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-562">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="a9ea5-563">L'intestazione `Origin` è obbligatoria e deve essere diversa dall'host.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-563">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="a9ea5-564">Se il server consente la `Access-Control-Allow-Origin` richiesta, imposta l'intestazione nella risposta.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-564">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="a9ea5-565">Il valore di questa `Origin` intestazione corrisponde all'intestazione `"*"`della richiesta o è il valore jolly , ovvero è consentita qualsiasi origine:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-565">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="a9ea5-566">Se la risposta non `Access-Control-Allow-Origin` include l'intestazione, la richiesta cross-origin ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-566">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="a9ea5-567">In particolare, il browser non consente la richiesta.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-567">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="a9ea5-568">Anche se il server restituisce una risposta corretta, il browser non rende disponibile la risposta all'app client.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-568">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="a9ea5-569">Testare CORS</span><span class="sxs-lookup"><span data-stu-id="a9ea5-569">Test CORS</span></span>

<span data-ttu-id="a9ea5-570">Per testare CORS:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-570">To test CORS:</span></span>

1. <span data-ttu-id="a9ea5-571">[Creare un progetto API.](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-571">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="a9ea5-572">In alternativa, è possibile [scaricare l'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-572">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="a9ea5-573">Abilitare CORS utilizzando uno degli approcci in questo documento.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-573">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="a9ea5-574">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-574">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="a9ea5-575">`WithOrigins("https://localhost:<port>");`deve essere usato solo per testare un'app di esempio simile al [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)scaricato.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-575">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="a9ea5-576">Creare un progetto di app Web (pagine Razor o MVC).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-576">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="a9ea5-577">L'esempio usa pagine Razor.The sample uses Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-577">The sample uses Razor Pages.</span></span> <span data-ttu-id="a9ea5-578">È possibile creare l'app Web nella stessa soluzione del progetto API.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-578">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="a9ea5-579">Aggiungere il codice evidenziato seguente al file *Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="a9ea5-579">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="a9ea5-580">Nel codice precedente sostituire `url: 'https://<web app>.azurewebsites.net/api/values/1',` con l'URL dell'app distribuita.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-580">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="a9ea5-581">Distribuire il progetto API.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-581">Deploy the API project.</span></span> <span data-ttu-id="a9ea5-582">Ad esempio, [distribuire in Azure .For](xref:host-and-deploy/azure-apps/index)example, deploy to Azure .</span><span class="sxs-lookup"><span data-stu-id="a9ea5-582">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="a9ea5-583">Eseguire le pagine Razor o l'app MVC dal desktop e fare clic sul pulsante **Test.**</span><span class="sxs-lookup"><span data-stu-id="a9ea5-583">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="a9ea5-584">Utilizzare gli strumenti F12 per esaminare i messaggi di errore.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-584">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="a9ea5-585">Rimuovere l'origine `WithOrigins` localhost da e distribuire l'app.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-585">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="a9ea5-586">In alternativa, eseguire l'app client con una porta diversa.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-586">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="a9ea5-587">Ad esempio, eseguire da Visual Studio.For example, run from Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-587">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="a9ea5-588">Eseguire il test con l'app client.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-588">Test with the client app.</span></span> <span data-ttu-id="a9ea5-589">Gli errori CORS restituiscono un errore, ma il messaggio di errore non è disponibile per JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-589">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="a9ea5-590">Utilizzare la scheda della console negli strumenti F12 per visualizzare l'errore.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-590">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="a9ea5-591">A seconda del browser, viene visualizzato un errore (nella console degli strumenti F12) simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-591">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="a9ea5-592">Utilizzo di Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-592">Using Microsoft Edge:</span></span>

     <span data-ttu-id="a9ea5-593">**SEC7120: [CORS] `https://localhost:44375` L'origine `https://localhost:44375` non è stata trovata nell'intestazione di risposta Access-Control-Allow-Origin per la risorsa cross-origin in`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="a9ea5-593">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="a9ea5-594">Utilizzo di Chrome:</span><span class="sxs-lookup"><span data-stu-id="a9ea5-594">Using Chrome:</span></span>

     <span data-ttu-id="a9ea5-595">**L'accesso a `https://webapi.azurewebsites.net/api/values/1` XMLHttpRequest all'origine `https://localhost:44375` è stato bloccato dai criteri CORS: non è presente alcuna intestazione 'Access-Control-Allow-Origin' nella risorsa richiesta.**</span><span class="sxs-lookup"><span data-stu-id="a9ea5-595">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="a9ea5-596">Gli endpoint abilitati per CORS possono essere testati con uno strumento, ad esempio [Fiddler](https://www.telerik.com/fiddler) o [Postman](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="a9ea5-596">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="a9ea5-597">Quando si utilizza uno strumento, l'origine della richiesta specificata dall'intestazione `Origin` deve essere diversa dall'host che riceve la richiesta.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-597">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="a9ea5-598">Se la richiesta non è *di origine incrociata* in base al valore dell'intestazione: `Origin`</span><span class="sxs-lookup"><span data-stu-id="a9ea5-598">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="a9ea5-599">Non è necessario che il middleware CORS elabori la richiesta.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-599">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="a9ea5-600">Le intestazioni CORS non vengono restituite nella risposta.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-600">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="a9ea5-601">CORS in IIS</span><span class="sxs-lookup"><span data-stu-id="a9ea5-601">CORS in IIS</span></span>

<span data-ttu-id="a9ea5-602">Durante la distribuzione in IIS, CORS deve essere eseguito prima dell'autenticazione di Windows se il server non è configurato per consentire l'accesso anonimo.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-602">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="a9ea5-603">Per supportare questo scenario, il [modulo IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) deve essere installato e configurato per l'app.</span><span class="sxs-lookup"><span data-stu-id="a9ea5-603">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a9ea5-604">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a9ea5-604">Additional resources</span></span>

* [<span data-ttu-id="a9ea5-605">Condivisione risorse tra le origini (CORS)</span><span class="sxs-lookup"><span data-stu-id="a9ea5-605">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="a9ea5-606">Introduzione al modulo IIS CORS</span><span class="sxs-lookup"><span data-stu-id="a9ea5-606">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
