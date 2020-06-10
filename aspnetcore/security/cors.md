---
title: Abilitare le richieste tra le origini (CORS) in ASP.NET Core
author: rick-anderson
description: Informazioni su come CORS come standard per consentire o rifiutare le richieste tra origini in un'app ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cors
ms.openlocfilehash: a78aff2d2e16f36ed034e6af110d7ed763271583
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84105753"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="25f27-103">Abilitare le richieste tra le origini (CORS) in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="25f27-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="25f27-104">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="25f27-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="25f27-105">Questo articolo illustra come abilitare CORS in un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="25f27-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="25f27-106">La sicurezza del browser impedisce a una pagina Web di eseguire richieste a un dominio diverso da quello che ha gestito la pagina Web.</span><span class="sxs-lookup"><span data-stu-id="25f27-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="25f27-107">Questa restrizione è detta *criterio della stessa origine*.</span><span class="sxs-lookup"><span data-stu-id="25f27-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="25f27-108">Il criterio della stessa origine impedisce a un sito dannoso di leggere dati sensibili da un altro sito.</span><span class="sxs-lookup"><span data-stu-id="25f27-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="25f27-109">In alcuni casi potrebbe essere necessario consentire ad altri siti di effettuare richieste tra le origini per l'app.</span><span class="sxs-lookup"><span data-stu-id="25f27-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="25f27-110">Per altre informazioni, vedere l' [articolo relativo a Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="25f27-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="25f27-111">[Condivisione risorse tra le origini](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="25f27-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="25f27-112">È uno standard W3C che consente a un server di ridurre i criteri della stessa origine.</span><span class="sxs-lookup"><span data-stu-id="25f27-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="25f27-113">**Non** è una funzionalità di sicurezza, CORS rilassa la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="25f27-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="25f27-114">Un'API non è più sicura consentendo CORS.</span><span class="sxs-lookup"><span data-stu-id="25f27-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="25f27-115">Per ulteriori informazioni, vedere [funzionamento di CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="25f27-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="25f27-116">Consente a un server di consentire in modo esplicito alcune richieste tra origini rifiutando altre.</span><span class="sxs-lookup"><span data-stu-id="25f27-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="25f27-117">È più sicuro e flessibile rispetto alle tecniche precedenti, ad esempio [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="25f27-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="25f27-118">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="25f27-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="25f27-119">Stessa origine</span><span class="sxs-lookup"><span data-stu-id="25f27-119">Same origin</span></span>

<span data-ttu-id="25f27-120">Due URL hanno la stessa origine se hanno schemi, host e porte identici ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="25f27-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="25f27-121">Questi due URL hanno la stessa origine:</span><span class="sxs-lookup"><span data-stu-id="25f27-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="25f27-122">Questi URL hanno origini diverse da quelle dei due URL precedenti:</span><span class="sxs-lookup"><span data-stu-id="25f27-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="25f27-123">`https://example.net`: Dominio diverso</span><span class="sxs-lookup"><span data-stu-id="25f27-123">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="25f27-124">`https://www.example.com/foo.html`: Sottodominio diverso</span><span class="sxs-lookup"><span data-stu-id="25f27-124">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="25f27-125">`http://example.com/foo.html`: Schema diverso</span><span class="sxs-lookup"><span data-stu-id="25f27-125">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="25f27-126">`https://example.com:9000/foo.html`: Porta diversa</span><span class="sxs-lookup"><span data-stu-id="25f27-126">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="25f27-127">Abilitare CORS</span><span class="sxs-lookup"><span data-stu-id="25f27-127">Enable CORS</span></span>

<span data-ttu-id="25f27-128">Sono disponibili tre modi per abilitare CORS:</span><span class="sxs-lookup"><span data-stu-id="25f27-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="25f27-129">Nel middleware usando un criterio [denominato](#np) o un [criterio predefinito](#dp).</span><span class="sxs-lookup"><span data-stu-id="25f27-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="25f27-130">Uso del [routing degli endpoint](#ecors).</span><span class="sxs-lookup"><span data-stu-id="25f27-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="25f27-131">Con l'attributo [[EnableCors]](#attr) .</span><span class="sxs-lookup"><span data-stu-id="25f27-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="25f27-132">L'utilizzo dell'attributo [[EnableCors]](#attr) con un criterio denominato fornisce il controllo più raffinato per limitare gli endpoint che supportano CORS.</span><span class="sxs-lookup"><span data-stu-id="25f27-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="25f27-133">Ogni approccio è descritto in dettaglio nelle sezioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="25f27-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="25f27-134">CORS con criteri e middleware denominati</span><span class="sxs-lookup"><span data-stu-id="25f27-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="25f27-135">Il middleware CORS gestisce le richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="25f27-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="25f27-136">Il codice seguente applica un criterio CORS a tutti gli endpoint dell'app con le origini specificate:</span><span class="sxs-lookup"><span data-stu-id="25f27-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="25f27-137">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="25f27-137">The preceding code:</span></span>

* <span data-ttu-id="25f27-138">Imposta il nome dei criteri su `_myAllowSpecificOrigins` .</span><span class="sxs-lookup"><span data-stu-id="25f27-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="25f27-139">Il nome del criterio è arbitrario.</span><span class="sxs-lookup"><span data-stu-id="25f27-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="25f27-140">Chiama il <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodo di estensione e specifica il `_myAllowSpecificOrigins` criterio CORS.</span><span class="sxs-lookup"><span data-stu-id="25f27-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="25f27-141">`UseCors`aggiunge il middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="25f27-141">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="25f27-142">La chiamata a `UseCors` deve essere posizionata dopo `UseRouting` , ma prima di `UseAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="25f27-142">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="25f27-143">Per ulteriori informazioni, vedere l' [ordine del middleware](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="25f27-143">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="25f27-144">Chiama <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> con un' [espressione lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="25f27-144">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="25f27-145">L'espressione lambda accetta un <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> oggetto.</span><span class="sxs-lookup"><span data-stu-id="25f27-145">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="25f27-146">Le [Opzioni di configurazione](#cors-policy-options), ad esempio `WithOrigins` , sono descritte più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="25f27-146">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="25f27-147">Abilita il `_myAllowSpecificOrigins` criterio CORS per tutti gli endpoint del controller.</span><span class="sxs-lookup"><span data-stu-id="25f27-147">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="25f27-148">Vedere [endpoint routing](#ecors) per applicare un criterio CORS a endpoint specifici.</span><span class="sxs-lookup"><span data-stu-id="25f27-148">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="25f27-149">Con il routing dell'endpoint è **necessario** configurare il middleware CORS per l'esecuzione tra le chiamate a `UseRouting` e `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="25f27-149">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="25f27-150">Vedere [test CORS](#testc) per istruzioni sul test di codice simile al codice precedente.</span><span class="sxs-lookup"><span data-stu-id="25f27-150">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="25f27-151">La <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chiamata al metodo aggiunge i servizi CORS al contenitore del servizio dell'app:</span><span class="sxs-lookup"><span data-stu-id="25f27-151">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="25f27-152">Per ulteriori informazioni, vedere [Opzioni dei criteri CORS](#cpo) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="25f27-152">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="25f27-153">I <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> metodi possono essere concatenati, come illustrato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="25f27-153">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="25f27-154">Nota: l'URL specificato **non** deve contenere una barra finale ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="25f27-154">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="25f27-155">Se l'URL termina con `/` , il confronto restituisce `false` e non viene restituita alcuna intestazione.</span><span class="sxs-lookup"><span data-stu-id="25f27-155">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="25f27-156">CORS con criteri e middleware predefiniti</span><span class="sxs-lookup"><span data-stu-id="25f27-156">CORS with default policy and middleware</span></span>

<span data-ttu-id="25f27-157">Il codice evidenziato seguente Abilita i criteri predefiniti di CORS:</span><span class="sxs-lookup"><span data-stu-id="25f27-157">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="25f27-158">Il codice precedente applica i criteri predefiniti di CORS a tutti gli endpoint del controller.</span><span class="sxs-lookup"><span data-stu-id="25f27-158">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="25f27-159">Abilitare cors con routing degli endpoint</span><span class="sxs-lookup"><span data-stu-id="25f27-159">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="25f27-160">L'abilitazione di CORS in base all'endpoint `RequireCors` attualmente in **uso non** supporta [le richieste preliminari automatiche](#apf).</span><span class="sxs-lookup"><span data-stu-id="25f27-160">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="25f27-161">Per ulteriori informazioni, vedere [questo problema di GitHub](https://github.com/dotnet/aspnetcore/issues/20709) e [testare cors con endpoint routing e [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="25f27-161">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="25f27-162">Con il routing degli endpoint, è possibile abilitare CORS in base all'endpoint usando il <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set di metodi di estensione:</span><span class="sxs-lookup"><span data-stu-id="25f27-162">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="25f27-163">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="25f27-163">In the preceding code:</span></span>

* <span data-ttu-id="25f27-164">`app.UseCors`Abilita il middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="25f27-164">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="25f27-165">Poiché un criterio predefinito non è stato configurato, `app.UseCors()` da solo non Abilita CORS.</span><span class="sxs-lookup"><span data-stu-id="25f27-165">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="25f27-166">Gli `/echo` endpoint del controller e consentono richieste tra le origini usando i criteri specificati.</span><span class="sxs-lookup"><span data-stu-id="25f27-166">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="25f27-167">Gli `/echo2` Razor endpoint delle pagine e non **not** consentono richieste tra le origini perché non è stato specificato alcun criterio predefinito.</span><span class="sxs-lookup"><span data-stu-id="25f27-167">The `/echo2` and Razor Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="25f27-168">L'attributo [[DisableCors]](#dc) non **Disabilita CORS** che è stato abilitato dal routing di endpoint con `RequireCors` .</span><span class="sxs-lookup"><span data-stu-id="25f27-168">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="25f27-169">Per istruzioni sul test del codice simile a quello precedente, vedere [test CORS with endpoint routing e [HttpOptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="25f27-169">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="25f27-170">Abilita CORS con attributi</span><span class="sxs-lookup"><span data-stu-id="25f27-170">Enable CORS with attributes</span></span>

<span data-ttu-id="25f27-171">L'abilitazione di CORS con l'attributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) e l'applicazione di un criterio denominato solo agli endpoint che richiedono CORS fornisce il controllo migliore.</span><span class="sxs-lookup"><span data-stu-id="25f27-171">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="25f27-172">L'attributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) rappresenta un'alternativa all'applicazione di CORS a livello globale.</span><span class="sxs-lookup"><span data-stu-id="25f27-172">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="25f27-173">L' `[EnableCors]` attributo Abilita CORS per gli endpoint selezionati, anziché tutti gli endpoint:</span><span class="sxs-lookup"><span data-stu-id="25f27-173">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="25f27-174">`[EnableCors]`Specifica i criteri predefiniti.</span><span class="sxs-lookup"><span data-stu-id="25f27-174">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="25f27-175">`[EnableCors("{Policy String}")]`Specifica un criterio denominato.</span><span class="sxs-lookup"><span data-stu-id="25f27-175">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="25f27-176">L' `[EnableCors]` attributo può essere applicato a:</span><span class="sxs-lookup"><span data-stu-id="25f27-176">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="25f27-177">Pagina`PageModel`</span><span class="sxs-lookup"><span data-stu-id="25f27-177"> Page `PageModel`</span></span>
* <span data-ttu-id="25f27-178">Controller</span><span class="sxs-lookup"><span data-stu-id="25f27-178">Controller</span></span>
* <span data-ttu-id="25f27-179">Metodo di azione del controller</span><span class="sxs-lookup"><span data-stu-id="25f27-179">Controller action method</span></span>

