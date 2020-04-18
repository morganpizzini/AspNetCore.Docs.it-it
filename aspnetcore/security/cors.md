---
title: Abilitare le richieste cross-origin (CORS) in ASP.NET CoreEnable Cross-Origin Requests (CORS) in ASP.NET Core
author: rick-anderson
description: Scopri come CORS come standard per consentire o rifiutare le richieste tra origini in un'app ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
uid: security/cors
ms.openlocfilehash: 56a339d9018f619af38aecc6f4c2ff40c3c43d2f
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642692"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="99adf-103">Abilitare le richieste cross-origin (CORS) in ASP.NET CoreEnable Cross-Origin Requests (CORS) in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="99adf-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="99adf-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e Kirk [Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="99adf-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="99adf-105">Questo articolo illustra come abilitare CORS in un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="99adf-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="99adf-106">La sicurezza del browser impedisce a una pagina Web di effettuare richieste a un dominio diverso da quello che ha servito la pagina web.</span><span class="sxs-lookup"><span data-stu-id="99adf-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="99adf-107">Questa restrizione è *denominata criterio*della stessa origine .</span><span class="sxs-lookup"><span data-stu-id="99adf-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="99adf-108">I criteri della stessa origine impediscono a un sito dannoso di leggere dati sensibili da un altro sito.</span><span class="sxs-lookup"><span data-stu-id="99adf-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="99adf-109">In alcuni alcuni punti, potresti voler consentire ad altri siti di effettuare richieste cross-origin alla tua app.</span><span class="sxs-lookup"><span data-stu-id="99adf-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="99adf-110">Per ulteriori informazioni, vedere l'articolo di [Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="99adf-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="99adf-111">[Condivisione delle risorse tra origini](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="99adf-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="99adf-112">È uno standard W3C che consente a un server di ridurre la stessa origine.</span><span class="sxs-lookup"><span data-stu-id="99adf-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="99adf-113">**Non** è una funzione di sicurezza, CORS rilassa la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="99adf-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="99adf-114">Un'API non è più sicura consentendo CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="99adf-115">Per ulteriori informazioni, consultate [Funzionamento di CORS.](#how-cors)</span><span class="sxs-lookup"><span data-stu-id="99adf-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="99adf-116">Consente a un server di consentire in modo esplicito alcune richieste tra origini rifiutandone altre.</span><span class="sxs-lookup"><span data-stu-id="99adf-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="99adf-117">È più sicuro e più flessibile rispetto alle tecniche precedenti, ad esempio [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="99adf-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="99adf-118">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="99adf-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="99adf-119">Stessa origine</span><span class="sxs-lookup"><span data-stu-id="99adf-119">Same origin</span></span>

<span data-ttu-id="99adf-120">Due URL hanno la stessa origine se hanno schemi, host e porte identici ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="99adf-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="99adf-121">Questi due URL hanno la stessa origine:</span><span class="sxs-lookup"><span data-stu-id="99adf-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="99adf-122">Questi URL hanno origini diverse rispetto ai due URL precedenti:</span><span class="sxs-lookup"><span data-stu-id="99adf-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="99adf-123">`https://example.net`&ndash; Dominio diverso</span><span class="sxs-lookup"><span data-stu-id="99adf-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="99adf-124">`https://www.example.com/foo.html`&ndash; Sottodominio diverso</span><span class="sxs-lookup"><span data-stu-id="99adf-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="99adf-125">`http://example.com/foo.html`&ndash; Schema diverso</span><span class="sxs-lookup"><span data-stu-id="99adf-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="99adf-126">`https://example.com:9000/foo.html`&ndash; Porta diversa</span><span class="sxs-lookup"><span data-stu-id="99adf-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="99adf-127">Abilitare CORS</span><span class="sxs-lookup"><span data-stu-id="99adf-127">Enable CORS</span></span>

<span data-ttu-id="99adf-128">Esistono tre modi per abilitare CORS:</span><span class="sxs-lookup"><span data-stu-id="99adf-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="99adf-129">Nel middleware che utilizza un [criterio denominato](#np) o [predefinito](#dp).</span><span class="sxs-lookup"><span data-stu-id="99adf-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="99adf-130">Utilizzo del [routing endpoint](#ecors).</span><span class="sxs-lookup"><span data-stu-id="99adf-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="99adf-131">Con l'attributo [[EnableCors].](#attr)</span><span class="sxs-lookup"><span data-stu-id="99adf-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="99adf-132">L'utilizzo dell'attributo [[EnableCors]](#attr) con un criterio denominato fornisce il controllo più condizionato per limitare gli endpoint che supportano CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="99adf-133">Ogni approccio è descritto in dettaglio nelle sezioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="99adf-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="99adf-134">CORS con criteri denominati e middleware</span><span class="sxs-lookup"><span data-stu-id="99adf-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="99adf-135">IL middleware CORS gestisce le richieste tra origini.</span><span class="sxs-lookup"><span data-stu-id="99adf-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="99adf-136">Il codice seguente applica criteri CORS a tutti gli endpoint dell'app con le origini specificate:The following code applies a CORS policy to all the app's endpoints with the specified origins:</span><span class="sxs-lookup"><span data-stu-id="99adf-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="99adf-137">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="99adf-137">The preceding code:</span></span>

* <span data-ttu-id="99adf-138">Imposta il nome `_myAllowSpecificOrigins`del criterio su .</span><span class="sxs-lookup"><span data-stu-id="99adf-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="99adf-139">Il nome del criterio è arbitrario.</span><span class="sxs-lookup"><span data-stu-id="99adf-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="99adf-140">Chiama <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> il metodo di `_myAllowSpecificOrigins` estensione e specifica il criterio CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="99adf-141">`UseCors`aggiunge il middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-141">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="99adf-142">La chiamata `UseCors` a deve `UseRouting`essere `UseAuthorization`effettuata dopo , ma prima di .</span><span class="sxs-lookup"><span data-stu-id="99adf-142">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="99adf-143">Per ulteriori informazioni, vedere [Ordine del middleware](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="99adf-143">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="99adf-144">Chiamate <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> con [un'espressione lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="99adf-144">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="99adf-145">L'espressione <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> lambda accetta un oggetto.</span><span class="sxs-lookup"><span data-stu-id="99adf-145">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="99adf-146">[Le opzioni](#cors-policy-options)di `WithOrigins`configurazione, ad esempio , sono descritte più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="99adf-146">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="99adf-147">Abilita `_myAllowSpecificOrigins` i criteri CORS per tutti gli endpoint del controller.</span><span class="sxs-lookup"><span data-stu-id="99adf-147">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="99adf-148">Vedere [routing degli endpoint](#ecors) per applicare criteri CORS a endpoint specifici.</span><span class="sxs-lookup"><span data-stu-id="99adf-148">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="99adf-149">Con il routing degli endpoint, il middleware CORS `UseRouting` `UseEndpoints` ***deve*** essere configurato per l'esecuzione tra le chiamate a e .</span><span class="sxs-lookup"><span data-stu-id="99adf-149">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="99adf-150">Vedere [Test CORS](#testc) per istruzioni sul test di codice simile al codice precedente.</span><span class="sxs-lookup"><span data-stu-id="99adf-150">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="99adf-151">La <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chiamata al metodo aggiunge servizi CORS al contenitore dei servizi dell'app:The method call adds CORS services to the app's service container:</span><span class="sxs-lookup"><span data-stu-id="99adf-151">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="99adf-152">Per ulteriori informazioni, vedere [Opzioni dei criteri CORS](#cpo) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="99adf-152">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="99adf-153">I <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> metodi possono essere concatenati, come illustrato nel codice seguente:The methods can be chained, as shown in the following code:</span><span class="sxs-lookup"><span data-stu-id="99adf-153">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="99adf-154">Nota: l'URL specificato **non** deve`/`contenere una barra finale ( ).</span><span class="sxs-lookup"><span data-stu-id="99adf-154">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="99adf-155">Se l'URL `/`termina con `false` , il confronto restituisce e non viene restituita alcuna intestazione.</span><span class="sxs-lookup"><span data-stu-id="99adf-155">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="99adf-156">CORS con criteri e middleware predefiniti</span><span class="sxs-lookup"><span data-stu-id="99adf-156">CORS with default policy and middleware</span></span>

<span data-ttu-id="99adf-157">Il codice evidenziato seguente abilita il criterio CORS predefinito:The following highlighted code enables the default CORS policy:</span><span class="sxs-lookup"><span data-stu-id="99adf-157">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="99adf-158">Il codice precedente applica i criteri CORS predefiniti a tutti gli endpoint del controller.</span><span class="sxs-lookup"><span data-stu-id="99adf-158">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="99adf-159">Abilitare Cors con routing endpoint</span><span class="sxs-lookup"><span data-stu-id="99adf-159">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="99adf-160">L'abilitazione di CORS `RequireCors` in base all'endpoint che utilizza attualmente ***non*** supporta le richieste automatiche di [verifica preliminare.](#apf)</span><span class="sxs-lookup"><span data-stu-id="99adf-160">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="99adf-161">Per ulteriori informazioni, vedere [questo problema di GitHub](https://github.com/dotnet/aspnetcore/issues/20709) e [Test corS con routing degli endpoint e [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="99adf-161">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="99adf-162">Con il routing degli endpoint, CORS può essere <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> abilitato in base all'endpoint usando il set di metodi di estensione:With endpoint routing, CORS can be enabled on a per-endpoint basis using the set of extension methods:</span><span class="sxs-lookup"><span data-stu-id="99adf-162">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="99adf-163">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="99adf-163">In the preceding code:</span></span>

* <span data-ttu-id="99adf-164">`app.UseCors`abilita il middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-164">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="99adf-165">Poiché un criterio predefinito non `app.UseCors()` è stato configurato, da solo non abilita CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-165">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="99adf-166">Gli `/echo` endpoint e del controller consentono le richieste tra origini utilizzando i criteri specificati.</span><span class="sxs-lookup"><span data-stu-id="99adf-166">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="99adf-167">Gli `/echo2` endpoint e Razor Pages ***non*** consentono richieste tra origini perché non è stato specificato alcun criterio predefinito.</span><span class="sxs-lookup"><span data-stu-id="99adf-167">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="99adf-168">L'attributo [[DisableCors]](#dc) ***non*** disabilita CORS abilitato `RequireCors`dal routing degli endpoint con .</span><span class="sxs-lookup"><span data-stu-id="99adf-168">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="99adf-169">Vedere [Test CORS con routing degli endpoint e [HttpOptions]](#tcer) per istruzioni sul test di codice simile al precedente.</span><span class="sxs-lookup"><span data-stu-id="99adf-169">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="99adf-170">Abilitare CORS con attributi</span><span class="sxs-lookup"><span data-stu-id="99adf-170">Enable CORS with attributes</span></span>

<span data-ttu-id="99adf-171">L'abilitazione di CORS con l'attributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) e l'applicazione di un criterio denominato solo agli endpoint che richiedono CORS forniscono il controllo migliore.</span><span class="sxs-lookup"><span data-stu-id="99adf-171">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="99adf-172">L'attributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornisce un'alternativa all'applicazione di CORS a livello globale.</span><span class="sxs-lookup"><span data-stu-id="99adf-172">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="99adf-173">L'attributo `[EnableCors]` abilita CORS per gli endpoint selezionati, anziché per tutti gli endpoint:The attribute enables CORS for selected endpoints, rather than all endpoints:</span><span class="sxs-lookup"><span data-stu-id="99adf-173">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="99adf-174">`[EnableCors]`specifica il criterio predefinito.</span><span class="sxs-lookup"><span data-stu-id="99adf-174">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="99adf-175">`[EnableCors("{Policy String}")]`specifica un criterio denominato.</span><span class="sxs-lookup"><span data-stu-id="99adf-175">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="99adf-176">L'attributo `[EnableCors]` può essere applicato a:</span><span class="sxs-lookup"><span data-stu-id="99adf-176">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="99adf-177">Pagina Razor`PageModel`</span><span class="sxs-lookup"><span data-stu-id="99adf-177">Razor Page `PageModel`</span></span>
* <span data-ttu-id="99adf-178">Controller</span><span class="sxs-lookup"><span data-stu-id="99adf-178">Controller</span></span>
* <span data-ttu-id="99adf-179">Metodo di azione del controller</span><span class="sxs-lookup"><span data-stu-id="99adf-179">Controller action method</span></span>

<span data-ttu-id="99adf-180">È possibile applicare criteri diversi ai controller, `[EnableCors]` ai modelli di pagina o ai metodi di azione con l'attributo.</span><span class="sxs-lookup"><span data-stu-id="99adf-180">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="99adf-181">Quando `[EnableCors]` l'attributo viene applicato a un controller, a un modello di pagina o a un metodo di azione e CORS è abilitato nel middleware, vengono applicati ***entrambi i*** criteri.</span><span class="sxs-lookup"><span data-stu-id="99adf-181">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="99adf-182">***Si consiglia di non combinare le politiche. Usa l'attributo*** `[EnableCors]` o il ***middleware, non entrambi nella stessa app.***</span><span class="sxs-lookup"><span data-stu-id="99adf-182">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="99adf-183">Il codice seguente applica criteri diversi a ogni metodo:The following code applies a different policy to each method:</span><span class="sxs-lookup"><span data-stu-id="99adf-183">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="99adf-184">Il codice seguente crea due criteri CORS:The following code creates two CORS policies:</span><span class="sxs-lookup"><span data-stu-id="99adf-184">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="99adf-185">Per il miglior controllo della limitazione delle richieste CORS:</span><span class="sxs-lookup"><span data-stu-id="99adf-185">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="99adf-186">Utilizzare `[EnableCors("MyPolicy")]` con un criterio denominato.</span><span class="sxs-lookup"><span data-stu-id="99adf-186">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="99adf-187">Non definire un criterio predefinito.</span><span class="sxs-lookup"><span data-stu-id="99adf-187">Don't define a default policy.</span></span>
* <span data-ttu-id="99adf-188">Non utilizzare il [routing degli endpoint.](#ecors)</span><span class="sxs-lookup"><span data-stu-id="99adf-188">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="99adf-189">Il codice nella sezione successiva incontra l'elenco precedente.</span><span class="sxs-lookup"><span data-stu-id="99adf-189">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="99adf-190">Vedere [Test CORS](#testc) per istruzioni sul test di codice simile al codice precedente.</span><span class="sxs-lookup"><span data-stu-id="99adf-190">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="99adf-191">Disabilita CORS</span><span class="sxs-lookup"><span data-stu-id="99adf-191">Disable CORS</span></span>

<span data-ttu-id="99adf-192">L'attributo [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ***non*** disabilita CORS abilitato dal [routing degli endpoint.](#ecors)</span><span class="sxs-lookup"><span data-stu-id="99adf-192">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="99adf-193">Il codice seguente definisce `"MyPolicy"`i criteri CORS :</span><span class="sxs-lookup"><span data-stu-id="99adf-193">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="99adf-194">Il codice seguente disabilita CORS per l'azione: `GetValues2`</span><span class="sxs-lookup"><span data-stu-id="99adf-194">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="99adf-195">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="99adf-195">The preceding code:</span></span>

* <span data-ttu-id="99adf-196">Non abilita CORS con [routing endpoint](#ecors).</span><span class="sxs-lookup"><span data-stu-id="99adf-196">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="99adf-197">Non definisce un [criterio CORS predefinito.](#dp)</span><span class="sxs-lookup"><span data-stu-id="99adf-197">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="99adf-198">Utilizza [[EnableCors("MyPolicy")]](#attr) per `"MyPolicy"` abilitare i criteri CORS per il controller.</span><span class="sxs-lookup"><span data-stu-id="99adf-198">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="99adf-199">Disabilita CORS per `GetValues2` il metodo.</span><span class="sxs-lookup"><span data-stu-id="99adf-199">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="99adf-200">Vedere [Test CORS](#testc) per istruzioni sul test del codice precedente.</span><span class="sxs-lookup"><span data-stu-id="99adf-200">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="99adf-201">Opzioni dei criteri CORS</span><span class="sxs-lookup"><span data-stu-id="99adf-201">CORS policy options</span></span>

<span data-ttu-id="99adf-202">In questa sezione vengono descritte le varie opzioni che è possibile impostare in un criterio CORS:</span><span class="sxs-lookup"><span data-stu-id="99adf-202">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="99adf-203">Impostare le origini consentite</span><span class="sxs-lookup"><span data-stu-id="99adf-203">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="99adf-204">Impostare i metodi HTTP consentiti</span><span class="sxs-lookup"><span data-stu-id="99adf-204">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="99adf-205">Impostare le intestazioni di richiesta consentite</span><span class="sxs-lookup"><span data-stu-id="99adf-205">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="99adf-206">Impostare le intestazioni delle risposte esposte</span><span class="sxs-lookup"><span data-stu-id="99adf-206">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="99adf-207">Credenziali nelle richieste tra originiCredentials in cross-origin requests</span><span class="sxs-lookup"><span data-stu-id="99adf-207">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="99adf-208">Impostare l'ora di scadenza preliminare</span><span class="sxs-lookup"><span data-stu-id="99adf-208">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="99adf-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>viene chiamato `Startup.ConfigureServices`in .</span><span class="sxs-lookup"><span data-stu-id="99adf-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="99adf-210">Per alcune opzioni, può essere utile leggere prima la sezione [Come CORS funziona.](#how-cors)</span><span class="sxs-lookup"><span data-stu-id="99adf-210">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="99adf-211">Impostare le origini consentite</span><span class="sxs-lookup"><span data-stu-id="99adf-211">Set the allowed origins</span></span>

<span data-ttu-id="99adf-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Consente le richieste CORS da tutte`http` `https`le origini con qualsiasi schema ( o ).</span><span class="sxs-lookup"><span data-stu-id="99adf-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="99adf-213">`AllowAnyOrigin`non è sicuro perché *qualsiasi sito Web* può effettuare richieste cross-origin all'app.</span><span class="sxs-lookup"><span data-stu-id="99adf-213">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="99adf-214">Specificando `AllowAnyOrigin` ed `AllowCredentials` è una configurazione non sicura e può comportare la falsificazione di richieste intersito.</span><span class="sxs-lookup"><span data-stu-id="99adf-214">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="99adf-215">Il servizio CORS restituisce una risposta CORS non valida quando un'app è configurata con entrambi i metodi.</span><span class="sxs-lookup"><span data-stu-id="99adf-215">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="99adf-216">`AllowAnyOrigin`influisce sulle richieste `Access-Control-Allow-Origin` di verifica preliminare e sull'intestazione.</span><span class="sxs-lookup"><span data-stu-id="99adf-216">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="99adf-217">Per altre informazioni, vedere la sezione [Richieste di verifica preliminare.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="99adf-217">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="99adf-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Imposta <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> la proprietà del criterio in modo che sia una funzione che consente alle origini di corrispondere a un dominio con caratteri jolly configurato durante la valutazione se l'origine è consentita.</span><span class="sxs-lookup"><span data-stu-id="99adf-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="99adf-219">Impostare i metodi HTTP consentiti</span><span class="sxs-lookup"><span data-stu-id="99adf-219">Set the allowed HTTP methods</span></span>

<span data-ttu-id="99adf-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="99adf-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="99adf-221">Consente qualsiasi metodo HTTP:</span><span class="sxs-lookup"><span data-stu-id="99adf-221">Allows any HTTP method:</span></span>
* <span data-ttu-id="99adf-222">Influisce sulle richieste `Access-Control-Allow-Methods` di verifica preliminare e sull'intestazione.</span><span class="sxs-lookup"><span data-stu-id="99adf-222">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="99adf-223">Per altre informazioni, vedere la sezione [Richieste di verifica preliminare.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="99adf-223">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="99adf-224">Impostare le intestazioni di richiesta consentite</span><span class="sxs-lookup"><span data-stu-id="99adf-224">Set the allowed request headers</span></span>

<span data-ttu-id="99adf-225">Per consentire l'invio di intestazioni specifiche in una richiesta <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> CORS, denominate intestazioni di richiesta [author](https://xhr.spec.whatwg.org/#request), chiamare e specificare le intestazioni consentite:</span><span class="sxs-lookup"><span data-stu-id="99adf-225">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="99adf-226">Per consentire tutte le <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>intestazioni di [richiesta dell'autore,](https://www.w3.org/TR/cors/#author-request-headers)chiamare :</span><span class="sxs-lookup"><span data-stu-id="99adf-226">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="99adf-227">`AllowAnyHeader`influisce sulle richieste di verifica preliminare e sull'intestazione [Access-Control-Request-Headers.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)</span><span class="sxs-lookup"><span data-stu-id="99adf-227">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="99adf-228">Per altre informazioni, vedere la sezione [Richieste di verifica preliminare.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="99adf-228">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="99adf-229">Un criterio del middleware CORS `WithHeaders` corrispondente a intestazioni specifiche `Access-Control-Request-Headers` specificate da è `WithHeaders`possibile solo quando le intestazioni inviate corrispondono esattamente alle intestazioni indicate in .</span><span class="sxs-lookup"><span data-stu-id="99adf-229">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="99adf-230">Ad esempio, si consideri un'app configurata come segue:For example, consider an app configured as follows:</span><span class="sxs-lookup"><span data-stu-id="99adf-230">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="99adf-231">IL middleware CORS rifiuta una richiesta di `Content-Language` verifica preliminare con la seguente intestazione di richiesta perché ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) non è elencato in `WithHeaders`:</span><span class="sxs-lookup"><span data-stu-id="99adf-231">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="99adf-232">L'app restituisce una risposta *200 OK* ma non restituisce le intestazioni CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-232">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="99adf-233">Pertanto, il browser non tenta la richiesta cross-origin.</span><span class="sxs-lookup"><span data-stu-id="99adf-233">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="99adf-234">Impostare le intestazioni delle risposte esposte</span><span class="sxs-lookup"><span data-stu-id="99adf-234">Set the exposed response headers</span></span>

<span data-ttu-id="99adf-235">Per impostazione predefinita, il browser non espone tutte le intestazioni di risposta all'app.</span><span class="sxs-lookup"><span data-stu-id="99adf-235">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="99adf-236">Per ulteriori informazioni, vedere [W3C Cross-Origin Resource Sharing (Terminologia): Intestazione di risposta semplice](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="99adf-236">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="99adf-237">Le intestazioni di risposta disponibili per impostazione predefinita sono:The response headers that are available by default are:</span><span class="sxs-lookup"><span data-stu-id="99adf-237">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="99adf-238">La specifica CORS chiama queste intestazioni intestazioni di *risposta semplici*.</span><span class="sxs-lookup"><span data-stu-id="99adf-238">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="99adf-239">Per rendere disponibili altre intestazioni <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>per l'app, chiamare :</span><span class="sxs-lookup"><span data-stu-id="99adf-239">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="99adf-240">Credenziali nelle richieste tra originiCredentials in cross-origin requests</span><span class="sxs-lookup"><span data-stu-id="99adf-240">Credentials in cross-origin requests</span></span>

<span data-ttu-id="99adf-241">Le credenziali richiedono una gestione speciale in una richiesta CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-241">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="99adf-242">Per impostazione predefinita, il browser non invia credenziali con una richiesta cross-origin.</span><span class="sxs-lookup"><span data-stu-id="99adf-242">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="99adf-243">Le credenziali includono cookie e schemi di autenticazione HTTP.</span><span class="sxs-lookup"><span data-stu-id="99adf-243">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="99adf-244">Per inviare le credenziali con una richiesta `XMLHttpRequest.withCredentials` tra `true`origini diverse, il client deve impostare su .</span><span class="sxs-lookup"><span data-stu-id="99adf-244">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="99adf-245">Utilizzando `XMLHttpRequest` direttamente:</span><span class="sxs-lookup"><span data-stu-id="99adf-245">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="99adf-246">Utilizzo di jQuery:</span><span class="sxs-lookup"><span data-stu-id="99adf-246">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="99adf-247">Utilizzo [dell'API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="99adf-247">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="99adf-248">Il server deve consentire le credenziali.</span><span class="sxs-lookup"><span data-stu-id="99adf-248">The server must allow the credentials.</span></span> <span data-ttu-id="99adf-249">Per consentire le credenziali di <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>origine incrociata, chiamare :</span><span class="sxs-lookup"><span data-stu-id="99adf-249">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="99adf-250">La risposta HTTP `Access-Control-Allow-Credentials` include un'intestazione, che indica al browser che il server consente le credenziali per una richiesta tra origini diverse.</span><span class="sxs-lookup"><span data-stu-id="99adf-250">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="99adf-251">Se il browser invia le credenziali ma la `Access-Control-Allow-Credentials` risposta non include un'intestazione valida, il browser non espone la risposta all'app e la richiesta tra origini ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="99adf-251">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="99adf-252">Consentire credenziali tra origini è un rischio per la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="99adf-252">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="99adf-253">Un sito Web in un altro dominio può inviare le credenziali di un utente connesso all'app per conto dell'utente all'insaputa dell'utente.</span><span class="sxs-lookup"><span data-stu-id="99adf-253">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="99adf-254">La specifica CORS indica inoltre `"*"` che l'impostazione delle `Access-Control-Allow-Credentials` origini (tutte le origini) non è valida se l'intestazione è presente.</span><span class="sxs-lookup"><span data-stu-id="99adf-254">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="99adf-255">Richieste di verifica preliminare</span><span class="sxs-lookup"><span data-stu-id="99adf-255">Preflight requests</span></span>

<span data-ttu-id="99adf-256">Per alcune richieste CORS, il browser invia una richiesta [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) aggiuntiva prima di effettuare la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="99adf-256">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="99adf-257">Questa richiesta viene definita [richiesta di verifica preliminare.](https://developer.mozilla.org/docs/Glossary/Preflight_request)</span><span class="sxs-lookup"><span data-stu-id="99adf-257">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="99adf-258">Il browser può ignorare la richiesta di verifica preliminare se ***tutte*** le seguenti condizioni sono vere:</span><span class="sxs-lookup"><span data-stu-id="99adf-258">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="99adf-259">Il metodo di richiesta è GET, HEAD o POST.</span><span class="sxs-lookup"><span data-stu-id="99adf-259">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="99adf-260">L'app non imposta intestazioni `Accept`di `Accept-Language` `Content-Language`richiesta `Content-Type`diverse `Last-Event-ID`da , , , o .</span><span class="sxs-lookup"><span data-stu-id="99adf-260">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="99adf-261">L'intestazione, `Content-Type` se impostata, ha uno dei seguenti valori:</span><span class="sxs-lookup"><span data-stu-id="99adf-261">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="99adf-262">La regola sulle intestazioni di richiesta impostate per la `setRequestHeader` richiesta `XMLHttpRequest` client si applica alle intestazioni impostate dall'app chiamando sull'oggetto.</span><span class="sxs-lookup"><span data-stu-id="99adf-262">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="99adf-263">La specifica CORS chiama queste intestazioni [intestazioni di richiesta dell'autore.](https://www.w3.org/TR/cors/#author-request-headers)</span><span class="sxs-lookup"><span data-stu-id="99adf-263">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="99adf-264">La regola non si applica alle intestazioni che `User-Agent` `Host`il `Content-Length`browser può impostare, ad esempio , o .</span><span class="sxs-lookup"><span data-stu-id="99adf-264">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="99adf-265">Di seguito è riportata una risposta di esempio simile alla richiesta di verifica preliminare effettuata dal pulsante **[Put test]** nella sezione [Test CORS](#testc) di questo documento.</span><span class="sxs-lookup"><span data-stu-id="99adf-265">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="99adf-266">La richiesta di verifica preliminare utilizza il metodo [HTTP OPTIONS.](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)</span><span class="sxs-lookup"><span data-stu-id="99adf-266">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="99adf-267">Può includere le seguenti intestazioni:</span><span class="sxs-lookup"><span data-stu-id="99adf-267">It may include the following headers:</span></span>

* <span data-ttu-id="99adf-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): il metodo HTTP che verrà utilizzato per la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="99adf-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="99adf-269">[Access-Control-Request-Headers:](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers)elenco di intestazioni di richiesta impostate dall'app nella richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="99adf-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="99adf-270">Come indicato in precedenza, non sono incluse le `User-Agent`intestazioni impostate dal browser, ad esempio .</span><span class="sxs-lookup"><span data-stu-id="99adf-270">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="99adf-271">Access-Control-Allow-Methods</span><span class="sxs-lookup"><span data-stu-id="99adf-271">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="99adf-272">Se la richiesta di verifica preliminare `200 OK` viene negata, l'app restituisce una risposta ma non imposta le intestazioni CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-272">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="99adf-273">Pertanto, il browser non tenta la richiesta cross-origin.</span><span class="sxs-lookup"><span data-stu-id="99adf-273">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="99adf-274">Per un esempio di richiesta di verifica preliminare negata, vedere la sezione [Test CORS](#testc) di questo documento.</span><span class="sxs-lookup"><span data-stu-id="99adf-274">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="99adf-275">Utilizzando gli strumenti F12, l'applicazione console mostra un errore simile a uno dei seguenti, a seconda del browser:</span><span class="sxs-lookup"><span data-stu-id="99adf-275">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="99adf-276">Firefox: Richiesta cross-origin bloccata: la stessa politica di `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`origine non consente la lettura della risorsa remota all'indirizzo .</span><span class="sxs-lookup"><span data-stu-id="99adf-276">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="99adf-277">(Motivo: richiesta CORS non riuscita).</span><span class="sxs-lookup"><span data-stu-id="99adf-277">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="99adf-278">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="99adf-278">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="99adf-279">Basato su chromium:https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5l'accessohttps://cors3.azurewebsites.netal recupero a ' dall'origine ' è stato bloccato dai criteri CORS: la risposta alla richiesta di verifica preliminare non supera il controllo di accesso: non è presente alcuna intestazione 'Access-Control-Allow-Origin' sulla risorsa richiesta.</span><span class="sxs-lookup"><span data-stu-id="99adf-279">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="99adf-280">Se si ritiene soddisfacente una risposta opaca, impostare la modalità della richiesta 'no-cors' per recuperare la risorsa con CORS disabilitato.</span><span class="sxs-lookup"><span data-stu-id="99adf-280">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="99adf-281">Per consentire intestazioni <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>specifiche, chiamare :</span><span class="sxs-lookup"><span data-stu-id="99adf-281">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="99adf-282">Per consentire tutte le <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>intestazioni di [richiesta dell'autore,](https://www.w3.org/TR/cors/#author-request-headers)chiamare :</span><span class="sxs-lookup"><span data-stu-id="99adf-282">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="99adf-283">I browser non sono coerenti `Access-Control-Request-Headers`nel modo in cui impostano .</span><span class="sxs-lookup"><span data-stu-id="99adf-283">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="99adf-284">Se uno dei due:</span><span class="sxs-lookup"><span data-stu-id="99adf-284">If either:</span></span>

* <span data-ttu-id="99adf-285">Le intestazioni sono impostate su un valore diverso da`"*"`</span><span class="sxs-lookup"><span data-stu-id="99adf-285">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="99adf-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>è chiamato: includere `Accept` `Content-Type`almeno `Origin`, e , oltre a tutte le intestazioni personalizzate che si desidera supportare.</span><span class="sxs-lookup"><span data-stu-id="99adf-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="99adf-287">Codice di richiesta di verifica preliminare automatica</span><span class="sxs-lookup"><span data-stu-id="99adf-287">Automatic preflight request code</span></span>

<span data-ttu-id="99adf-288">Quando viene applicato il criterio CORS:</span><span class="sxs-lookup"><span data-stu-id="99adf-288">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="99adf-289">Globalmente chiamando `app.UseCors` `Startup.Configure`in .</span><span class="sxs-lookup"><span data-stu-id="99adf-289">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="99adf-290">Utilizzo `[EnableCors]` dell'attributo .</span><span class="sxs-lookup"><span data-stu-id="99adf-290">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="99adf-291">ASP.NET Core risponde alla richiesta OPTIONS di verifica preliminare.</span><span class="sxs-lookup"><span data-stu-id="99adf-291">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="99adf-292">L'abilitazione di CORS `RequireCors` in base all'endpoint ***non*** supporta attualmente le richieste di verifica preliminare automatiche.</span><span class="sxs-lookup"><span data-stu-id="99adf-292">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="99adf-293">La sezione [Test CORS](#testc) di questo documento illustra questo comportamento.</span><span class="sxs-lookup"><span data-stu-id="99adf-293">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="99adf-294">Attributo [HttpOptions] per le richieste di verifica preliminare</span><span class="sxs-lookup"><span data-stu-id="99adf-294">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="99adf-295">Quando CORS è abilitato con il criterio appropriato, ASP.NET Core risponde in genere automaticamente alle richieste di verifica preliminare CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-295">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="99adf-296">In alcuni scenari, questo potrebbe non essere il caso.</span><span class="sxs-lookup"><span data-stu-id="99adf-296">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="99adf-297">Ad esempio, utilizzando [CORS con il routing degli endpoint](#ecors).</span><span class="sxs-lookup"><span data-stu-id="99adf-297">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="99adf-298">Il codice seguente usa l'attributo [HttpOptions] per creare endpoint per le richieste OPTIONS:The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span><span class="sxs-lookup"><span data-stu-id="99adf-298">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="99adf-299">Vedere [Test CORS con routing degli endpoint e [HttpOptions]](#tcer) per istruzioni sul test del codice precedente.</span><span class="sxs-lookup"><span data-stu-id="99adf-299">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="99adf-300">Impostare l'ora di scadenza preliminare</span><span class="sxs-lookup"><span data-stu-id="99adf-300">Set the preflight expiration time</span></span>

<span data-ttu-id="99adf-301">L'intestazione `Access-Control-Max-Age` specifica per quanto tempo la risposta alla richiesta di verifica preliminare può essere memorizzata nella cache.</span><span class="sxs-lookup"><span data-stu-id="99adf-301">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="99adf-302">Per impostare questa <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>intestazione, chiamare :</span><span class="sxs-lookup"><span data-stu-id="99adf-302">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="99adf-303">Come funziona CORS</span><span class="sxs-lookup"><span data-stu-id="99adf-303">How CORS works</span></span>

<span data-ttu-id="99adf-304">In questa sezione viene descritto cosa accade in una richiesta [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) a livello dei messaggi HTTP.</span><span class="sxs-lookup"><span data-stu-id="99adf-304">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="99adf-305">CORS **non** è una funzione di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="99adf-305">CORS is **not** a security feature.</span></span> <span data-ttu-id="99adf-306">CORS è uno standard W3C che consente a un server di ridurre i criteri della stessa origine.</span><span class="sxs-lookup"><span data-stu-id="99adf-306">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="99adf-307">Ad esempio, un attore malintenzionato potrebbe utilizzare [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) sul tuo sito ed eseguire una richiesta intersito al loro sito abilitato CORS per rubare informazioni.</span><span class="sxs-lookup"><span data-stu-id="99adf-307">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="99adf-308">Un'API non è più sicura consentendo CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-308">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="99adf-309">Spetta al client (browser) applicare CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-309">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="99adf-310">Il server esegue la richiesta e restituisce la risposta, è il client che restituisce un errore e blocca la risposta.</span><span class="sxs-lookup"><span data-stu-id="99adf-310">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="99adf-311">Ad esempio, uno dei seguenti strumenti visualizzerà la risposta del server:</span><span class="sxs-lookup"><span data-stu-id="99adf-311">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="99adf-312">Fiddler</span><span class="sxs-lookup"><span data-stu-id="99adf-312">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="99adf-313">Postman</span><span class="sxs-lookup"><span data-stu-id="99adf-313">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="99adf-314">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="99adf-314">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="99adf-315">Un browser Web immettendo l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="99adf-315">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="99adf-316">È un modo per un server per consentire ai browser di eseguire una richiesta [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) o [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) tra origini che altrimenti sarebbe vietata.</span><span class="sxs-lookup"><span data-stu-id="99adf-316">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="99adf-317">I browser senza CORS non possono eseguire richieste cross-origin.</span><span class="sxs-lookup"><span data-stu-id="99adf-317">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="99adf-318">Prima di CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) veniva usato per aggirare questa restrizione.</span><span class="sxs-lookup"><span data-stu-id="99adf-318">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="99adf-319">JSONP non usa XHR, usa `<script>` il tag per ricevere la risposta.</span><span class="sxs-lookup"><span data-stu-id="99adf-319">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="99adf-320">Gli script possono essere caricati tra origini diverse.</span><span class="sxs-lookup"><span data-stu-id="99adf-320">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="99adf-321">La [specifica CORS](https://www.w3.org/TR/cors/) ha introdotto diverse nuove intestazioni HTTP che abilitano le richieste tra origini diverse.</span><span class="sxs-lookup"><span data-stu-id="99adf-321">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="99adf-322">Se un browser supporta CORS, imposta automaticamente queste intestazioni per le richieste tra origini diverse.</span><span class="sxs-lookup"><span data-stu-id="99adf-322">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="99adf-323">Il codice JavaScript personalizzato non è necessario per abilitare CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-323">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="99adf-324">Il [pulsante test PUT](https://cors3.azurewebsites.net/test) nell'esempio distribuito [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="99adf-324">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="99adf-325">Di seguito è riportato un esempio [Values](https://cors3.azurewebsites.net/) di richiesta `https://cors1.azurewebsites.net/api/values`tra origini dal pulsante di test Valori a .</span><span class="sxs-lookup"><span data-stu-id="99adf-325">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="99adf-326">L'intestazione: `Origin`</span><span class="sxs-lookup"><span data-stu-id="99adf-326">The `Origin` header:</span></span>

* <span data-ttu-id="99adf-327">Fornisce il dominio del sito che effettua la richiesta.</span><span class="sxs-lookup"><span data-stu-id="99adf-327">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="99adf-328">È obbligatorio e deve essere diverso dall'host.</span><span class="sxs-lookup"><span data-stu-id="99adf-328">Is required and must be different from the host.</span></span>

<span data-ttu-id="99adf-329">**Intestazioni generali**</span><span class="sxs-lookup"><span data-stu-id="99adf-329">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="99adf-330">**Intestazioni di risposta**</span><span class="sxs-lookup"><span data-stu-id="99adf-330">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="99adf-331">**Intestazioni della richiesta**</span><span class="sxs-lookup"><span data-stu-id="99adf-331">**Request headers**</span></span>

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

<span data-ttu-id="99adf-332">Nelle `OPTIONS` richieste, il server imposta l'intestazione **di intestazioni** `Access-Control-Allow-Origin: {allowed origin}` di risposta nella risposta.</span><span class="sxs-lookup"><span data-stu-id="99adf-332">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="99adf-333">Ad esempio, la richiesta del pulsante `OPTIONS` [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) di [esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)distribuita contiene le intestazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="99adf-333">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="99adf-334">**Intestazioni generali**</span><span class="sxs-lookup"><span data-stu-id="99adf-334">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="99adf-335">**Intestazioni di risposta**</span><span class="sxs-lookup"><span data-stu-id="99adf-335">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="99adf-336">**Intestazioni della richiesta**</span><span class="sxs-lookup"><span data-stu-id="99adf-336">**Request headers**</span></span>

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

<span data-ttu-id="99adf-337">Nelle intestazioni **Di risposta**precedenti, il server imposta l'intestazione [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) nella risposta.</span><span class="sxs-lookup"><span data-stu-id="99adf-337">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="99adf-338">Il `https://cors1.azurewebsites.net` valore di questa `Origin` intestazione corrisponde all'intestazione della richiesta.</span><span class="sxs-lookup"><span data-stu-id="99adf-338">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="99adf-339">Se <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> viene chiamato, viene restituito il `Access-Control-Allow-Origin: *`valore con caratteri jolly .</span><span class="sxs-lookup"><span data-stu-id="99adf-339">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="99adf-340">`AllowAnyOrigin`permette qualsiasi origine.</span><span class="sxs-lookup"><span data-stu-id="99adf-340">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="99adf-341">Se la risposta non `Access-Control-Allow-Origin` include l'intestazione, la richiesta cross-origin ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="99adf-341">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="99adf-342">In particolare, il browser non consente la richiesta.</span><span class="sxs-lookup"><span data-stu-id="99adf-342">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="99adf-343">Anche se il server restituisce una risposta corretta, il browser non rende disponibile la risposta all'app client.</span><span class="sxs-lookup"><span data-stu-id="99adf-343">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="99adf-344">Visualizza richieste OPTIONS</span><span class="sxs-lookup"><span data-stu-id="99adf-344">Display OPTIONS requests</span></span>

<span data-ttu-id="99adf-345">Per impostazione predefinita, i browser Chrome ed Edge non mostrano le richieste OPTIONS nella scheda rete degli strumenti F12.</span><span class="sxs-lookup"><span data-stu-id="99adf-345">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="99adf-346">Per visualizzare le richieste OPTIONS in questi browser:</span><span class="sxs-lookup"><span data-stu-id="99adf-346">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="99adf-347">`chrome://flags/#out-of-blink-cors` o `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="99adf-347">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="99adf-348">disattivare il flag.</span><span class="sxs-lookup"><span data-stu-id="99adf-348">disable the flag.</span></span>
* <span data-ttu-id="99adf-349">Riavvia.</span><span class="sxs-lookup"><span data-stu-id="99adf-349">restart.</span></span>

<span data-ttu-id="99adf-350">Firefox mostra le richieste OPTIONS per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="99adf-350">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="99adf-351">CORS in IIS</span><span class="sxs-lookup"><span data-stu-id="99adf-351">CORS in IIS</span></span>

<span data-ttu-id="99adf-352">Durante la distribuzione in IIS, CORS deve essere eseguito prima dell'autenticazione di Windows se il server non è configurato per consentire l'accesso anonimo.</span><span class="sxs-lookup"><span data-stu-id="99adf-352">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="99adf-353">Per supportare questo scenario, il [modulo IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) deve essere installato e configurato per l'app.</span><span class="sxs-lookup"><span data-stu-id="99adf-353">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="99adf-354">Testare CORS</span><span class="sxs-lookup"><span data-stu-id="99adf-354">Test CORS</span></span>

<span data-ttu-id="99adf-355">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) include codice per testare CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-355">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="99adf-356">Vedere [come scaricare un esempio](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="99adf-356">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="99adf-357">L'esempio è un progetto API con pagine Razor aggiunte:The sample is an API project with Razor Pages added:</span><span class="sxs-lookup"><span data-stu-id="99adf-357">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="99adf-358">`WithOrigins("https://localhost:<port>");`deve essere usato solo per testare un'app di esempio simile al [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)scaricato.</span><span class="sxs-lookup"><span data-stu-id="99adf-358">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="99adf-359">Di `ValuesController` seguito sono disponibili gli endpoint per il test:The following provides the endpoints for testing:</span><span class="sxs-lookup"><span data-stu-id="99adf-359">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="99adf-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) viene fornito dal pacchetto [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet e visualizza le informazioni sulla route.</span><span class="sxs-lookup"><span data-stu-id="99adf-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="99adf-361">Testare il codice di esempio precedente utilizzando uno degli approcci seguenti:Test the preceding sample code by using one of the following approaches:</span><span class="sxs-lookup"><span data-stu-id="99adf-361">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="99adf-362">Usare l'app [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)di esempio distribuita in .</span><span class="sxs-lookup"><span data-stu-id="99adf-362">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="99adf-363">Non è necessario scaricare l'esempio.</span><span class="sxs-lookup"><span data-stu-id="99adf-363">There is no need to download the sample.</span></span>
* <span data-ttu-id="99adf-364">Eseguire l'esempio utilizzando `dotnet run` l'URL predefinito di `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="99adf-364">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="99adf-365">Eseguire l'esempio da Visual Studio con la porta impostata `https://localhost:44398`su 44398 per un URL di .</span><span class="sxs-lookup"><span data-stu-id="99adf-365">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="99adf-366">Utilizzando un browser con gli strumenti F12:</span><span class="sxs-lookup"><span data-stu-id="99adf-366">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="99adf-367">Selezionare il pulsante **Valori** ed esaminare le intestazioni nella scheda **Rete.**</span><span class="sxs-lookup"><span data-stu-id="99adf-367">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="99adf-368">Selezionare il pulsante **TEST PUT.**</span><span class="sxs-lookup"><span data-stu-id="99adf-368">Select the **PUT test** button.</span></span> <span data-ttu-id="99adf-369">Vedere [Visualizzare le richieste OPTIONS](#options) per istruzioni sulla visualizzazione della richiesta OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="99adf-369">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="99adf-370">Il **test PUT** crea due richieste, una richiesta di verifica preliminare OPTIONS e la richiesta PUT.</span><span class="sxs-lookup"><span data-stu-id="99adf-370">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="99adf-371">Selezionare **`GetValues2 [DisableCors]`** il pulsante per attivare una richiesta CORS non riuscita.</span><span class="sxs-lookup"><span data-stu-id="99adf-371">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="99adf-372">Come accennato nel documento, la risposta restituisce 200 successo, ma la richiesta CORS non viene effettuata.</span><span class="sxs-lookup"><span data-stu-id="99adf-372">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="99adf-373">Selezionare la scheda **Console** per visualizzare l'errore CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-373">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="99adf-374">A seconda del browser, viene visualizzato un errore simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="99adf-374">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="99adf-375">L'accesso `'https://cors1.azurewebsites.net/api/values/GetValues2'` per `'https://cors3.azurewebsites.net'` recuperare dall'origine è stato bloccato dai criteri CORS: non è presente alcuna intestazione 'Access-Control-Allow-Origin' sulla risorsa richiesta.</span><span class="sxs-lookup"><span data-stu-id="99adf-375">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="99adf-376">Se si ritiene soddisfacente una risposta opaca, impostare la modalità della richiesta 'no-cors' per recuperare la risorsa con CORS disabilitato.</span><span class="sxs-lookup"><span data-stu-id="99adf-376">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="99adf-377">Gli endpoint abilitati per CORS possono essere testati con uno strumento, ad esempio [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)o [Postman](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="99adf-377">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="99adf-378">Quando si utilizza uno strumento, l'origine della richiesta specificata dall'intestazione `Origin` deve essere diversa dall'host che riceve la richiesta.</span><span class="sxs-lookup"><span data-stu-id="99adf-378">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="99adf-379">Se la richiesta non è *di origine incrociata* in base al valore dell'intestazione: `Origin`</span><span class="sxs-lookup"><span data-stu-id="99adf-379">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="99adf-380">Non è necessario che il middleware CORS elabori la richiesta.</span><span class="sxs-lookup"><span data-stu-id="99adf-380">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="99adf-381">Le intestazioni CORS non vengono restituite nella risposta.</span><span class="sxs-lookup"><span data-stu-id="99adf-381">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="99adf-382">Il comando `curl` seguente utilizza per inviare una richiesta OPTIONS con informazioni:</span><span class="sxs-lookup"><span data-stu-id="99adf-382">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="99adf-383">Test corS con routing degli endpoint e [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="99adf-383">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="99adf-384">L'abilitazione di CORS `RequireCors` in base all'endpoint che utilizza attualmente ***non*** supporta le richieste automatiche di [verifica preliminare.](#apf)</span><span class="sxs-lookup"><span data-stu-id="99adf-384">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="99adf-385">Si consideri il codice seguente che utilizza il [routing degli endpoint per abilitare CORS:](#ecors)</span><span class="sxs-lookup"><span data-stu-id="99adf-385">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="99adf-386">Di `TodoItems1Controller` seguito sono disponibili gli endpoint per il test:The following provides endpoints for testing:</span><span class="sxs-lookup"><span data-stu-id="99adf-386">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="99adf-387">Testare il codice precedente dalla pagina di [test](https://cors1.azurewebsites.net/test?number=1) [dell'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)distribuito.</span><span class="sxs-lookup"><span data-stu-id="99adf-387">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="99adf-388">I pulsanti **Elimina [EnableCors]** e **GET [EnableCors]** hanno esito positivo, perché gli endpoint hanno `[EnableCors]` e rispondono alle richieste di verifica preliminare.</span><span class="sxs-lookup"><span data-stu-id="99adf-388">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="99adf-389">Gli altri endpoint hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="99adf-389">The other endpoints fails.</span></span> <span data-ttu-id="99adf-390">Il pulsante **GET** ha esito negativo perché [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) invia:</span><span class="sxs-lookup"><span data-stu-id="99adf-390">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="99adf-391">Di `TodoItems2Controller` seguito vengono forniti endpoint simili, ma viene fornito codice esplicito per rispondere alle richieste OPTIONS:The following provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span><span class="sxs-lookup"><span data-stu-id="99adf-391">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="99adf-392">Testare il codice precedente dalla pagina di [test](https://cors1.azurewebsites.net/test?number=2) dell'esempio distribuito.</span><span class="sxs-lookup"><span data-stu-id="99adf-392">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="99adf-393">Nell'elenco a discesa **Controller** selezionare **Verifica preliminare** e quindi **Imposta controller**.</span><span class="sxs-lookup"><span data-stu-id="99adf-393">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="99adf-394">Tutte le chiamate CORS agli `TodoItems2Controller` endpoint hanno esito positivo.</span><span class="sxs-lookup"><span data-stu-id="99adf-394">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="99adf-395">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="99adf-395">Additional resources</span></span>

* [<span data-ttu-id="99adf-396">Condivisione risorse tra le origini (CORS)</span><span class="sxs-lookup"><span data-stu-id="99adf-396">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="99adf-397">Introduzione al modulo IIS CORS</span><span class="sxs-lookup"><span data-stu-id="99adf-397">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="99adf-398">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="99adf-398">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="99adf-399">Questo articolo illustra come abilitare CORS in un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="99adf-399">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="99adf-400">La sicurezza del browser impedisce a una pagina Web di effettuare richieste a un dominio diverso da quello che ha servito la pagina web.</span><span class="sxs-lookup"><span data-stu-id="99adf-400">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="99adf-401">Questa restrizione è *denominata criterio*della stessa origine .</span><span class="sxs-lookup"><span data-stu-id="99adf-401">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="99adf-402">I criteri della stessa origine impediscono a un sito dannoso di leggere dati sensibili da un altro sito.</span><span class="sxs-lookup"><span data-stu-id="99adf-402">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="99adf-403">In alcuni altri punti, potresti voler consentire ad altri siti di effettuare richieste cross-origin alla tua app.</span><span class="sxs-lookup"><span data-stu-id="99adf-403">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="99adf-404">Per ulteriori informazioni, vedere l'articolo di [Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="99adf-404">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="99adf-405">[Condivisione delle risorse tra origini](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="99adf-405">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="99adf-406">È uno standard W3C che consente a un server di ridurre la stessa origine.</span><span class="sxs-lookup"><span data-stu-id="99adf-406">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="99adf-407">**Non** è una funzione di sicurezza, CORS rilassa la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="99adf-407">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="99adf-408">Un'API non è più sicura consentendo CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-408">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="99adf-409">Per ulteriori informazioni, consultate [Funzionamento di CORS.](#how-cors)</span><span class="sxs-lookup"><span data-stu-id="99adf-409">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="99adf-410">Consente a un server di consentire in modo esplicito alcune richieste tra origini rifiutandone altre.</span><span class="sxs-lookup"><span data-stu-id="99adf-410">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="99adf-411">È più sicuro e più flessibile rispetto alle tecniche precedenti, ad esempio [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="99adf-411">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="99adf-412">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="99adf-412">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="99adf-413">Stessa origine</span><span class="sxs-lookup"><span data-stu-id="99adf-413">Same origin</span></span>

<span data-ttu-id="99adf-414">Due URL hanno la stessa origine se hanno schemi, host e porte identici ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="99adf-414">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="99adf-415">Questi due URL hanno la stessa origine:</span><span class="sxs-lookup"><span data-stu-id="99adf-415">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="99adf-416">Questi URL hanno origini diverse rispetto ai due URL precedenti:</span><span class="sxs-lookup"><span data-stu-id="99adf-416">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="99adf-417">`https://example.net`&ndash; Dominio diverso</span><span class="sxs-lookup"><span data-stu-id="99adf-417">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="99adf-418">`https://www.example.com/foo.html`&ndash; Sottodominio diverso</span><span class="sxs-lookup"><span data-stu-id="99adf-418">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="99adf-419">`http://example.com/foo.html`&ndash; Schema diverso</span><span class="sxs-lookup"><span data-stu-id="99adf-419">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="99adf-420">`https://example.com:9000/foo.html`&ndash; Porta diversa</span><span class="sxs-lookup"><span data-stu-id="99adf-420">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="99adf-421">Internet Explorer non considera la porta quando si confrontano le origini.</span><span class="sxs-lookup"><span data-stu-id="99adf-421">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="99adf-422">CORS con criteri denominati e middleware</span><span class="sxs-lookup"><span data-stu-id="99adf-422">CORS with named policy and middleware</span></span>

<span data-ttu-id="99adf-423">IL middleware CORS gestisce le richieste tra origini.</span><span class="sxs-lookup"><span data-stu-id="99adf-423">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="99adf-424">Il codice seguente abilita CORS per l'intera app con l'origine specificata:The following code enables CORS for the entire app with the specified origin:</span><span class="sxs-lookup"><span data-stu-id="99adf-424">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="99adf-425">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="99adf-425">The preceding code:</span></span>

* <span data-ttu-id="99adf-426">Imposta il nome\_del criterio su " myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="99adf-426">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="99adf-427">Il nome del criterio è arbitrario.</span><span class="sxs-lookup"><span data-stu-id="99adf-427">The policy name is arbitrary.</span></span>
* <span data-ttu-id="99adf-428">Chiama <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> il metodo di estensione, che abilita CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-428">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="99adf-429">Chiamate <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> con [un'espressione lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="99adf-429">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="99adf-430">L'espressione <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> lambda accetta un oggetto.</span><span class="sxs-lookup"><span data-stu-id="99adf-430">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="99adf-431">[Le opzioni](#cors-policy-options)di `WithOrigins`configurazione, ad esempio , sono descritte più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="99adf-431">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="99adf-432">La <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chiamata al metodo aggiunge servizi CORS al contenitore dei servizi dell'app:The method call adds CORS services to the app's service container:</span><span class="sxs-lookup"><span data-stu-id="99adf-432">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="99adf-433">Per ulteriori informazioni, vedere [Opzioni dei criteri CORS](#cpo) in questo documento .</span><span class="sxs-lookup"><span data-stu-id="99adf-433">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="99adf-434">Il <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> metodo può concatenare i metodi, come illustrato nel codice seguente:The method can chain methods, as shown in the following code:</span><span class="sxs-lookup"><span data-stu-id="99adf-434">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="99adf-435">Nota: l'URL **non** deve`/`contenere una barra finale ( ).</span><span class="sxs-lookup"><span data-stu-id="99adf-435">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="99adf-436">Se l'URL `/`termina con `false` , il confronto restituisce e non viene restituita alcuna intestazione.</span><span class="sxs-lookup"><span data-stu-id="99adf-436">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="99adf-437">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span><span class="sxs-lookup"><span data-stu-id="99adf-437">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="99adf-438">Nota: `UseCors` deve `UseMvc`essere chiamato prima di .</span><span class="sxs-lookup"><span data-stu-id="99adf-438">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="99adf-439">Vedere [Abilitare CORS in pagine Razor, controller e metodi](#ecors) di azione per applicare i criteri CORS a livello di pagina/controller/azione.</span><span class="sxs-lookup"><span data-stu-id="99adf-439">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="99adf-440">Vedere [Test CORS](#test) per istruzioni sul test di codice simile al codice precedente.</span><span class="sxs-lookup"><span data-stu-id="99adf-440">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="99adf-441">Abilitare CORS con attributi</span><span class="sxs-lookup"><span data-stu-id="99adf-441">Enable CORS with attributes</span></span>

<span data-ttu-id="99adf-442">L'attributo [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornisce un'alternativa all'applicazione di CORS a livello globale.</span><span class="sxs-lookup"><span data-stu-id="99adf-442">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="99adf-443">L'attributo `[EnableCors]` abilita CORS per i punti finali selezionati, anziché per tutti i punti finali.</span><span class="sxs-lookup"><span data-stu-id="99adf-443">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="99adf-444">Consente `[EnableCors]` di specificare `[EnableCors("{Policy String}")]` il criterio predefinito e di specificare un criterio.</span><span class="sxs-lookup"><span data-stu-id="99adf-444">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="99adf-445">L'attributo `[EnableCors]` può essere applicato a:</span><span class="sxs-lookup"><span data-stu-id="99adf-445">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="99adf-446">Pagina Razor`PageModel`</span><span class="sxs-lookup"><span data-stu-id="99adf-446">Razor Page `PageModel`</span></span>
* <span data-ttu-id="99adf-447">Controller</span><span class="sxs-lookup"><span data-stu-id="99adf-447">Controller</span></span>
* <span data-ttu-id="99adf-448">Metodo di azione del controller</span><span class="sxs-lookup"><span data-stu-id="99adf-448">Controller action method</span></span>

<span data-ttu-id="99adf-449">È possibile applicare criteri diversi a controller/modello `[EnableCors]` di pagina/azione con l'attributo .</span><span class="sxs-lookup"><span data-stu-id="99adf-449">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="99adf-450">Quando `[EnableCors]` l'attributo viene applicato a un metodo di azione/modello di pagina/controller e CORS è abilitato nel middleware, vengono applicati ***entrambi i*** criteri.</span><span class="sxs-lookup"><span data-stu-id="99adf-450">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="99adf-451">Si consiglia di ***non*** combinare le politiche.</span><span class="sxs-lookup"><span data-stu-id="99adf-451">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="99adf-452">Utilizzare `[EnableCors]` l'attributo o il middleware,**non entrambi**.</span><span class="sxs-lookup"><span data-stu-id="99adf-452">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="99adf-453">Quando `[EnableCors]`si utilizza , **non** definire un criterio predefinito.</span><span class="sxs-lookup"><span data-stu-id="99adf-453">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="99adf-454">Il codice seguente applica criteri diversi a ogni metodo:The following code applies a different policy to each method:</span><span class="sxs-lookup"><span data-stu-id="99adf-454">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="99adf-455">Il codice seguente crea un criterio predefinito `"AnotherPolicy"`CORS e un criterio denominato :</span><span class="sxs-lookup"><span data-stu-id="99adf-455">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="99adf-456">Disabilita CORS</span><span class="sxs-lookup"><span data-stu-id="99adf-456">Disable CORS</span></span>

<span data-ttu-id="99adf-457">L'attributo [ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) disabilita CORS per il controller/modello di pagina/azione.</span><span class="sxs-lookup"><span data-stu-id="99adf-457">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="99adf-458">Opzioni dei criteri CORS</span><span class="sxs-lookup"><span data-stu-id="99adf-458">CORS policy options</span></span>

<span data-ttu-id="99adf-459">In questa sezione vengono descritte le varie opzioni che è possibile impostare in un criterio CORS:</span><span class="sxs-lookup"><span data-stu-id="99adf-459">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="99adf-460">Impostare le origini consentite</span><span class="sxs-lookup"><span data-stu-id="99adf-460">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="99adf-461">Impostare i metodi HTTP consentiti</span><span class="sxs-lookup"><span data-stu-id="99adf-461">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="99adf-462">Impostare le intestazioni di richiesta consentite</span><span class="sxs-lookup"><span data-stu-id="99adf-462">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="99adf-463">Impostare le intestazioni delle risposte esposte</span><span class="sxs-lookup"><span data-stu-id="99adf-463">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="99adf-464">Credenziali nelle richieste tra originiCredentials in cross-origin requests</span><span class="sxs-lookup"><span data-stu-id="99adf-464">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="99adf-465">Impostare l'ora di scadenza preliminare</span><span class="sxs-lookup"><span data-stu-id="99adf-465">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="99adf-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>viene chiamato `Startup.ConfigureServices`in .</span><span class="sxs-lookup"><span data-stu-id="99adf-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="99adf-467">Per alcune opzioni, può essere utile leggere prima la sezione [Come CORS funziona.](#how-cors)</span><span class="sxs-lookup"><span data-stu-id="99adf-467">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="99adf-468">Impostare le origini consentite</span><span class="sxs-lookup"><span data-stu-id="99adf-468">Set the allowed origins</span></span>

<span data-ttu-id="99adf-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Consente le richieste CORS da tutte`http` `https`le origini con qualsiasi schema ( o ).</span><span class="sxs-lookup"><span data-stu-id="99adf-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="99adf-470">`AllowAnyOrigin`non è sicuro perché *qualsiasi sito Web* può effettuare richieste cross-origin all'app.</span><span class="sxs-lookup"><span data-stu-id="99adf-470">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="99adf-471">Specificando `AllowAnyOrigin` ed `AllowCredentials` è una configurazione non sicura e può comportare la falsificazione di richieste intersito.</span><span class="sxs-lookup"><span data-stu-id="99adf-471">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="99adf-472">Per un'app sicura, specificare un elenco esatto di origini se il client deve autorizzare se stesso ad accedere alle risorse del server.</span><span class="sxs-lookup"><span data-stu-id="99adf-472">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="99adf-473">`AllowAnyOrigin`influisce sulle richieste `Access-Control-Allow-Origin` di verifica preliminare e sull'intestazione.</span><span class="sxs-lookup"><span data-stu-id="99adf-473">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="99adf-474">Per altre informazioni, vedere la sezione [Richieste di verifica preliminare.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="99adf-474">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="99adf-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Imposta <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> la proprietà del criterio in modo che sia una funzione che consente alle origini di corrispondere a un dominio con caratteri jolly configurato durante la valutazione se l'origine è consentita.</span><span class="sxs-lookup"><span data-stu-id="99adf-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="99adf-476">Impostare i metodi HTTP consentiti</span><span class="sxs-lookup"><span data-stu-id="99adf-476">Set the allowed HTTP methods</span></span>

<span data-ttu-id="99adf-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="99adf-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="99adf-478">Consente qualsiasi metodo HTTP:</span><span class="sxs-lookup"><span data-stu-id="99adf-478">Allows any HTTP method:</span></span>
* <span data-ttu-id="99adf-479">Influisce sulle richieste `Access-Control-Allow-Methods` di verifica preliminare e sull'intestazione.</span><span class="sxs-lookup"><span data-stu-id="99adf-479">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="99adf-480">Per altre informazioni, vedere la sezione [Richieste di verifica preliminare.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="99adf-480">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="99adf-481">Impostare le intestazioni di richiesta consentite</span><span class="sxs-lookup"><span data-stu-id="99adf-481">Set the allowed request headers</span></span>

<span data-ttu-id="99adf-482">Per consentire l'invio di intestazioni specifiche in una richiesta <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> CORS, denominate intestazioni di richiesta *author*, chiamare e specificare le intestazioni consentite:</span><span class="sxs-lookup"><span data-stu-id="99adf-482">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="99adf-483">Per consentire tutte le <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>intestazioni di richiesta dell'autore, chiamare :</span><span class="sxs-lookup"><span data-stu-id="99adf-483">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="99adf-484">Questa impostazione influisce sulle `Access-Control-Request-Headers` richieste di verifica preliminare e sull'intestazione.</span><span class="sxs-lookup"><span data-stu-id="99adf-484">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="99adf-485">Per altre informazioni, vedere la sezione [Richieste di verifica preliminare.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="99adf-485">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="99adf-486">IL middleware CORS consente `Access-Control-Request-Headers` sempre l'invio di quattro intestazioni nell'oggetto indipendentemente dai valori configurati in CorsPolicy.Headers.</span><span class="sxs-lookup"><span data-stu-id="99adf-486">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="99adf-487">Questo elenco di intestazioni include:</span><span class="sxs-lookup"><span data-stu-id="99adf-487">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="99adf-488">Ad esempio, si consideri un'app configurata come segue:For example, consider an app configured as follows:</span><span class="sxs-lookup"><span data-stu-id="99adf-488">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="99adf-489">IL middleware CORS risponde correttamente a una richiesta `Content-Language` di verifica preliminare con l'intestazione della richiesta seguente perché è sempre inserita nella whitelist:</span><span class="sxs-lookup"><span data-stu-id="99adf-489">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="99adf-490">Impostare le intestazioni delle risposte esposte</span><span class="sxs-lookup"><span data-stu-id="99adf-490">Set the exposed response headers</span></span>

<span data-ttu-id="99adf-491">Per impostazione predefinita, il browser non espone tutte le intestazioni di risposta all'app.</span><span class="sxs-lookup"><span data-stu-id="99adf-491">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="99adf-492">Per ulteriori informazioni, vedere [W3C Cross-Origin Resource Sharing (Terminologia): Intestazione di risposta semplice](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="99adf-492">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="99adf-493">Le intestazioni di risposta disponibili per impostazione predefinita sono:The response headers that are available by default are:</span><span class="sxs-lookup"><span data-stu-id="99adf-493">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="99adf-494">La specifica CORS chiama queste intestazioni intestazioni di *risposta semplici*.</span><span class="sxs-lookup"><span data-stu-id="99adf-494">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="99adf-495">Per rendere disponibili altre intestazioni <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>per l'app, chiamare :</span><span class="sxs-lookup"><span data-stu-id="99adf-495">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="99adf-496">Credenziali nelle richieste tra originiCredentials in cross-origin requests</span><span class="sxs-lookup"><span data-stu-id="99adf-496">Credentials in cross-origin requests</span></span>

<span data-ttu-id="99adf-497">Le credenziali richiedono una gestione speciale in una richiesta CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-497">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="99adf-498">Per impostazione predefinita, il browser non invia credenziali con una richiesta cross-origin.</span><span class="sxs-lookup"><span data-stu-id="99adf-498">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="99adf-499">Le credenziali includono cookie e schemi di autenticazione HTTP.</span><span class="sxs-lookup"><span data-stu-id="99adf-499">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="99adf-500">Per inviare le credenziali con una richiesta `XMLHttpRequest.withCredentials` tra `true`origini diverse, il client deve impostare su .</span><span class="sxs-lookup"><span data-stu-id="99adf-500">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="99adf-501">Utilizzando `XMLHttpRequest` direttamente:</span><span class="sxs-lookup"><span data-stu-id="99adf-501">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="99adf-502">Utilizzo di jQuery:</span><span class="sxs-lookup"><span data-stu-id="99adf-502">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="99adf-503">Utilizzo [dell'API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="99adf-503">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="99adf-504">Il server deve consentire le credenziali.</span><span class="sxs-lookup"><span data-stu-id="99adf-504">The server must allow the credentials.</span></span> <span data-ttu-id="99adf-505">Per consentire le credenziali di <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>origine incrociata, chiamare :</span><span class="sxs-lookup"><span data-stu-id="99adf-505">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="99adf-506">La risposta HTTP `Access-Control-Allow-Credentials` include un'intestazione, che indica al browser che il server consente le credenziali per una richiesta tra origini diverse.</span><span class="sxs-lookup"><span data-stu-id="99adf-506">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="99adf-507">Se il browser invia le credenziali ma la `Access-Control-Allow-Credentials` risposta non include un'intestazione valida, il browser non espone la risposta all'app e la richiesta tra origini ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="99adf-507">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="99adf-508">Consentire credenziali tra origini è un rischio per la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="99adf-508">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="99adf-509">Un sito Web in un altro dominio può inviare le credenziali di un utente connesso all'app per conto dell'utente all'insaputa dell'utente.</span><span class="sxs-lookup"><span data-stu-id="99adf-509">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="99adf-510">La specifica CORS indica inoltre `"*"` che l'impostazione delle `Access-Control-Allow-Credentials` origini (tutte le origini) non è valida se l'intestazione è presente.</span><span class="sxs-lookup"><span data-stu-id="99adf-510">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="99adf-511">Richieste di verifica preliminare</span><span class="sxs-lookup"><span data-stu-id="99adf-511">Preflight requests</span></span>

<span data-ttu-id="99adf-512">Per alcune richieste CORS, il browser invia una richiesta aggiuntiva prima di effettuare la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="99adf-512">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="99adf-513">Questa richiesta viene definita *richiesta di verifica preliminare.*</span><span class="sxs-lookup"><span data-stu-id="99adf-513">This request is called a *preflight request*.</span></span> <span data-ttu-id="99adf-514">Il browser può ignorare la richiesta di verifica preliminare se sono vere le seguenti condizioni:</span><span class="sxs-lookup"><span data-stu-id="99adf-514">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="99adf-515">Il metodo di richiesta è GET, HEAD o POST.</span><span class="sxs-lookup"><span data-stu-id="99adf-515">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="99adf-516">L'app non imposta intestazioni `Accept`di `Accept-Language` `Content-Language`richiesta `Content-Type`diverse `Last-Event-ID`da , , , o .</span><span class="sxs-lookup"><span data-stu-id="99adf-516">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="99adf-517">L'intestazione, `Content-Type` se impostata, ha uno dei seguenti valori:</span><span class="sxs-lookup"><span data-stu-id="99adf-517">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="99adf-518">La regola sulle intestazioni di richiesta impostate per la `setRequestHeader` richiesta `XMLHttpRequest` client si applica alle intestazioni impostate dall'app chiamando sull'oggetto.</span><span class="sxs-lookup"><span data-stu-id="99adf-518">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="99adf-519">La specifica CORS chiama queste intestazioni *intestazioni di richiesta dell'autore.*</span><span class="sxs-lookup"><span data-stu-id="99adf-519">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="99adf-520">La regola non si applica alle intestazioni che `User-Agent` `Host`il `Content-Length`browser può impostare, ad esempio , o .</span><span class="sxs-lookup"><span data-stu-id="99adf-520">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="99adf-521">Di seguito è riportato un esempio di richiesta di verifica preliminare:The following is an example of a preflight request:</span><span class="sxs-lookup"><span data-stu-id="99adf-521">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="99adf-522">La richiesta di verifica preliminare utilizza il metodo HTTP OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="99adf-522">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="99adf-523">Include due intestazioni speciali:</span><span class="sxs-lookup"><span data-stu-id="99adf-523">It includes two special headers:</span></span>

* <span data-ttu-id="99adf-524">`Access-Control-Request-Method`: il metodo HTTP che verrà utilizzato per la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="99adf-524">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="99adf-525">`Access-Control-Request-Headers`: un elenco di intestazioni di richiesta impostate dall'app nella richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="99adf-525">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="99adf-526">Come indicato in precedenza, non sono incluse le `User-Agent`intestazioni impostate dal browser, ad esempio .</span><span class="sxs-lookup"><span data-stu-id="99adf-526">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="99adf-527">Quando CORS è abilitato con il criterio appropriato, ASP.NET Core risponde in genere automaticamente alle richieste di verifica preliminare CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-527">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="99adf-528">Vedere [l'attributo [HttpOptions] per le richieste di verifica preliminare](#pro).</span><span class="sxs-lookup"><span data-stu-id="99adf-528">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="99adf-529">Una richiesta di verifica preliminare CORS può includere un'intestazione, `Access-Control-Request-Headers` che indica al server le intestazioni inviate con la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="99adf-529">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="99adf-530">Per consentire intestazioni <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>specifiche, chiamare :</span><span class="sxs-lookup"><span data-stu-id="99adf-530">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="99adf-531">Per consentire tutte le <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>intestazioni di richiesta dell'autore, chiamare :</span><span class="sxs-lookup"><span data-stu-id="99adf-531">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="99adf-532">I browser non sono del tutto `Access-Control-Request-Headers`coerenti nel modo in cui impostano .</span><span class="sxs-lookup"><span data-stu-id="99adf-532">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="99adf-533">Se si impostano intestazioni `"*"` su <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>un valore diverso `Accept`da `Content-Type`(o utilizzare ), è necessario includere almeno , e `Origin`, oltre a tutte le intestazioni personalizzate che si desidera supportare.</span><span class="sxs-lookup"><span data-stu-id="99adf-533">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="99adf-534">Di seguito è riportato un esempio di risposta alla richiesta di verifica preliminare (presupponendo che il server consenta la richiesta):</span><span class="sxs-lookup"><span data-stu-id="99adf-534">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="99adf-535">La risposta `Access-Control-Allow-Methods` include un'intestazione che elenca `Access-Control-Allow-Headers` i metodi consentiti e, facoltativamente, un'intestazione, che elenca le intestazioni consentite.</span><span class="sxs-lookup"><span data-stu-id="99adf-535">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="99adf-536">Se la richiesta di verifica preliminare ha esito positivo, il browser invia la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="99adf-536">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="99adf-537">Se la richiesta di verifica preliminare viene negata, l'app restituisce una risposta *200 OK* ma non restituisce le intestazioni CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-537">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="99adf-538">Pertanto, il browser non tenta la richiesta cross-origin.</span><span class="sxs-lookup"><span data-stu-id="99adf-538">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="99adf-539">Impostare l'ora di scadenza preliminare</span><span class="sxs-lookup"><span data-stu-id="99adf-539">Set the preflight expiration time</span></span>

<span data-ttu-id="99adf-540">L'intestazione `Access-Control-Max-Age` specifica per quanto tempo la risposta alla richiesta di verifica preliminare può essere memorizzata nella cache.</span><span class="sxs-lookup"><span data-stu-id="99adf-540">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="99adf-541">Per impostare questa <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>intestazione, chiamare :</span><span class="sxs-lookup"><span data-stu-id="99adf-541">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="99adf-542">Come funziona CORS</span><span class="sxs-lookup"><span data-stu-id="99adf-542">How CORS works</span></span>

<span data-ttu-id="99adf-543">In questa sezione viene descritto cosa accade in una richiesta [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) a livello dei messaggi HTTP.</span><span class="sxs-lookup"><span data-stu-id="99adf-543">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="99adf-544">CORS **non** è una funzione di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="99adf-544">CORS is **not** a security feature.</span></span> <span data-ttu-id="99adf-545">CORS è uno standard W3C che consente a un server di ridurre i criteri della stessa origine.</span><span class="sxs-lookup"><span data-stu-id="99adf-545">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="99adf-546">Ad esempio, un attore malintenzionato potrebbe utilizzare [Impedisci Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) sul tuo sito ed eseguire una richiesta intersito al loro sito abilitato CORS per rubare informazioni.</span><span class="sxs-lookup"><span data-stu-id="99adf-546">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="99adf-547">La tua API non è più sicura consentendo CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-547">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="99adf-548">Spetta al client (browser) applicare CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-548">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="99adf-549">Il server esegue la richiesta e restituisce la risposta, è il client che restituisce un errore e blocca la risposta.</span><span class="sxs-lookup"><span data-stu-id="99adf-549">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="99adf-550">Ad esempio, uno dei seguenti strumenti visualizzerà la risposta del server:</span><span class="sxs-lookup"><span data-stu-id="99adf-550">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="99adf-551">Fiddler</span><span class="sxs-lookup"><span data-stu-id="99adf-551">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="99adf-552">Postman</span><span class="sxs-lookup"><span data-stu-id="99adf-552">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="99adf-553">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="99adf-553">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="99adf-554">Un browser Web immettendo l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="99adf-554">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="99adf-555">È un modo per un server per consentire ai browser di eseguire una richiesta [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) o [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) tra origini che altrimenti sarebbe vietata.</span><span class="sxs-lookup"><span data-stu-id="99adf-555">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="99adf-556">I browser (senza CORS) non possono eseguire richieste cross-origin.</span><span class="sxs-lookup"><span data-stu-id="99adf-556">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="99adf-557">Prima di CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) veniva usato per aggirare questa restrizione.</span><span class="sxs-lookup"><span data-stu-id="99adf-557">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="99adf-558">JSONP non usa XHR, usa `<script>` il tag per ricevere la risposta.</span><span class="sxs-lookup"><span data-stu-id="99adf-558">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="99adf-559">Gli script possono essere caricati tra origini diverse.</span><span class="sxs-lookup"><span data-stu-id="99adf-559">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="99adf-560">La [specifica CORS](https://www.w3.org/TR/cors/) ha introdotto diverse nuove intestazioni HTTP che abilitano le richieste tra origini diverse.</span><span class="sxs-lookup"><span data-stu-id="99adf-560">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="99adf-561">Se un browser supporta CORS, imposta automaticamente queste intestazioni per le richieste tra origini diverse.</span><span class="sxs-lookup"><span data-stu-id="99adf-561">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="99adf-562">Il codice JavaScript personalizzato non è necessario per abilitare CORS.</span><span class="sxs-lookup"><span data-stu-id="99adf-562">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="99adf-563">Di seguito è riportato un esempio di richiesta tra origini.</span><span class="sxs-lookup"><span data-stu-id="99adf-563">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="99adf-564">L'intestazione `Origin` fornisce il dominio del sito che effettua la richiesta.</span><span class="sxs-lookup"><span data-stu-id="99adf-564">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="99adf-565">L'intestazione `Origin` è obbligatoria e deve essere diversa dall'host.</span><span class="sxs-lookup"><span data-stu-id="99adf-565">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="99adf-566">Se il server consente la `Access-Control-Allow-Origin` richiesta, imposta l'intestazione nella risposta.</span><span class="sxs-lookup"><span data-stu-id="99adf-566">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="99adf-567">Il valore di questa `Origin` intestazione corrisponde all'intestazione `"*"`della richiesta o è il valore jolly , ovvero è consentita qualsiasi origine:</span><span class="sxs-lookup"><span data-stu-id="99adf-567">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="99adf-568">Se la risposta non `Access-Control-Allow-Origin` include l'intestazione, la richiesta cross-origin ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="99adf-568">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="99adf-569">In particolare, il browser non consente la richiesta.</span><span class="sxs-lookup"><span data-stu-id="99adf-569">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="99adf-570">Anche se il server restituisce una risposta corretta, il browser non rende disponibile la risposta all'app client.</span><span class="sxs-lookup"><span data-stu-id="99adf-570">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="99adf-571">Testare CORS</span><span class="sxs-lookup"><span data-stu-id="99adf-571">Test CORS</span></span>

<span data-ttu-id="99adf-572">Per testare CORS:</span><span class="sxs-lookup"><span data-stu-id="99adf-572">To test CORS:</span></span>

1. <span data-ttu-id="99adf-573">[Creare un progetto API.](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="99adf-573">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="99adf-574">In alternativa, è possibile [scaricare l'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="99adf-574">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="99adf-575">Abilitare CORS utilizzando uno degli approcci in questo documento.</span><span class="sxs-lookup"><span data-stu-id="99adf-575">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="99adf-576">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="99adf-576">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="99adf-577">`WithOrigins("https://localhost:<port>");`deve essere usato solo per testare un'app di esempio simile al [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)scaricato.</span><span class="sxs-lookup"><span data-stu-id="99adf-577">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="99adf-578">Creare un progetto di app Web (pagine Razor o MVC).</span><span class="sxs-lookup"><span data-stu-id="99adf-578">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="99adf-579">L'esempio usa pagine Razor.The sample uses Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="99adf-579">The sample uses Razor Pages.</span></span> <span data-ttu-id="99adf-580">È possibile creare l'app Web nella stessa soluzione del progetto API.</span><span class="sxs-lookup"><span data-stu-id="99adf-580">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="99adf-581">Aggiungere il codice evidenziato seguente al file *Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="99adf-581">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="99adf-582">Nel codice precedente sostituire `url: 'https://<web app>.azurewebsites.net/api/values/1',` con l'URL dell'app distribuita.</span><span class="sxs-lookup"><span data-stu-id="99adf-582">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="99adf-583">Distribuire il progetto API.</span><span class="sxs-lookup"><span data-stu-id="99adf-583">Deploy the API project.</span></span> <span data-ttu-id="99adf-584">Ad esempio, [distribuire in Azure .For](xref:host-and-deploy/azure-apps/index)example, deploy to Azure .</span><span class="sxs-lookup"><span data-stu-id="99adf-584">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="99adf-585">Eseguire le pagine Razor o l'app MVC dal desktop e fare clic sul pulsante **Test.**</span><span class="sxs-lookup"><span data-stu-id="99adf-585">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="99adf-586">Utilizzare gli strumenti F12 per esaminare i messaggi di errore.</span><span class="sxs-lookup"><span data-stu-id="99adf-586">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="99adf-587">Rimuovere l'origine `WithOrigins` localhost da e distribuire l'app.</span><span class="sxs-lookup"><span data-stu-id="99adf-587">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="99adf-588">In alternativa, eseguire l'app client con una porta diversa.</span><span class="sxs-lookup"><span data-stu-id="99adf-588">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="99adf-589">Ad esempio, eseguire da Visual Studio.For example, run from Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="99adf-589">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="99adf-590">Eseguire il test con l'app client.</span><span class="sxs-lookup"><span data-stu-id="99adf-590">Test with the client app.</span></span> <span data-ttu-id="99adf-591">Gli errori CORS restituiscono un errore, ma il messaggio di errore non è disponibile per JavaScript.</span><span class="sxs-lookup"><span data-stu-id="99adf-591">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="99adf-592">Utilizzare la scheda della console negli strumenti F12 per visualizzare l'errore.</span><span class="sxs-lookup"><span data-stu-id="99adf-592">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="99adf-593">A seconda del browser, viene visualizzato un errore (nella console degli strumenti F12) simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="99adf-593">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="99adf-594">Utilizzo di Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="99adf-594">Using Microsoft Edge:</span></span>

     <span data-ttu-id="99adf-595">**SEC7120: [CORS] `https://localhost:44375` L'origine `https://localhost:44375` non è stata trovata nell'intestazione di risposta Access-Control-Allow-Origin per la risorsa cross-origin in`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="99adf-595">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="99adf-596">Utilizzo di Chrome:</span><span class="sxs-lookup"><span data-stu-id="99adf-596">Using Chrome:</span></span>

     <span data-ttu-id="99adf-597">**L'accesso a `https://webapi.azurewebsites.net/api/values/1` XMLHttpRequest all'origine `https://localhost:44375` è stato bloccato dai criteri CORS: non è presente alcuna intestazione 'Access-Control-Allow-Origin' nella risorsa richiesta.**</span><span class="sxs-lookup"><span data-stu-id="99adf-597">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="99adf-598">Gli endpoint abilitati per CORS possono essere testati con uno strumento, ad esempio [Fiddler](https://www.telerik.com/fiddler) o [Postman](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="99adf-598">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="99adf-599">Quando si utilizza uno strumento, l'origine della richiesta specificata dall'intestazione `Origin` deve essere diversa dall'host che riceve la richiesta.</span><span class="sxs-lookup"><span data-stu-id="99adf-599">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="99adf-600">Se la richiesta non è *di origine incrociata* in base al valore dell'intestazione: `Origin`</span><span class="sxs-lookup"><span data-stu-id="99adf-600">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="99adf-601">Non è necessario che il middleware CORS elabori la richiesta.</span><span class="sxs-lookup"><span data-stu-id="99adf-601">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="99adf-602">Le intestazioni CORS non vengono restituite nella risposta.</span><span class="sxs-lookup"><span data-stu-id="99adf-602">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="99adf-603">CORS in IIS</span><span class="sxs-lookup"><span data-stu-id="99adf-603">CORS in IIS</span></span>

<span data-ttu-id="99adf-604">Durante la distribuzione in IIS, CORS deve essere eseguito prima dell'autenticazione di Windows se il server non è configurato per consentire l'accesso anonimo.</span><span class="sxs-lookup"><span data-stu-id="99adf-604">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="99adf-605">Per supportare questo scenario, il [modulo IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) deve essere installato e configurato per l'app.</span><span class="sxs-lookup"><span data-stu-id="99adf-605">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="99adf-606">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="99adf-606">Additional resources</span></span>

* [<span data-ttu-id="99adf-607">Condivisione risorse tra le origini (CORS)</span><span class="sxs-lookup"><span data-stu-id="99adf-607">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="99adf-608">Introduzione al modulo IIS CORS</span><span class="sxs-lookup"><span data-stu-id="99adf-608">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