<span data-ttu-id="25f27-180">È possibile applicare criteri diversi ai controller, ai modelli di pagina o ai metodi di azione con l' `[EnableCors]` attributo.</span><span class="sxs-lookup"><span data-stu-id="25f27-180">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="25f27-181">Quando l' `[EnableCors]` attributo viene applicato a un controller, a un modello di pagina o a un metodo di azione e CORS è abilitato nel middleware, vengono applicati **entrambi** i criteri.</span><span class="sxs-lookup"><span data-stu-id="25f27-181">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="25f27-182">**È consigliabile evitare di combinare i criteri. Usare l'** `[EnableCors]` **attributo o il middleware, non entrambi nella stessa app.**</span><span class="sxs-lookup"><span data-stu-id="25f27-182">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="25f27-183">Il codice seguente applica un criterio diverso a ogni metodo:</span><span class="sxs-lookup"><span data-stu-id="25f27-183">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="25f27-184">Il codice seguente crea due criteri CORS:</span><span class="sxs-lookup"><span data-stu-id="25f27-184">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="25f27-185">Per il controllo più raffinato della limitazione delle richieste CORS:</span><span class="sxs-lookup"><span data-stu-id="25f27-185">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="25f27-186">Usare `[EnableCors("MyPolicy")]` con un criterio denominato.</span><span class="sxs-lookup"><span data-stu-id="25f27-186">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="25f27-187">Non definire un criterio predefinito.</span><span class="sxs-lookup"><span data-stu-id="25f27-187">Don't define a default policy.</span></span>
* <span data-ttu-id="25f27-188">Non usare il [routing degli endpoint](#ecors).</span><span class="sxs-lookup"><span data-stu-id="25f27-188">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="25f27-189">Il codice nella sezione successiva soddisfa l'elenco precedente.</span><span class="sxs-lookup"><span data-stu-id="25f27-189">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="25f27-190">Vedere [test CORS](#testc) per istruzioni sul test di codice simile al codice precedente.</span><span class="sxs-lookup"><span data-stu-id="25f27-190">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="25f27-191">Disabilitare CORS</span><span class="sxs-lookup"><span data-stu-id="25f27-191">Disable CORS</span></span>

<span data-ttu-id="25f27-192">L'attributo [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) non **Disabilita CORS** che è stato abilitato dal routing dell' [endpoint](#ecors).</span><span class="sxs-lookup"><span data-stu-id="25f27-192">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="25f27-193">Il codice seguente definisce i criteri CORS `"MyPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="25f27-193">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="25f27-194">Il codice seguente Disabilita CORS per l' `GetValues2` azione:</span><span class="sxs-lookup"><span data-stu-id="25f27-194">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="25f27-195">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="25f27-195">The preceding code:</span></span>

* <span data-ttu-id="25f27-196">Non Abilita CORS con [routing endpoint](#ecors).</span><span class="sxs-lookup"><span data-stu-id="25f27-196">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="25f27-197">Non definisce un [criterio CORS predefinito](#dp).</span><span class="sxs-lookup"><span data-stu-id="25f27-197">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="25f27-198">USA [[EnableCors ("criteri")]](#attr) per abilitare il `"MyPolicy"` criterio CORS per il controller.</span><span class="sxs-lookup"><span data-stu-id="25f27-198">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="25f27-199">Disabilita CORS per il `GetValues2` metodo.</span><span class="sxs-lookup"><span data-stu-id="25f27-199">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="25f27-200">Vedere [test CORS](#testc) per istruzioni sul test del codice precedente.</span><span class="sxs-lookup"><span data-stu-id="25f27-200">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="25f27-201">Opzioni dei criteri di CORS</span><span class="sxs-lookup"><span data-stu-id="25f27-201">CORS policy options</span></span>

<span data-ttu-id="25f27-202">In questa sezione vengono descritte le varie opzioni che è possibile impostare in un criterio CORS:</span><span class="sxs-lookup"><span data-stu-id="25f27-202">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="25f27-203">Imposta le origini consentite</span><span class="sxs-lookup"><span data-stu-id="25f27-203">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="25f27-204">Impostare i metodi HTTP consentiti</span><span class="sxs-lookup"><span data-stu-id="25f27-204">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="25f27-205">Impostare le intestazioni della richiesta consentita</span><span class="sxs-lookup"><span data-stu-id="25f27-205">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="25f27-206">Impostare le intestazioni di risposta esposte</span><span class="sxs-lookup"><span data-stu-id="25f27-206">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="25f27-207">Credenziali nelle richieste tra le origini</span><span class="sxs-lookup"><span data-stu-id="25f27-207">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="25f27-208">Imposta la data di scadenza preliminare</span><span class="sxs-lookup"><span data-stu-id="25f27-208">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="25f27-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>viene chiamato in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="25f27-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="25f27-210">Per alcune opzioni, può essere utile leggere prima la sezione [come funziona CORS](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="25f27-210">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="25f27-211">Imposta le origini consentite</span><span class="sxs-lookup"><span data-stu-id="25f27-211">Set the allowed origins</span></span>

<span data-ttu-id="25f27-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Consente le richieste CORS da tutte le origini con qualsiasi schema ( `http` o `https` ).</span><span class="sxs-lookup"><span data-stu-id="25f27-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="25f27-213">`AllowAnyOrigin`non è sicuro perché *qualsiasi sito Web* può effettuare richieste tra origini per l'app.</span><span class="sxs-lookup"><span data-stu-id="25f27-213">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="25f27-214">La definizione di `AllowAnyOrigin` e `AllowCredentials` è una configurazione non sicura e può comportare la falsificazione della richiesta tra siti.</span><span class="sxs-lookup"><span data-stu-id="25f27-214">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="25f27-215">Il servizio CORS restituisce una risposta CORS non valida quando un'app è configurata con entrambi i metodi.</span><span class="sxs-lookup"><span data-stu-id="25f27-215">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="25f27-216">`AllowAnyOrigin`influiscono sulle richieste preliminari e sull' `Access-Control-Allow-Origin` intestazione.</span><span class="sxs-lookup"><span data-stu-id="25f27-216">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="25f27-217">Per ulteriori informazioni, vedere la sezione [preflight requests](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="25f27-217">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="25f27-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Imposta la <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> proprietà dei criteri in modo che sia una funzione che consente alle origini di corrispondere a un dominio con caratteri jolly configurato durante la valutazione se l'origine è consentita.</span><span class="sxs-lookup"><span data-stu-id="25f27-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="25f27-219">Impostare i metodi HTTP consentiti</span><span class="sxs-lookup"><span data-stu-id="25f27-219">Set the allowed HTTP methods</span></span>

<span data-ttu-id="25f27-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="25f27-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="25f27-221">Consente qualsiasi metodo HTTP:</span><span class="sxs-lookup"><span data-stu-id="25f27-221">Allows any HTTP method:</span></span>
* <span data-ttu-id="25f27-222">Influiscono sulle richieste preliminari e sull' `Access-Control-Allow-Methods` intestazione.</span><span class="sxs-lookup"><span data-stu-id="25f27-222">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="25f27-223">Per ulteriori informazioni, vedere la sezione [preflight requests](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="25f27-223">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="25f27-224">Impostare le intestazioni della richiesta consentita</span><span class="sxs-lookup"><span data-stu-id="25f27-224">Set the allowed request headers</span></span>

<span data-ttu-id="25f27-225">Per consentire l'invio di intestazioni specifiche in una richiesta CORS, denominate [intestazioni richiesta autore](https://xhr.spec.whatwg.org/#request), chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> e specificare le intestazioni consentite:</span><span class="sxs-lookup"><span data-stu-id="25f27-225">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="25f27-226">Per consentire tutte le [intestazioni di richiesta di autore](https://www.w3.org/TR/cors/#author-request-headers), chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="25f27-226">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="25f27-227">`AllowAnyHeader`influiscono sulle richieste preliminari e sull'intestazione [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) .</span><span class="sxs-lookup"><span data-stu-id="25f27-227">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="25f27-228">Per ulteriori informazioni, vedere la sezione [preflight requests](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="25f27-228">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="25f27-229">Un criterio middleware CORS corrisponde a intestazioni specifiche specificate da `WithHeaders` è possibile solo quando le intestazioni inviate `Access-Control-Request-Headers` corrispondono esattamente alle intestazioni indicate in `WithHeaders` .</span><span class="sxs-lookup"><span data-stu-id="25f27-229">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="25f27-230">Si consideri, ad esempio, un'app configurata come segue:</span><span class="sxs-lookup"><span data-stu-id="25f27-230">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="25f27-231">Il middleware CORS rifiuta una richiesta preliminare con la seguente intestazione di richiesta perché `Content-Language` ([HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) non è elencato in `WithHeaders` :</span><span class="sxs-lookup"><span data-stu-id="25f27-231">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="25f27-232">L'app restituisce una risposta *200 OK* , ma non invia le intestazioni CORS.</span><span class="sxs-lookup"><span data-stu-id="25f27-232">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="25f27-233">Pertanto, il browser non tenta di eseguire la richiesta tra origini.</span><span class="sxs-lookup"><span data-stu-id="25f27-233">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="25f27-234">Impostare le intestazioni di risposta esposte</span><span class="sxs-lookup"><span data-stu-id="25f27-234">Set the exposed response headers</span></span>

<span data-ttu-id="25f27-235">Per impostazione predefinita, il browser non espone tutte le intestazioni di risposta all'app.</span><span class="sxs-lookup"><span data-stu-id="25f27-235">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="25f27-236">Per altre informazioni, vedere la pagina relativa alla [condivisione delle risorse tra le origini W3C (terminologia): intestazione della risposta semplice](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="25f27-236">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="25f27-237">Le intestazioni di risposta disponibili per impostazione predefinita sono:</span><span class="sxs-lookup"><span data-stu-id="25f27-237">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="25f27-238">La specifica CORS chiama queste intestazioni di *risposta semplici*.</span><span class="sxs-lookup"><span data-stu-id="25f27-238">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="25f27-239">Per rendere disponibili altre intestazioni per l'app, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="25f27-239">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="25f27-240">Credenziali nelle richieste tra le origini</span><span class="sxs-lookup"><span data-stu-id="25f27-240">Credentials in cross-origin requests</span></span>

<span data-ttu-id="25f27-241">Le credenziali richiedono una gestione speciale in una richiesta CORS.</span><span class="sxs-lookup"><span data-stu-id="25f27-241">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="25f27-242">Per impostazione predefinita, il browser non invia le credenziali con una richiesta tra le origini.</span><span class="sxs-lookup"><span data-stu-id="25f27-242">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="25f27-243">Le credenziali includono i cookie e gli schemi di autenticazione HTTP.</span><span class="sxs-lookup"><span data-stu-id="25f27-243">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="25f27-244">Per inviare le credenziali con una richiesta tra le origini, il client deve impostare `XMLHttpRequest.withCredentials` su `true` .</span><span class="sxs-lookup"><span data-stu-id="25f27-244">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="25f27-245">Utilizzando `XMLHttpRequest` direttamente:</span><span class="sxs-lookup"><span data-stu-id="25f27-245">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="25f27-246">Uso di jQuery:</span><span class="sxs-lookup"><span data-stu-id="25f27-246">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="25f27-247">Uso dell' [API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="25f27-247">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="25f27-248">Il server deve consentire le credenziali.</span><span class="sxs-lookup"><span data-stu-id="25f27-248">The server must allow the credentials.</span></span> <span data-ttu-id="25f27-249">Per consentire le credenziali tra le origini, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="25f27-249">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="25f27-250">La risposta HTTP include un' `Access-Control-Allow-Credentials` intestazione che indica al browser che il server consente le credenziali per una richiesta tra le origini.</span><span class="sxs-lookup"><span data-stu-id="25f27-250">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="25f27-251">Se il browser invia le credenziali, ma la risposta non include un' `Access-Control-Allow-Credentials` intestazione valida, il browser non espone la risposta all'app e la richiesta tra le origini ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="25f27-251">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="25f27-252">Consentire le credenziali tra le origini costituisce un rischio per la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="25f27-252">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="25f27-253">Un sito Web in un altro dominio può inviare le credenziali dell'utente che ha eseguito l'accesso all'app per conto dell'utente senza la conoscenza dell'utente.</span><span class="sxs-lookup"><span data-stu-id="25f27-253">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="25f27-254">La specifica CORS indica inoltre che l'impostazione delle origini su `"*"` (tutte le origini) non è valida se l' `Access-Control-Allow-Credentials` intestazione è presente.</span><span class="sxs-lookup"><span data-stu-id="25f27-254">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="25f27-255">Richieste preliminari</span><span class="sxs-lookup"><span data-stu-id="25f27-255">Preflight requests</span></span>

<span data-ttu-id="25f27-256">Per alcune richieste CORS, il browser invia una richiesta di [Opzioni](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) aggiuntive prima di effettuare la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="25f27-256">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="25f27-257">Questa richiesta è detta [richiesta preliminare](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span><span class="sxs-lookup"><span data-stu-id="25f27-257">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="25f27-258">Il browser può ignorare la richiesta preliminare se si verificano **tutte** le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="25f27-258">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="25f27-259">Il metodo di richiesta è GET, HEAD o POST.</span><span class="sxs-lookup"><span data-stu-id="25f27-259">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="25f27-260">L'app non imposta intestazioni di richiesta diverse da `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` o `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="25f27-260">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="25f27-261">L' `Content-Type` intestazione, se impostata, presenta uno dei valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="25f27-261">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="25f27-262">La regola sulle intestazioni di richiesta impostate per la richiesta del client si applica alle intestazioni impostate dall'app chiamando `setRequestHeader` sull' `XMLHttpRequest` oggetto.</span><span class="sxs-lookup"><span data-stu-id="25f27-262">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="25f27-263">La specifica CORS chiama queste intestazioni di [richiesta autore](https://www.w3.org/TR/cors/#author-request-headers)intestazioni.</span><span class="sxs-lookup"><span data-stu-id="25f27-263">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="25f27-264">La regola non si applica alle intestazioni che il browser può impostare, ad esempio `User-Agent` , `Host` o `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="25f27-264">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="25f27-265">Di seguito è riportato un esempio di risposta simile alla richiesta preliminare eseguita dal pulsante **[Put test]** nella sezione [test CORS](#testc) di questo documento.</span><span class="sxs-lookup"><span data-stu-id="25f27-265">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="25f27-266">La richiesta preliminare usa il metodo delle [Opzioni http](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) .</span><span class="sxs-lookup"><span data-stu-id="25f27-266">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="25f27-267">Può includere le intestazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="25f27-267">It may include the following headers:</span></span>

* <span data-ttu-id="25f27-268">[Access-Control-request-method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): il metodo HTTP che verrà usato per la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="25f27-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="25f27-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): elenco di intestazioni di richiesta impostate dall'app sulla richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="25f27-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="25f27-270">Come indicato in precedenza, non include le intestazioni impostate dal browser, ad esempio `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="25f27-270">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="25f27-271">Access-Control-Allow-Methods</span><span class="sxs-lookup"><span data-stu-id="25f27-271">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="25f27-272">Se la richiesta preliminare viene negata, l'app restituisce una `200 OK` risposta ma non imposta le intestazioni CORS.</span><span class="sxs-lookup"><span data-stu-id="25f27-272">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="25f27-273">Pertanto, il browser non tenta di eseguire la richiesta tra origini.</span><span class="sxs-lookup"><span data-stu-id="25f27-273">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="25f27-274">Per un esempio di richiesta preliminare negata, vedere la sezione [test CORS](#testc) di questo documento.</span><span class="sxs-lookup"><span data-stu-id="25f27-274">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="25f27-275">Usando gli strumenti F12, l'app console Visualizza un errore simile a uno dei seguenti, a seconda del browser:</span><span class="sxs-lookup"><span data-stu-id="25f27-275">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="25f27-276">Firefox: richiesta tra le origini bloccata: gli stessi criteri di origine non consentono la lettura della risorsa remota in `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` .</span><span class="sxs-lookup"><span data-stu-id="25f27-276">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="25f27-277">(Motivo: richiesta CORS non riuscita).</span><span class="sxs-lookup"><span data-stu-id="25f27-277">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="25f27-278">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="25f27-278">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="25f27-279">Basata su cromo: l'accesso al recupero in ' https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 ' dall'origine ' https://cors3.azurewebsites.net ' è stato bloccato dal criterio CORS: la risposta alla richiesta preliminare non supera il controllo di accesso: non è presente alcuna intestazione ' Access-Control-Allow-Origin ' nella risorsa richiesta.</span><span class="sxs-lookup"><span data-stu-id="25f27-279">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="25f27-280">Se si ritiene soddisfacente una risposta opaca, impostare la modalità della richiesta 'no-cors' per recuperare la risorsa con CORS disabilitato.</span><span class="sxs-lookup"><span data-stu-id="25f27-280">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="25f27-281">Per consentire intestazioni specifiche, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="25f27-281">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="25f27-282">Per consentire tutte le [intestazioni di richiesta di autore](https://www.w3.org/TR/cors/#author-request-headers), chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="25f27-282">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="25f27-283">I browser non sono coerenti con la modalità di impostazione `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="25f27-283">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="25f27-284">Se uno:</span><span class="sxs-lookup"><span data-stu-id="25f27-284">If either:</span></span>

* <span data-ttu-id="25f27-285">Le intestazioni sono impostate su un valore diverso da`"*"`</span><span class="sxs-lookup"><span data-stu-id="25f27-285">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="25f27-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>viene chiamato: includere almeno `Accept` , `Content-Type` e `Origin` , più eventuali intestazioni personalizzate che si desidera supportare.</span><span class="sxs-lookup"><span data-stu-id="25f27-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="25f27-287">Codice richiesta preliminare automatica</span><span class="sxs-lookup"><span data-stu-id="25f27-287">Automatic preflight request code</span></span>

<span data-ttu-id="25f27-288">Quando viene applicato il criterio CORS:</span><span class="sxs-lookup"><span data-stu-id="25f27-288">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="25f27-289">A livello globale, chiamando il `app.UseCors` `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="25f27-289">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="25f27-290">Utilizzando l' `[EnableCors]` attributo.</span><span class="sxs-lookup"><span data-stu-id="25f27-290">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="25f27-291">ASP.NET Core risponde alla richiesta di opzioni preliminari.</span><span class="sxs-lookup"><span data-stu-id="25f27-291">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="25f27-292">L'abilitazione di CORS in base all'endpoint `RequireCors` attualmente in **uso non** supporta le richieste preliminari automatiche.</span><span class="sxs-lookup"><span data-stu-id="25f27-292">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="25f27-293">Questo comportamento viene illustrato nella sezione [test CORS](#testc) di questo documento.</span><span class="sxs-lookup"><span data-stu-id="25f27-293">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="25f27-294">Attributo [HttpOptions] per le richieste preliminari</span><span class="sxs-lookup"><span data-stu-id="25f27-294">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="25f27-295">Quando CORS è abilitato con i criteri appropriati, ASP.NET Core risponde in genere alle richieste preliminari CORS automaticamente.</span><span class="sxs-lookup"><span data-stu-id="25f27-295">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="25f27-296">In alcuni scenari questo potrebbe non essere il caso.</span><span class="sxs-lookup"><span data-stu-id="25f27-296">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="25f27-297">Ad esempio, usando [cors con il routing degli endpoint](#ecors).</span><span class="sxs-lookup"><span data-stu-id="25f27-297">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="25f27-298">Il codice seguente usa l'attributo [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) per creare endpoint per le richieste di opzioni:</span><span class="sxs-lookup"><span data-stu-id="25f27-298">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="25f27-299">Per istruzioni sul test del codice precedente [, vedere test CORS with endpoint routing e [HttpOptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="25f27-299">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="25f27-300">Imposta la data di scadenza preliminare</span><span class="sxs-lookup"><span data-stu-id="25f27-300">Set the preflight expiration time</span></span>

<span data-ttu-id="25f27-301">L' `Access-Control-Max-Age` intestazione specifica per quanto tempo la risposta alla richiesta preliminare può essere memorizzata nella cache.</span><span class="sxs-lookup"><span data-stu-id="25f27-301">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="25f27-302">Per impostare questa intestazione, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="25f27-302">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="25f27-303">Funzionamento di CORS</span><span class="sxs-lookup"><span data-stu-id="25f27-303">How CORS works</span></span>

<span data-ttu-id="25f27-304">In questa sezione viene descritto cosa accade in una richiesta [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) al livello dei messaggi HTTP.</span><span class="sxs-lookup"><span data-stu-id="25f27-304">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="25f27-305">CORS **non** è una funzionalità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="25f27-305">CORS is **not** a security feature.</span></span> <span data-ttu-id="25f27-306">CORS è uno standard W3C che consente a un server di ridurre i criteri della stessa origine.</span><span class="sxs-lookup"><span data-stu-id="25f27-306">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="25f27-307">Ad esempio, un attore malintenzionato potrebbe usare [lo scripting tra siti (XSS)](xref:security/cross-site-scripting) per il sito ed eseguire una richiesta tra siti al sito abilitato per CORS per rubare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="25f27-307">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="25f27-308">Un'API non è più sicura consentendo CORS.</span><span class="sxs-lookup"><span data-stu-id="25f27-308">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="25f27-309">Il client (browser) deve applicare CORS.</span><span class="sxs-lookup"><span data-stu-id="25f27-309">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="25f27-310">Il server esegue la richiesta e restituisce la risposta, ovvero il client che restituisce un errore e blocca la risposta.</span><span class="sxs-lookup"><span data-stu-id="25f27-310">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="25f27-311">Uno degli strumenti seguenti, ad esempio, visualizzerà la risposta del server:</span><span class="sxs-lookup"><span data-stu-id="25f27-311">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="25f27-312">Fiddler</span><span class="sxs-lookup"><span data-stu-id="25f27-312">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="25f27-313">Postman</span><span class="sxs-lookup"><span data-stu-id="25f27-313">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="25f27-314">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="25f27-314">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="25f27-315">Un Web browser immettendo l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="25f27-315">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="25f27-316">Si tratta di un modo per consentire a un server di consentire ai browser di eseguire una richiesta dell' [API di recupero](https://developer.mozilla.org/docs/Web/API/Fetch_API) o di [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) tra le origini, che altrimenti sarebbe proibita.</span><span class="sxs-lookup"><span data-stu-id="25f27-316">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="25f27-317">I browser senza CORS non possono eseguire richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="25f27-317">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="25f27-318">Prima di CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) è stato usato per aggirare questa restrizione.</span><span class="sxs-lookup"><span data-stu-id="25f27-318">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="25f27-319">JSONP non usa XHR, usa il `<script>` tag per ricevere la risposta.</span><span class="sxs-lookup"><span data-stu-id="25f27-319">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="25f27-320">Gli script possono essere caricati tra le origini.</span><span class="sxs-lookup"><span data-stu-id="25f27-320">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="25f27-321">La [specifica CORS](https://www.w3.org/TR/cors/) ha introdotto diverse nuove intestazioni HTTP che consentono richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="25f27-321">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="25f27-322">Se un browser supporta CORS, queste intestazioni vengono impostate automaticamente per le richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="25f27-322">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="25f27-323">Il codice JavaScript personalizzato non è necessario per abilitare CORS.</span><span class="sxs-lookup"><span data-stu-id="25f27-323">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="25f27-324">[Pulsante Put test](https://cors3.azurewebsites.net/test) nell' [esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) distribuito</span><span class="sxs-lookup"><span data-stu-id="25f27-324">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="25f27-325">Di seguito è riportato un esempio di richiesta tra più origini dal pulsante test [valori](https://cors3.azurewebsites.net/) a `https://cors1.azurewebsites.net/api/values` .</span><span class="sxs-lookup"><span data-stu-id="25f27-325">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="25f27-326">`Origin`Intestazione:</span><span class="sxs-lookup"><span data-stu-id="25f27-326">The `Origin` header:</span></span>

* <span data-ttu-id="25f27-327">Fornisce il dominio del sito che sta effettuando la richiesta.</span><span class="sxs-lookup"><span data-stu-id="25f27-327">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="25f27-328">È obbligatorio e deve essere diverso dall'host.</span><span class="sxs-lookup"><span data-stu-id="25f27-328">Is required and must be different from the host.</span></span>

<span data-ttu-id="25f27-329">**Intestazioni generali**</span><span class="sxs-lookup"><span data-stu-id="25f27-329">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="25f27-330">**Intestazioni della risposta**</span><span class="sxs-lookup"><span data-stu-id="25f27-330">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="25f27-331">**Intestazioni della richiesta**</span><span class="sxs-lookup"><span data-stu-id="25f27-331">**Request headers**</span></span>

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

<span data-ttu-id="25f27-332">Nelle `OPTIONS` richieste, il server imposta l'intestazione delle **intestazioni di risposta** `Access-Control-Allow-Origin: {allowed origin}` nella risposta.</span><span class="sxs-lookup"><span data-stu-id="25f27-332">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="25f27-333">Ad esempio, la richiesta del pulsante [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) di [esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)distribuito `OPTIONS` contiene le intestazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="25f27-333">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="25f27-334">**Intestazioni generali**</span><span class="sxs-lookup"><span data-stu-id="25f27-334">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="25f27-335">**Intestazioni della risposta**</span><span class="sxs-lookup"><span data-stu-id="25f27-335">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="25f27-336">**Intestazioni della richiesta**</span><span class="sxs-lookup"><span data-stu-id="25f27-336">**Request headers**</span></span>

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

<span data-ttu-id="25f27-337">Nelle intestazioni di **risposta**precedenti, il server imposta l'intestazione [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) nella risposta.</span><span class="sxs-lookup"><span data-stu-id="25f27-337">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="25f27-338">Il `https://cors1.azurewebsites.net` valore di questa intestazione corrisponde all' `Origin` intestazione della richiesta.</span><span class="sxs-lookup"><span data-stu-id="25f27-338">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="25f27-339">Se <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> viene chiamato, `Access-Control-Allow-Origin: *` viene restituito il valore del carattere jolly.</span><span class="sxs-lookup"><span data-stu-id="25f27-339">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="25f27-340">`AllowAnyOrigin`consente qualsiasi origine.</span><span class="sxs-lookup"><span data-stu-id="25f27-340">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="25f27-341">Se la risposta non include l' `Access-Control-Allow-Origin` intestazione, la richiesta tra le origini ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="25f27-341">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="25f27-342">In particolare, il browser non consente la richiesta.</span><span class="sxs-lookup"><span data-stu-id="25f27-342">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="25f27-343">Anche se il server restituisce una risposta con esito positivo, il browser non rende disponibile la risposta all'app client.</span><span class="sxs-lookup"><span data-stu-id="25f27-343">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="25f27-344">Richieste di opzioni di visualizzazione</span><span class="sxs-lookup"><span data-stu-id="25f27-344">Display OPTIONS requests</span></span>

<span data-ttu-id="25f27-345">Per impostazione predefinita, i browser Chrome e Edge non visualizzano le richieste OPTIONS nella scheda rete degli strumenti F12.</span><span class="sxs-lookup"><span data-stu-id="25f27-345">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="25f27-346">Per visualizzare le richieste di opzioni in questi browser:</span><span class="sxs-lookup"><span data-stu-id="25f27-346">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="25f27-347">`chrome://flags/#out-of-blink-cors` o `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="25f27-347">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="25f27-348">disabilitare il flag.</span><span class="sxs-lookup"><span data-stu-id="25f27-348">disable the flag.</span></span>
* <span data-ttu-id="25f27-349">Riavvia.</span><span class="sxs-lookup"><span data-stu-id="25f27-349">restart.</span></span>

<span data-ttu-id="25f27-350">Per impostazione predefinita, Firefox Visualizza le richieste OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="25f27-350">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="25f27-351">CORS in IIS</span><span class="sxs-lookup"><span data-stu-id="25f27-351">CORS in IIS</span></span>

<span data-ttu-id="25f27-352">Quando si esegue la distribuzione in IIS, CORS deve essere eseguito prima dell'autenticazione di Windows se il server non è configurato per consentire l'accesso anonimo.</span><span class="sxs-lookup"><span data-stu-id="25f27-352">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="25f27-353">Per supportare questo scenario, è necessario installare e configurare il [modulo cors di IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) per l'app.</span><span class="sxs-lookup"><span data-stu-id="25f27-353">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="25f27-354">Testare CORS</span><span class="sxs-lookup"><span data-stu-id="25f27-354">Test CORS</span></span>

<span data-ttu-id="25f27-355">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) contiene codice per testare CORS.</span><span class="sxs-lookup"><span data-stu-id="25f27-355">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="25f27-356">Vedere [come scaricare un esempio](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="25f27-356">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="25f27-357">L'esempio è un progetto API con Razor pagine aggiunte:</span><span class="sxs-lookup"><span data-stu-id="25f27-357">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="25f27-358">`WithOrigins("https://localhost:<port>");`deve essere usato solo per il test di un'app di esempio simile al [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)per il download.</span><span class="sxs-lookup"><span data-stu-id="25f27-358">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="25f27-359">Di seguito sono riportati `ValuesController` gli endpoint per i test:</span><span class="sxs-lookup"><span data-stu-id="25f27-359">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="25f27-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) viene fornito dal pacchetto NuGet [Rick. docs. Samples. RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) e visualizza le informazioni sulla route.</span><span class="sxs-lookup"><span data-stu-id="25f27-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="25f27-361">Testare il codice di esempio precedente usando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="25f27-361">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="25f27-362">Usare l'app di esempio distribuita all'indirizzo [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="25f27-362">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="25f27-363">Non è necessario scaricare l'esempio.</span><span class="sxs-lookup"><span data-stu-id="25f27-363">There is no need to download the sample.</span></span>
* <span data-ttu-id="25f27-364">Eseguire l'esempio con `dotnet run` utilizzando l'URL predefinito `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="25f27-364">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="25f27-365">Eseguire l'esempio da Visual Studio con la porta impostata su 44398 per un URL di `https://localhost:44398` .</span><span class="sxs-lookup"><span data-stu-id="25f27-365">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="25f27-366">Uso di un browser con gli strumenti F12:</span><span class="sxs-lookup"><span data-stu-id="25f27-366">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="25f27-367">Selezionare il pulsante **valori** ed esaminare le intestazioni nella scheda **rete** .</span><span class="sxs-lookup"><span data-stu-id="25f27-367">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="25f27-368">Selezionare il pulsante **put test (Put test** ).</span><span class="sxs-lookup"><span data-stu-id="25f27-368">Select the **PUT test** button.</span></span> <span data-ttu-id="25f27-369">Per istruzioni sulla visualizzazione della richiesta OPTIONS, vedere [visualizzare le richieste di opzioni](#options) .</span><span class="sxs-lookup"><span data-stu-id="25f27-369">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="25f27-370">Il **test put** crea due richieste, una richiesta preliminare Options e la richiesta PUT.</span><span class="sxs-lookup"><span data-stu-id="25f27-370">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="25f27-371">Selezionare il **`GetValues2 [DisableCors]`** pulsante per attivare una richiesta CORS non riuscita.</span><span class="sxs-lookup"><span data-stu-id="25f27-371">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="25f27-372">Come indicato nel documento, la risposta restituisce 200 esito positivo, ma la richiesta CORS non viene eseguita.</span><span class="sxs-lookup"><span data-stu-id="25f27-372">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="25f27-373">Selezionare la scheda **console** per visualizzare l'errore CORS.</span><span class="sxs-lookup"><span data-stu-id="25f27-373">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="25f27-374">A seconda del browser, viene visualizzato un errore simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="25f27-374">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="25f27-375">L'accesso al recupero `'https://cors1.azurewebsites.net/api/values/GetValues2'` dall'origine `'https://cors3.azurewebsites.net'` è stato bloccato dal criterio CORS: non è presente alcuna intestazione ' Access-Control-Allow-Origin ' nella risorsa richiesta.</span><span class="sxs-lookup"><span data-stu-id="25f27-375">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="25f27-376">Se si ritiene soddisfacente una risposta opaca, impostare la modalità della richiesta 'no-cors' per recuperare la risorsa con CORS disabilitato.</span><span class="sxs-lookup"><span data-stu-id="25f27-376">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="25f27-377">Gli endpoint abilitati per CORS possono essere testati con uno strumento, ad esempio [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)o [postazione](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="25f27-377">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="25f27-378">Quando si utilizza uno strumento, l'origine della richiesta specificata dall' `Origin` intestazione deve essere diversa dall'host che riceve la richiesta.</span><span class="sxs-lookup"><span data-stu-id="25f27-378">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="25f27-379">Se la richiesta non è *tra origini* basate sul valore dell' `Origin` intestazione:</span><span class="sxs-lookup"><span data-stu-id="25f27-379">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="25f27-380">Non è necessario che il middleware CORS elabori la richiesta.</span><span class="sxs-lookup"><span data-stu-id="25f27-380">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="25f27-381">Le intestazioni CORS non vengono restituite nella risposta.</span><span class="sxs-lookup"><span data-stu-id="25f27-381">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="25f27-382">Il comando seguente usa `curl` per emettere una richiesta OPTIONS con informazioni:</span><span class="sxs-lookup"><span data-stu-id="25f27-382">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="25f27-383">Test di CORS con routing endpoint e [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="25f27-383">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="25f27-384">L'abilitazione di CORS in base all'endpoint `RequireCors` attualmente in **uso non** supporta [le richieste preliminari automatiche](#apf).</span><span class="sxs-lookup"><span data-stu-id="25f27-384">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="25f27-385">Si consideri il codice seguente che usa il [routing dell'endpoint per abilitare CORS](#ecors):</span><span class="sxs-lookup"><span data-stu-id="25f27-385">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="25f27-386">Di seguito sono riportati gli `TodoItems1Controller` endpoint per i test:</span><span class="sxs-lookup"><span data-stu-id="25f27-386">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="25f27-387">Testare il codice precedente dalla [pagina di test](https://cors1.azurewebsites.net/test?number=1) dell' [esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)distribuito.</span><span class="sxs-lookup"><span data-stu-id="25f27-387">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="25f27-388">I pulsanti **Delete [EnableCors]** e **Get [EnableCors]** sono riusciti perché gli endpoint hanno `[EnableCors]` e rispondono alle richieste preliminari.</span><span class="sxs-lookup"><span data-stu-id="25f27-388">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="25f27-389">Gli altri endpoint hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="25f27-389">The other endpoints fails.</span></span> <span data-ttu-id="25f27-390">Il pulsante **Get** ha esito negativo perché il [codice JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) Invia:</span><span class="sxs-lookup"><span data-stu-id="25f27-390">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="25f27-391">Di seguito `TodoItems2Controller` vengono forniti endpoint simili, ma è incluso codice esplicito per rispondere alle richieste di opzioni:</span><span class="sxs-lookup"><span data-stu-id="25f27-391">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="25f27-392">Testare il codice precedente dalla [pagina di test](https://cors1.azurewebsites.net/test?number=2) dell'esempio distribuito.</span><span class="sxs-lookup"><span data-stu-id="25f27-392">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="25f27-393">Nell'elenco a discesa **controller** selezionare **preliminare** e quindi **impostare controller**.</span><span class="sxs-lookup"><span data-stu-id="25f27-393">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="25f27-394">Tutte le chiamate CORS agli `TodoItems2Controller` endpoint hanno esito positivo.</span><span class="sxs-lookup"><span data-stu-id="25f27-394">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="25f27-395">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="25f27-395">Additional resources</span></span>

* [<span data-ttu-id="25f27-396">Condivisione risorse tra le origini (CORS)</span><span class="sxs-lookup"><span data-stu-id="25f27-396">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="25f27-397">Introduzione al modulo CORS di IIS</span><span class="sxs-lookup"><span data-stu-id="25f27-397">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="25f27-398">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="25f27-398">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="25f27-399">Questo articolo illustra come abilitare CORS in un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="25f27-399">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="25f27-400">La sicurezza del browser impedisce a una pagina Web di eseguire richieste a un dominio diverso da quello che ha gestito la pagina Web.</span><span class="sxs-lookup"><span data-stu-id="25f27-400">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="25f27-401">Questa restrizione è detta *criterio della stessa origine*.</span><span class="sxs-lookup"><span data-stu-id="25f27-401">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="25f27-402">Il criterio della stessa origine impedisce a un sito dannoso di leggere dati sensibili da un altro sito.</span><span class="sxs-lookup"><span data-stu-id="25f27-402">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="25f27-403">In alcuni casi potrebbe essere necessario consentire ad altri siti di effettuare richieste tra le origini all'app.</span><span class="sxs-lookup"><span data-stu-id="25f27-403">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="25f27-404">Per altre informazioni, vedere l' [articolo relativo a Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="25f27-404">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="25f27-405">[Condivisione risorse tra le origini](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="25f27-405">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="25f27-406">È uno standard W3C che consente a un server di ridurre i criteri della stessa origine.</span><span class="sxs-lookup"><span data-stu-id="25f27-406">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="25f27-407">**Non** è una funzionalità di sicurezza, CORS rilassa la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="25f27-407">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="25f27-408">Un'API non è più sicura consentendo CORS.</span><span class="sxs-lookup"><span data-stu-id="25f27-408">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="25f27-409">Per ulteriori informazioni, vedere [funzionamento di CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="25f27-409">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="25f27-410">Consente a un server di consentire in modo esplicito alcune richieste tra origini rifiutando altre.</span><span class="sxs-lookup"><span data-stu-id="25f27-410">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="25f27-411">È più sicuro e flessibile rispetto alle tecniche precedenti, ad esempio [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="25f27-411">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="25f27-412">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="25f27-412">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="25f27-413">Stessa origine</span><span class="sxs-lookup"><span data-stu-id="25f27-413">Same origin</span></span>

<span data-ttu-id="25f27-414">Due URL hanno la stessa origine se hanno schemi, host e porte identici ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="25f27-414">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="25f27-415">Questi due URL hanno la stessa origine:</span><span class="sxs-lookup"><span data-stu-id="25f27-415">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="25f27-416">Questi URL hanno origini diverse da quelle dei due URL precedenti:</span><span class="sxs-lookup"><span data-stu-id="25f27-416">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="25f27-417">`https://example.net`: Dominio diverso</span><span class="sxs-lookup"><span data-stu-id="25f27-417">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="25f27-418">`https://www.example.com/foo.html`: Sottodominio diverso</span><span class="sxs-lookup"><span data-stu-id="25f27-418">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="25f27-419">`http://example.com/foo.html`: Schema diverso</span><span class="sxs-lookup"><span data-stu-id="25f27-419">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="25f27-420">`https://example.com:9000/foo.html`: Porta diversa</span><span class="sxs-lookup"><span data-stu-id="25f27-420">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="25f27-421">Internet Explorer non considera la porta durante il confronto delle origini.</span><span class="sxs-lookup"><span data-stu-id="25f27-421">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="25f27-422">CORS con criteri e middleware denominati</span><span class="sxs-lookup"><span data-stu-id="25f27-422">CORS with named policy and middleware</span></span>

<span data-ttu-id="25f27-423">Il middleware CORS gestisce le richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="25f27-423">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="25f27-424">Il codice seguente Abilita CORS per l'intera app con l'origine specificata:</span><span class="sxs-lookup"><span data-stu-id="25f27-424">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="25f27-425">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="25f27-425">The preceding code:</span></span>

* <span data-ttu-id="25f27-426">Imposta il nome del criterio su " \_ myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="25f27-426">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="25f27-427">Il nome del criterio è arbitrario.</span><span class="sxs-lookup"><span data-stu-id="25f27-427">The policy name is arbitrary.</span></span>
* <span data-ttu-id="25f27-428">Chiama il <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodo di estensione, che Abilita CORS.</span><span class="sxs-lookup"><span data-stu-id="25f27-428">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="25f27-429">Chiama <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> con un' [espressione lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="25f27-429">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="25f27-430">L'espressione lambda accetta un <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> oggetto.</span><span class="sxs-lookup"><span data-stu-id="25f27-430">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="25f27-431">Le [Opzioni di configurazione](#cors-policy-options), ad esempio `WithOrigins` , sono descritte più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="25f27-431">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="25f27-432">La <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chiamata al metodo aggiunge i servizi CORS al contenitore del servizio dell'app:</span><span class="sxs-lookup"><span data-stu-id="25f27-432">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="25f27-433">Per ulteriori informazioni, vedere [Opzioni dei criteri CORS](#cpo) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="25f27-433">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="25f27-434">Il <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> metodo può concatenare i metodi, come illustrato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="25f27-434">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="25f27-435">Nota: l'URL **non** deve contenere una barra finale ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="25f27-435">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="25f27-436">Se l'URL termina con `/` , il confronto restituisce `false` e non viene restituita alcuna intestazione.</span><span class="sxs-lookup"><span data-stu-id="25f27-436">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="25f27-437">Il codice seguente applica i criteri di CORS a tutti gli endpoint di app tramite il middleware CORS:</span><span class="sxs-lookup"><span data-stu-id="25f27-437">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="25f27-438">Nota: `UseCors` è necessario chiamare prima `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="25f27-438">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="25f27-439">Vedere [abilitare CORS in Razor pagine, controller e metodi di azione](#ecors) per applicare i criteri di CORS a livello di pagina/controller/azione.</span><span class="sxs-lookup"><span data-stu-id="25f27-439">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="25f27-440">Vedere [test CORS](#test) per istruzioni sul test di codice simile al codice precedente.</span><span class="sxs-lookup"><span data-stu-id="25f27-440">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="25f27-441">Abilita CORS con attributi</span><span class="sxs-lookup"><span data-stu-id="25f27-441">Enable CORS with attributes</span></span>

<span data-ttu-id="25f27-442">L'attributo [ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornisce un'alternativa all'applicazione di CORS a livello globale.</span><span class="sxs-lookup"><span data-stu-id="25f27-442">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="25f27-443">L' `[EnableCors]` attributo Abilita CORS per i punti finali selezionati, anziché tutti i punti finali.</span><span class="sxs-lookup"><span data-stu-id="25f27-443">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="25f27-444">Usare `[EnableCors]` per specificare i criteri predefiniti e `[EnableCors("{Policy String}")]` per specificare un criterio.</span><span class="sxs-lookup"><span data-stu-id="25f27-444">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="25f27-445">L' `[EnableCors]` attributo può essere applicato a:</span><span class="sxs-lookup"><span data-stu-id="25f27-445">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="25f27-446">Pagina`PageModel`</span><span class="sxs-lookup"><span data-stu-id="25f27-446"> Page `PageModel`</span></span>
* <span data-ttu-id="25f27-447">Controller</span><span class="sxs-lookup"><span data-stu-id="25f27-447">Controller</span></span>
* <span data-ttu-id="25f27-448">Metodo di azione del controller</span><span class="sxs-lookup"><span data-stu-id="25f27-448">Controller action method</span></span>

<span data-ttu-id="25f27-449">È possibile applicare criteri diversi a controller/pagina-modello/azione con l' `[EnableCors]` attributo.</span><span class="sxs-lookup"><span data-stu-id="25f27-449">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="25f27-450">Quando l' `[EnableCors]` attributo viene applicato a un metodo di azione/modello di pagina, mentre CORS è abilitato nel middleware, vengono applicati **entrambi** i criteri.</span><span class="sxs-lookup"><span data-stu-id="25f27-450">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="25f27-451">Si consiglia di **non** combinare i criteri.</span><span class="sxs-lookup"><span data-stu-id="25f27-451">We recommend **not** combining policies.</span></span> <span data-ttu-id="25f27-452">Usare l' `[EnableCors]` attributo o il middleware, **non entrambi**.</span><span class="sxs-lookup"><span data-stu-id="25f27-452">Use the `[EnableCors]` attribute or middleware, **not both**.</span></span> <span data-ttu-id="25f27-453">Quando `[EnableCors]` si usa, **non** definire criteri predefiniti.</span><span class="sxs-lookup"><span data-stu-id="25f27-453">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="25f27-454">Il codice seguente applica un criterio diverso a ogni metodo:</span><span class="sxs-lookup"><span data-stu-id="25f27-454">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="25f27-455">Il codice seguente crea un criterio predefinito CORS e un criterio denominato `"AnotherPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="25f27-455">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="25f27-456">Disabilitare CORS</span><span class="sxs-lookup"><span data-stu-id="25f27-456">Disable CORS</span></span>

<span data-ttu-id="25f27-457">L'attributo [ &lbrack; DISABLECORS &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) Disabilita CORS per il controller, il modello di pagina o l'azione.</span><span class="sxs-lookup"><span data-stu-id="25f27-457">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="25f27-458">Opzioni dei criteri di CORS</span><span class="sxs-lookup"><span data-stu-id="25f27-458">CORS policy options</span></span>

<span data-ttu-id="25f27-459">In questa sezione vengono descritte le varie opzioni che è possibile impostare in un criterio CORS:</span><span class="sxs-lookup"><span data-stu-id="25f27-459">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="25f27-460">Imposta le origini consentite</span><span class="sxs-lookup"><span data-stu-id="25f27-460">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="25f27-461">Impostare i metodi HTTP consentiti</span><span class="sxs-lookup"><span data-stu-id="25f27-461">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="25f27-462">Impostare le intestazioni della richiesta consentita</span><span class="sxs-lookup"><span data-stu-id="25f27-462">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="25f27-463">Impostare le intestazioni di risposta esposte</span><span class="sxs-lookup"><span data-stu-id="25f27-463">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="25f27-464">Credenziali nelle richieste tra le origini</span><span class="sxs-lookup"><span data-stu-id="25f27-464">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="25f27-465">Imposta la data di scadenza preliminare</span><span class="sxs-lookup"><span data-stu-id="25f27-465">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="25f27-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>viene chiamato in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="25f27-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="25f27-467">Per alcune opzioni, può essere utile leggere prima la sezione [come funziona CORS](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="25f27-467">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="25f27-468">Imposta le origini consentite</span><span class="sxs-lookup"><span data-stu-id="25f27-468">Set the allowed origins</span></span>

<span data-ttu-id="25f27-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Consente le richieste CORS da tutte le origini con qualsiasi schema ( `http` o `https` ).</span><span class="sxs-lookup"><span data-stu-id="25f27-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="25f27-470">`AllowAnyOrigin`non è sicuro perché *qualsiasi sito Web* può effettuare richieste tra origini per l'app.</span><span class="sxs-lookup"><span data-stu-id="25f27-470">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="25f27-471">La definizione di `AllowAnyOrigin` e `AllowCredentials` è una configurazione non sicura e può comportare la falsificazione della richiesta tra siti.</span><span class="sxs-lookup"><span data-stu-id="25f27-471">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="25f27-472">Per un'app sicura, specificare un elenco esatto di origini se il client deve autorizzare se stesso ad accedere alle risorse del server.</span><span class="sxs-lookup"><span data-stu-id="25f27-472">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="25f27-473">`AllowAnyOrigin`influiscono sulle richieste preliminari e sull' `Access-Control-Allow-Origin` intestazione.</span><span class="sxs-lookup"><span data-stu-id="25f27-473">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="25f27-474">Per ulteriori informazioni, vedere la sezione [preflight requests](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="25f27-474">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="25f27-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Imposta la <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> proprietà dei criteri in modo che sia una funzione che consente alle origini di corrispondere a un dominio con caratteri jolly configurato durante la valutazione se l'origine è consentita.</span><span class="sxs-lookup"><span data-stu-id="25f27-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="25f27-476">Impostare i metodi HTTP consentiti</span><span class="sxs-lookup"><span data-stu-id="25f27-476">Set the allowed HTTP methods</span></span>

<span data-ttu-id="25f27-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="25f27-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="25f27-478">Consente qualsiasi metodo HTTP:</span><span class="sxs-lookup"><span data-stu-id="25f27-478">Allows any HTTP method:</span></span>
* <span data-ttu-id="25f27-479">Influiscono sulle richieste preliminari e sull' `Access-Control-Allow-Methods` intestazione.</span><span class="sxs-lookup"><span data-stu-id="25f27-479">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="25f27-480">Per ulteriori informazioni, vedere la sezione [preflight requests](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="25f27-480">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="25f27-481">Impostare le intestazioni della richiesta consentita</span><span class="sxs-lookup"><span data-stu-id="25f27-481">Set the allowed request headers</span></span>

<span data-ttu-id="25f27-482">Per consentire l'invio di intestazioni specifiche in una richiesta CORS, denominate *intestazioni richiesta autore*, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> e specificare le intestazioni consentite:</span><span class="sxs-lookup"><span data-stu-id="25f27-482">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="25f27-483">Per consentire tutte le intestazioni di richiesta di autore, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="25f27-483">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="25f27-484">Questa impostazione influiscono sulle richieste preliminari e sull' `Access-Control-Request-Headers` intestazione.</span><span class="sxs-lookup"><span data-stu-id="25f27-484">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="25f27-485">Per ulteriori informazioni, vedere la sezione [preflight requests](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="25f27-485">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="25f27-486">Il middleware CORS consente sempre l'invio di quattro intestazioni in, `Access-Control-Request-Headers` indipendentemente dai valori configurati in CorsPolicy. Headers.</span><span class="sxs-lookup"><span data-stu-id="25f27-486">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="25f27-487">Questo elenco di intestazioni include:</span><span class="sxs-lookup"><span data-stu-id="25f27-487">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="25f27-488">Si consideri, ad esempio, un'app configurata come segue:</span><span class="sxs-lookup"><span data-stu-id="25f27-488">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="25f27-489">Il middleware CORS risponde correttamente a una richiesta preliminare con la seguente intestazione di richiesta perché è sempre consentita `Content-Language` :</span><span class="sxs-lookup"><span data-stu-id="25f27-489">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="25f27-490">Impostare le intestazioni di risposta esposte</span><span class="sxs-lookup"><span data-stu-id="25f27-490">Set the exposed response headers</span></span>

<span data-ttu-id="25f27-491">Per impostazione predefinita, il browser non espone tutte le intestazioni di risposta all'app.</span><span class="sxs-lookup"><span data-stu-id="25f27-491">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="25f27-492">Per altre informazioni, vedere la pagina relativa alla [condivisione delle risorse tra le origini W3C (terminologia): intestazione della risposta semplice](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="25f27-492">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="25f27-493">Le intestazioni di risposta disponibili per impostazione predefinita sono:</span><span class="sxs-lookup"><span data-stu-id="25f27-493">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="25f27-494">La specifica CORS chiama queste intestazioni di *risposta semplici*.</span><span class="sxs-lookup"><span data-stu-id="25f27-494">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="25f27-495">Per rendere disponibili altre intestazioni per l'app, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="25f27-495">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="25f27-496">Credenziali nelle richieste tra le origini</span><span class="sxs-lookup"><span data-stu-id="25f27-496">Credentials in cross-origin requests</span></span>

<span data-ttu-id="25f27-497">Le credenziali richiedono una gestione speciale in una richiesta CORS.</span><span class="sxs-lookup"><span data-stu-id="25f27-497">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="25f27-498">Per impostazione predefinita, il browser non invia le credenziali con una richiesta tra le origini.</span><span class="sxs-lookup"><span data-stu-id="25f27-498">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="25f27-499">Le credenziali includono i cookie e gli schemi di autenticazione HTTP.</span><span class="sxs-lookup"><span data-stu-id="25f27-499">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="25f27-500">Per inviare le credenziali con una richiesta tra le origini, il client deve impostare `XMLHttpRequest.withCredentials` su `true` .</span><span class="sxs-lookup"><span data-stu-id="25f27-500">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="25f27-501">Utilizzando `XMLHttpRequest` direttamente:</span><span class="sxs-lookup"><span data-stu-id="25f27-501">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="25f27-502">Uso di jQuery:</span><span class="sxs-lookup"><span data-stu-id="25f27-502">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="25f27-503">Uso dell' [API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="25f27-503">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="25f27-504">Il server deve consentire le credenziali.</span><span class="sxs-lookup"><span data-stu-id="25f27-504">The server must allow the credentials.</span></span> <span data-ttu-id="25f27-505">Per consentire le credenziali tra le origini, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="25f27-505">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="25f27-506">La risposta HTTP include un' `Access-Control-Allow-Credentials` intestazione che indica al browser che il server consente le credenziali per una richiesta tra le origini.</span><span class="sxs-lookup"><span data-stu-id="25f27-506">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="25f27-507">Se il browser invia le credenziali, ma la risposta non include un' `Access-Control-Allow-Credentials` intestazione valida, il browser non espone la risposta all'app e la richiesta tra le origini ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="25f27-507">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="25f27-508">Consentire le credenziali tra le origini costituisce un rischio per la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="25f27-508">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="25f27-509">Un sito Web in un altro dominio può inviare le credenziali dell'utente che ha eseguito l'accesso all'app per conto dell'utente senza la conoscenza dell'utente.</span><span class="sxs-lookup"><span data-stu-id="25f27-509">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="25f27-510">La specifica CORS indica inoltre che l'impostazione delle origini su `"*"` (tutte le origini) non è valida se l' `Access-Control-Allow-Credentials` intestazione è presente.</span><span class="sxs-lookup"><span data-stu-id="25f27-510">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="25f27-511">Richieste preliminari</span><span class="sxs-lookup"><span data-stu-id="25f27-511">Preflight requests</span></span>

<span data-ttu-id="25f27-512">Per alcune richieste CORS, il browser invia una richiesta aggiuntiva prima di effettuare la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="25f27-512">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="25f27-513">Questa richiesta è detta *richiesta preliminare*.</span><span class="sxs-lookup"><span data-stu-id="25f27-513">This request is called a *preflight request*.</span></span> <span data-ttu-id="25f27-514">Il browser può ignorare la richiesta preliminare se vengono soddisfatte le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="25f27-514">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="25f27-515">Il metodo di richiesta è GET, HEAD o POST.</span><span class="sxs-lookup"><span data-stu-id="25f27-515">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="25f27-516">L'app non imposta intestazioni di richiesta diverse da `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` o `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="25f27-516">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="25f27-517">L' `Content-Type` intestazione, se impostata, presenta uno dei valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="25f27-517">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="25f27-518">La regola sulle intestazioni di richiesta impostate per la richiesta del client si applica alle intestazioni impostate dall'app chiamando `setRequestHeader` sull' `XMLHttpRequest` oggetto.</span><span class="sxs-lookup"><span data-stu-id="25f27-518">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="25f27-519">La specifica CORS chiama queste intestazioni di *richiesta autore*intestazioni.</span><span class="sxs-lookup"><span data-stu-id="25f27-519">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="25f27-520">La regola non si applica alle intestazioni che il browser può impostare, ad esempio `User-Agent` , `Host` o `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="25f27-520">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="25f27-521">Di seguito è riportato un esempio di una richiesta preliminare:</span><span class="sxs-lookup"><span data-stu-id="25f27-521">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="25f27-522">La richiesta pre-flight usa il metodo delle opzioni HTTP.</span><span class="sxs-lookup"><span data-stu-id="25f27-522">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="25f27-523">Sono incluse due intestazioni speciali:</span><span class="sxs-lookup"><span data-stu-id="25f27-523">It includes two special headers:</span></span>

* <span data-ttu-id="25f27-524">`Access-Control-Request-Method`: Metodo HTTP che verrà usato per la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="25f27-524">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="25f27-525">`Access-Control-Request-Headers`: Elenco di intestazioni di richiesta impostate dall'app sulla richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="25f27-525">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="25f27-526">Come indicato in precedenza, non include le intestazioni impostate dal browser, ad esempio `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="25f27-526">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="25f27-527">Quando CORS è abilitato con i criteri appropriati, ASP.NET Core in genere risponde automaticamente alle richieste preliminari di CORS.</span><span class="sxs-lookup"><span data-stu-id="25f27-527">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="25f27-528">Vedere l' [attributo [HttpOptions] per le richieste preliminari](#pro).</span><span class="sxs-lookup"><span data-stu-id="25f27-528">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="25f27-529">Una richiesta preliminare CORS può includere un' `Access-Control-Request-Headers` intestazione, che indica al server le intestazioni inviate con la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="25f27-529">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="25f27-530">Per consentire intestazioni specifiche, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="25f27-530">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="25f27-531">Per consentire tutte le intestazioni di richiesta di autore, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="25f27-531">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="25f27-532">I browser non sono completamente coerenti nel modo in cui vengono impostati `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="25f27-532">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="25f27-533">Se si impostano le intestazioni su un valore diverso da `"*"` (o si utilizza <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> ), è necessario includere almeno `Accept` , `Content-Type` e `Origin` , più eventuali intestazioni personalizzate che si desidera supportare.</span><span class="sxs-lookup"><span data-stu-id="25f27-533">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="25f27-534">Di seguito è riportato un esempio di risposta alla richiesta preliminare (presupponendo che il server consenta la richiesta):</span><span class="sxs-lookup"><span data-stu-id="25f27-534">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="25f27-535">La risposta include un' `Access-Control-Allow-Methods` intestazione che elenca i metodi consentiti e, facoltativamente `Access-Control-Allow-Headers` , un'intestazione, che elenca le intestazioni consentite.</span><span class="sxs-lookup"><span data-stu-id="25f27-535">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="25f27-536">Se la richiesta preliminare ha esito positivo, il browser invia la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="25f27-536">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="25f27-537">Se la richiesta preliminare viene negata, l'app restituisce una risposta *200 OK* , ma non invia le intestazioni CORS.</span><span class="sxs-lookup"><span data-stu-id="25f27-537">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="25f27-538">Pertanto, il browser non tenta di eseguire la richiesta tra origini.</span><span class="sxs-lookup"><span data-stu-id="25f27-538">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="25f27-539">Imposta la data di scadenza preliminare</span><span class="sxs-lookup"><span data-stu-id="25f27-539">Set the preflight expiration time</span></span>

<span data-ttu-id="25f27-540">L' `Access-Control-Max-Age` intestazione specifica per quanto tempo la risposta alla richiesta preliminare può essere memorizzata nella cache.</span><span class="sxs-lookup"><span data-stu-id="25f27-540">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="25f27-541">Per impostare questa intestazione, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="25f27-541">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="25f27-542">Funzionamento di CORS</span><span class="sxs-lookup"><span data-stu-id="25f27-542">How CORS works</span></span>

<span data-ttu-id="25f27-543">In questa sezione viene descritto cosa accade in una richiesta [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) al livello dei messaggi HTTP.</span><span class="sxs-lookup"><span data-stu-id="25f27-543">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="25f27-544">CORS **non** è una funzionalità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="25f27-544">CORS is **not** a security feature.</span></span> <span data-ttu-id="25f27-545">CORS è uno standard W3C che consente a un server di ridurre i criteri della stessa origine.</span><span class="sxs-lookup"><span data-stu-id="25f27-545">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="25f27-546">Ad esempio, un attore malintenzionato potrebbe usare [Impedisci lo scripting tra siti (XSS)](xref:security/cross-site-scripting) per il sito ed eseguire una richiesta tra siti al sito abilitato per CORS per rubare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="25f27-546">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="25f27-547">L'API non è più sicura consentendo CORS.</span><span class="sxs-lookup"><span data-stu-id="25f27-547">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="25f27-548">Il client (browser) deve applicare CORS.</span><span class="sxs-lookup"><span data-stu-id="25f27-548">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="25f27-549">Il server esegue la richiesta e restituisce la risposta, ovvero il client che restituisce un errore e blocca la risposta.</span><span class="sxs-lookup"><span data-stu-id="25f27-549">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="25f27-550">Uno degli strumenti seguenti, ad esempio, visualizzerà la risposta del server:</span><span class="sxs-lookup"><span data-stu-id="25f27-550">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="25f27-551">Fiddler</span><span class="sxs-lookup"><span data-stu-id="25f27-551">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="25f27-552">Postman</span><span class="sxs-lookup"><span data-stu-id="25f27-552">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="25f27-553">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="25f27-553">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="25f27-554">Un Web browser immettendo l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="25f27-554">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="25f27-555">Si tratta di un modo per consentire a un server di consentire ai browser di eseguire una richiesta dell' [API di recupero](https://developer.mozilla.org/docs/Web/API/Fetch_API) o di [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) tra le origini, che altrimenti sarebbe proibita.</span><span class="sxs-lookup"><span data-stu-id="25f27-555">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="25f27-556">I browser (senza CORS) non possono eseguire richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="25f27-556">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="25f27-557">Prima di CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) è stato usato per aggirare questa restrizione.</span><span class="sxs-lookup"><span data-stu-id="25f27-557">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="25f27-558">JSONP non usa XHR, usa il `<script>` tag per ricevere la risposta.</span><span class="sxs-lookup"><span data-stu-id="25f27-558">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="25f27-559">Gli script possono essere caricati tra le origini.</span><span class="sxs-lookup"><span data-stu-id="25f27-559">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="25f27-560">La [specifica CORS](https://www.w3.org/TR/cors/) ha introdotto diverse nuove intestazioni HTTP che consentono richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="25f27-560">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="25f27-561">Se un browser supporta CORS, queste intestazioni vengono impostate automaticamente per le richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="25f27-561">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="25f27-562">Il codice JavaScript personalizzato non è necessario per abilitare CORS.</span><span class="sxs-lookup"><span data-stu-id="25f27-562">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="25f27-563">Di seguito è riportato un esempio di richiesta tra le origini.</span><span class="sxs-lookup"><span data-stu-id="25f27-563">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="25f27-564">L' `Origin` intestazione fornisce il dominio del sito che sta effettuando la richiesta.</span><span class="sxs-lookup"><span data-stu-id="25f27-564">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="25f27-565">L' `Origin` intestazione è obbligatoria e deve essere diversa dall'host.</span><span class="sxs-lookup"><span data-stu-id="25f27-565">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="25f27-566">Se il server consente la richiesta, imposta l' `Access-Control-Allow-Origin` intestazione nella risposta.</span><span class="sxs-lookup"><span data-stu-id="25f27-566">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="25f27-567">Il valore di questa intestazione corrisponde all' `Origin` intestazione della richiesta o è il valore del carattere jolly `"*"` , ovvero qualsiasi origine è consentita:</span><span class="sxs-lookup"><span data-stu-id="25f27-567">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="25f27-568">Se la risposta non include l' `Access-Control-Allow-Origin` intestazione, la richiesta tra le origini ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="25f27-568">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="25f27-569">In particolare, il browser non consente la richiesta.</span><span class="sxs-lookup"><span data-stu-id="25f27-569">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="25f27-570">Anche se il server restituisce una risposta con esito positivo, il browser non rende disponibile la risposta all'app client.</span><span class="sxs-lookup"><span data-stu-id="25f27-570">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="25f27-571">Testare CORS</span><span class="sxs-lookup"><span data-stu-id="25f27-571">Test CORS</span></span>

<span data-ttu-id="25f27-572">Per testare CORS:</span><span class="sxs-lookup"><span data-stu-id="25f27-572">To test CORS:</span></span>

1. <span data-ttu-id="25f27-573">[Creare un progetto API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="25f27-573">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="25f27-574">In alternativa, è possibile [scaricare l'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="25f27-574">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="25f27-575">Abilitare CORS usando uno degli approcci descritti in questo documento.</span><span class="sxs-lookup"><span data-stu-id="25f27-575">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="25f27-576">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="25f27-576">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="25f27-577">`WithOrigins("https://localhost:<port>");`deve essere usato solo per il test di un'app di esempio simile al [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)per il download.</span><span class="sxs-lookup"><span data-stu-id="25f27-577">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="25f27-578">Creare un progetto di app Web ( Razor pagine o MVC).</span><span class="sxs-lookup"><span data-stu-id="25f27-578">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="25f27-579">Nell'esempio vengono utilizzate le Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="25f27-579">The sample uses Razor Pages.</span></span> <span data-ttu-id="25f27-580">È possibile creare l'app Web nella stessa soluzione del progetto API.</span><span class="sxs-lookup"><span data-stu-id="25f27-580">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="25f27-581">Aggiungere il codice evidenziato seguente al file *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="25f27-581">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="25f27-582">Nel codice precedente sostituire `url: 'https://<web app>.azurewebsites.net/api/values/1',` con l'URL dell'app distribuita.</span><span class="sxs-lookup"><span data-stu-id="25f27-582">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="25f27-583">Distribuire il progetto API.</span><span class="sxs-lookup"><span data-stu-id="25f27-583">Deploy the API project.</span></span> <span data-ttu-id="25f27-584">Ad esempio, [eseguire la distribuzione in Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="25f27-584">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="25f27-585">Eseguire le Razor pagine o l'app MVC dal desktop e fare clic sul pulsante **test** .</span><span class="sxs-lookup"><span data-stu-id="25f27-585">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="25f27-586">Usare gli strumenti F12 per esaminare i messaggi di errore.</span><span class="sxs-lookup"><span data-stu-id="25f27-586">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="25f27-587">Rimuovere l'origine localhost da `WithOrigins` e distribuire l'app.</span><span class="sxs-lookup"><span data-stu-id="25f27-587">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="25f27-588">In alternativa, eseguire l'app client con una porta diversa.</span><span class="sxs-lookup"><span data-stu-id="25f27-588">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="25f27-589">Ad esempio, eseguire da Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="25f27-589">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="25f27-590">Eseguire il test con l'app client.</span><span class="sxs-lookup"><span data-stu-id="25f27-590">Test with the client app.</span></span> <span data-ttu-id="25f27-591">Gli errori CORS restituiscono un errore, ma il messaggio di errore non è disponibile per JavaScript.</span><span class="sxs-lookup"><span data-stu-id="25f27-591">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="25f27-592">Usare la scheda console negli strumenti F12 per visualizzare l'errore.</span><span class="sxs-lookup"><span data-stu-id="25f27-592">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="25f27-593">A seconda del browser, viene ricevuto un errore (nella console strumenti F12) simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="25f27-593">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="25f27-594">Uso di Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="25f27-594">Using Microsoft Edge:</span></span>

     <span data-ttu-id="25f27-595">**SEC7120: [CORS] l'origine `https://localhost:44375` non è stata trovata `https://localhost:44375` nell'intestazione della risposta Access-Control-Allow-Origin per la risorsa tra le origini in`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="25f27-595">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="25f27-596">Uso di Chrome:</span><span class="sxs-lookup"><span data-stu-id="25f27-596">Using Chrome:</span></span>

     <span data-ttu-id="25f27-597">**L'accesso a XMLHttpRequest `https://webapi.azurewebsites.net/api/values/1` da Origin `https://localhost:44375` è stato bloccato dal criterio CORS: non è presente alcuna intestazione ' Access-Control-Allow-Origin ' nella risorsa richiesta.**</span><span class="sxs-lookup"><span data-stu-id="25f27-597">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="25f27-598">Gli endpoint abilitati per CORS possono essere testati con uno strumento, ad esempio [Fiddler](https://www.telerik.com/fiddler) o [postazione](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="25f27-598">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="25f27-599">Quando si utilizza uno strumento, l'origine della richiesta specificata dall' `Origin` intestazione deve essere diversa dall'host che riceve la richiesta.</span><span class="sxs-lookup"><span data-stu-id="25f27-599">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="25f27-600">Se la richiesta non è *tra origini* basate sul valore dell' `Origin` intestazione:</span><span class="sxs-lookup"><span data-stu-id="25f27-600">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="25f27-601">Non è necessario che il middleware CORS elabori la richiesta.</span><span class="sxs-lookup"><span data-stu-id="25f27-601">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="25f27-602">Le intestazioni CORS non vengono restituite nella risposta.</span><span class="sxs-lookup"><span data-stu-id="25f27-602">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="25f27-603">CORS in IIS</span><span class="sxs-lookup"><span data-stu-id="25f27-603">CORS in IIS</span></span>

<span data-ttu-id="25f27-604">Quando si esegue la distribuzione in IIS, CORS deve essere eseguito prima dell'autenticazione di Windows se il server non è configurato per consentire l'accesso anonimo.</span><span class="sxs-lookup"><span data-stu-id="25f27-604">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="25f27-605">Per supportare questo scenario, è necessario installare e configurare il [modulo cors di IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) per l'app.</span><span class="sxs-lookup"><span data-stu-id="25f27-605">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="25f27-606">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="25f27-606">Additional resources</span></span>

* [<span data-ttu-id="25f27-607">Condivisione risorse tra le origini (CORS)</span><span class="sxs-lookup"><span data-stu-id="25f27-607">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="25f27-608">Introduzione al modulo CORS di IIS</span><span class="sxs-lookup"><span data-stu-id="25f27-608">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
