---
title: Panoramica dell'autenticazione ASP.NET Core
author: mjrousos
description: Informazioni sull'autenticazione in ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/index
ms.openlocfilehash: b0258118e116b1686abbebf1c8d89135ae3cb1f6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019316"
---
# <a name="overview-of-aspnet-core-authentication"></a><span data-ttu-id="04b4c-103">Panoramica dell'autenticazione ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="04b4c-103">Overview of ASP.NET Core authentication</span></span>

<span data-ttu-id="04b4c-104">Di [Mike risveglia](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="04b4c-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="04b4c-105">L'autenticazione è il processo di determinazione dell'identità di un utente.</span><span class="sxs-lookup"><span data-stu-id="04b4c-105">Authentication is the process of determining a user's identity.</span></span> <span data-ttu-id="04b4c-106">L' [autorizzazione](xref:security/authorization/introduction) è il processo che consente di determinare se un utente ha accesso a una risorsa.</span><span class="sxs-lookup"><span data-stu-id="04b4c-106">[Authorization](xref:security/authorization/introduction) is the process of determining whether a user has access to a resource.</span></span> <span data-ttu-id="04b4c-107">In ASP.NET Core, l'autenticazione viene gestita da `IAuthenticationService` , che viene utilizzata dal [middleware](xref:fundamentals/middleware/index)di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="04b4c-107">In ASP.NET Core, authentication is handled by the `IAuthenticationService`, which is used by authentication [middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="04b4c-108">Il servizio di autenticazione usa i gestori di autenticazione registrati per completare le azioni correlate all'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="04b4c-108">The authentication service uses registered authentication handlers to complete authentication-related actions.</span></span> <span data-ttu-id="04b4c-109">Di seguito sono riportati alcuni esempi di azioni correlate all'autenticazione:</span><span class="sxs-lookup"><span data-stu-id="04b4c-109">Examples of authentication-related actions include:</span></span>

* <span data-ttu-id="04b4c-110">Autenticazione di un utente.</span><span class="sxs-lookup"><span data-stu-id="04b4c-110">Authenticating a user.</span></span>
* <span data-ttu-id="04b4c-111">Risposta quando un utente non autenticato tenta di accedere a una risorsa con restrizioni.</span><span class="sxs-lookup"><span data-stu-id="04b4c-111">Responding when an unauthenticated user tries to access a restricted resource.</span></span>

<span data-ttu-id="04b4c-112">I gestori di autenticazione registrati e le relative opzioni di configurazione sono denominati "schemi".</span><span class="sxs-lookup"><span data-stu-id="04b4c-112">The registered authentication handlers and their configuration options are called "schemes".</span></span>

<span data-ttu-id="04b4c-113">Gli schemi di autenticazione vengono specificati registrando i servizi di autenticazione in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="04b4c-113">Authentication schemes are specified by registering authentication services in `Startup.ConfigureServices`:</span></span>

* <span data-ttu-id="04b4c-114">Chiamando un metodo di estensione specifico dello schema dopo una chiamata a `services.AddAuthentication` (ad `AddJwtBearer` esempio o `AddCookie` , ad esempio).</span><span class="sxs-lookup"><span data-stu-id="04b4c-114">By calling a scheme-specific extension method after a call to `services.AddAuthentication` (such as `AddJwtBearer` or `AddCookie`, for example).</span></span> <span data-ttu-id="04b4c-115">Questi metodi di estensione usano [AuthenticationBuilder. AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) per registrare gli schemi con le impostazioni appropriate.</span><span class="sxs-lookup"><span data-stu-id="04b4c-115">These extension methods use [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) to register schemes with appropriate settings.</span></span>
* <span data-ttu-id="04b4c-116">Meno comunemente, chiamando direttamente [AuthenticationBuilder. AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) .</span><span class="sxs-lookup"><span data-stu-id="04b4c-116">Less commonly, by calling [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) directly.</span></span>

<span data-ttu-id="04b4c-117">Il codice seguente, ad esempio, registra i gestori e i servizi di autenticazione per gli cookie schemi di autenticazione di JWT Bearer:</span><span class="sxs-lookup"><span data-stu-id="04b4c-117">For example, the following code registers authentication services and handlers for cookie and JWT bearer authentication schemes:</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

<span data-ttu-id="04b4c-118">Il `AddAuthentication` parametro `JwtBearerDefaults.AuthenticationScheme` è il nome dello schema da utilizzare per impostazione predefinita quando non è richiesto uno schema specifico.</span><span class="sxs-lookup"><span data-stu-id="04b4c-118">The `AddAuthentication` parameter `JwtBearerDefaults.AuthenticationScheme` is the name of the scheme to use by default when a specific scheme isn't requested.</span></span>

<span data-ttu-id="04b4c-119">Se si utilizzano più schemi, i criteri di autorizzazione o gli attributi di autorizzazione possono [specificare lo schema di autenticazione (o gli schemi)](xref:security/authorization/limitingidentitybyscheme) da cui dipendono per autenticare l'utente.</span><span class="sxs-lookup"><span data-stu-id="04b4c-119">If multiple schemes are used, authorization policies (or authorization attributes) can [specify the authentication scheme (or schemes)](xref:security/authorization/limitingidentitybyscheme) they depend on to authenticate the user.</span></span> <span data-ttu-id="04b4c-120">Nell'esempio precedente, lo cookie schema di autenticazione può essere usato specificandone il nome ( `CookieAuthenticationDefaults.AuthenticationScheme` per impostazione predefinita, ma è possibile specificare un nome diverso quando si chiama `AddCookie` ).</span><span class="sxs-lookup"><span data-stu-id="04b4c-120">In the example above, the cookie authentication scheme could be used by specifying its name (`CookieAuthenticationDefaults.AuthenticationScheme` by default, though a different name could be provided when calling `AddCookie`).</span></span>

<span data-ttu-id="04b4c-121">In alcuni casi, la chiamata a `AddAuthentication` viene eseguita automaticamente da altri metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="04b4c-121">In some cases, the call to `AddAuthentication` is automatically made by other extension methods.</span></span> <span data-ttu-id="04b4c-122">Ad esempio, quando si [Usa Identity ASP.NET Core ](xref:security/authentication/identity), `AddAuthentication` viene chiamato internamente.</span><span class="sxs-lookup"><span data-stu-id="04b4c-122">For example, when using [ASP.NET Core Identity](xref:security/authentication/identity), `AddAuthentication` is called internally.</span></span>

<span data-ttu-id="04b4c-123">Il middleware di autenticazione viene aggiunto in chiamando `Startup.Configure` il <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> metodo di estensione nell'app `IApplicationBuilder` .</span><span class="sxs-lookup"><span data-stu-id="04b4c-123">The Authentication middleware is added in `Startup.Configure` by calling the <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> extension method on the app's `IApplicationBuilder`.</span></span> <span data-ttu-id="04b4c-124">La chiamata a `UseAuthentication` registra il middleware che utilizza gli schemi di autenticazione registrati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="04b4c-124">Calling `UseAuthentication` registers the middleware which uses the previously registered authentication schemes.</span></span> <span data-ttu-id="04b4c-125">Chiamare `UseAuthentication` prima di qualsiasi middleware che dipende da utenti autenticati.</span><span class="sxs-lookup"><span data-stu-id="04b4c-125">Call `UseAuthentication` before any middleware that depends on users being authenticated.</span></span> <span data-ttu-id="04b4c-126">Quando si usa il routing degli endpoint, la chiamata a `UseAuthentication` deve essere:</span><span class="sxs-lookup"><span data-stu-id="04b4c-126">When using endpoint routing, the call to `UseAuthentication` must go:</span></span>

* <span data-ttu-id="04b4c-127">Dopo `UseRouting` , in modo che le informazioni sulla Route siano disponibili per le decisioni di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="04b4c-127">After `UseRouting`, so that route information is available for authentication decisions.</span></span>
* <span data-ttu-id="04b4c-128">Prima di `UseEndpoints` , in modo che gli utenti siano autenticati prima di accedere agli endpoint.</span><span class="sxs-lookup"><span data-stu-id="04b4c-128">Before `UseEndpoints`, so that users are authenticated before accessing the endpoints.</span></span>

## <a name="authentication-concepts"></a><span data-ttu-id="04b4c-129">Concetti di autenticazione</span><span class="sxs-lookup"><span data-stu-id="04b4c-129">Authentication Concepts</span></span>

### <a name="authentication-scheme"></a><span data-ttu-id="04b4c-130">Schema di autenticazione</span><span class="sxs-lookup"><span data-stu-id="04b4c-130">Authentication scheme</span></span>

<span data-ttu-id="04b4c-131">Uno schema di autenticazione è un nome che corrisponde a:</span><span class="sxs-lookup"><span data-stu-id="04b4c-131">An authentication scheme is a name which corresponds to:</span></span>

* <span data-ttu-id="04b4c-132">Gestore di autenticazione</span><span class="sxs-lookup"><span data-stu-id="04b4c-132">An authentication handler.</span></span>
* <span data-ttu-id="04b4c-133">Opzioni per la configurazione di un'istanza specifica del gestore.</span><span class="sxs-lookup"><span data-stu-id="04b4c-133">Options for configuring that specific instance of the handler.</span></span>

<span data-ttu-id="04b4c-134">Gli schemi sono utili come meccanismo per fare riferimento ai comportamenti di autenticazione, verifica e proibizione del gestore associato.</span><span class="sxs-lookup"><span data-stu-id="04b4c-134">Schemes are useful as a mechanism for referring to the authentication, challenge, and forbid behaviors of the associated handler.</span></span> <span data-ttu-id="04b4c-135">Un criterio di autorizzazione, ad esempio, può utilizzare i nomi degli schemi per specificare lo schema di autenticazione (o gli schemi) da utilizzare per autenticare l'utente.</span><span class="sxs-lookup"><span data-stu-id="04b4c-135">For example, an authorization policy can use scheme names to specify which authentication scheme (or schemes) should be used to authenticate the user.</span></span> <span data-ttu-id="04b4c-136">Quando si configura l'autenticazione, è normale specificare lo schema di autenticazione predefinito.</span><span class="sxs-lookup"><span data-stu-id="04b4c-136">When configuring authentication, it's common to specify the default authentication scheme.</span></span> <span data-ttu-id="04b4c-137">Viene utilizzato lo schema predefinito, a meno che una risorsa non richieda uno schema specifico.</span><span class="sxs-lookup"><span data-stu-id="04b4c-137">The default scheme is used unless a resource requests a specific scheme.</span></span> <span data-ttu-id="04b4c-138">È anche possibile:</span><span class="sxs-lookup"><span data-stu-id="04b4c-138">It's also possible to:</span></span>

* <span data-ttu-id="04b4c-139">Specificare diversi schemi predefiniti da usare per l'autenticazione, la verifica e la proibizione delle azioni.</span><span class="sxs-lookup"><span data-stu-id="04b4c-139">Specify different default schemes to use for authenticate, challenge, and forbid actions.</span></span>
* <span data-ttu-id="04b4c-140">Combinare più schemi in uno usando gli [schemi dei criteri](xref:security/authentication/policyschemes).</span><span class="sxs-lookup"><span data-stu-id="04b4c-140">Combine multiple schemes into one using [policy schemes](xref:security/authentication/policyschemes).</span></span>

### <a name="authentication-handler"></a><span data-ttu-id="04b4c-141">Gestore di autenticazione</span><span class="sxs-lookup"><span data-stu-id="04b4c-141">Authentication handler</span></span>

<span data-ttu-id="04b4c-142">Un gestore di autenticazione:</span><span class="sxs-lookup"><span data-stu-id="04b4c-142">An authentication handler:</span></span>

* <span data-ttu-id="04b4c-143">È un tipo che implementa il comportamento di uno schema.</span><span class="sxs-lookup"><span data-stu-id="04b4c-143">Is a type that implements the behavior of a scheme.</span></span>
* <span data-ttu-id="04b4c-144">È derivato da <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> o <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</span><span class="sxs-lookup"><span data-stu-id="04b4c-144">Is derived from <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> or <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span>
* <span data-ttu-id="04b4c-145">Ha la responsabilità principale di autenticare gli utenti.</span><span class="sxs-lookup"><span data-stu-id="04b4c-145">Has the primary responsibility to authenticate users.</span></span>

<span data-ttu-id="04b4c-146">In base alla configurazione dello schema di autenticazione e al contesto della richiesta in ingresso, i gestori di autenticazione:</span><span class="sxs-lookup"><span data-stu-id="04b4c-146">Based on the authentication scheme's configuration and the incoming request context, authentication handlers:</span></span>

* <span data-ttu-id="04b4c-147">Crea <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> oggetti che rappresentano l'identità dell'utente se l'autenticazione ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="04b4c-147">Construct <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objects representing the user's identity if authentication is successful.</span></span>
* <span data-ttu-id="04b4c-148">Se l'autenticazione ha esito negativo, restituire ' nessun risultato ' o ' errore '.</span><span class="sxs-lookup"><span data-stu-id="04b4c-148">Return 'no result' or 'failure' if authentication is unsuccessful.</span></span>
* <span data-ttu-id="04b4c-149">Sono disponibili metodi per la verifica e la proibizione delle azioni per gli utenti che tentano di accedere alle risorse:</span><span class="sxs-lookup"><span data-stu-id="04b4c-149">Have methods for challenge and forbid actions for when users attempt to access resources:</span></span>
  * <span data-ttu-id="04b4c-150">Non sono autorizzati ad accedere.</span><span class="sxs-lookup"><span data-stu-id="04b4c-150">They are unauthorized to access (forbid).</span></span>
  * <span data-ttu-id="04b4c-151">Quando non sono autenticati (Challenge).</span><span class="sxs-lookup"><span data-stu-id="04b4c-151">When they are unauthenticated (challenge).</span></span>

### <a name="authenticate"></a><span data-ttu-id="04b4c-152">Authenticate</span><span class="sxs-lookup"><span data-stu-id="04b4c-152">Authenticate</span></span>

<span data-ttu-id="04b4c-153">Un'azione di autenticazione dello schema di autenticazione è responsabile della costruzione dell'identità dell'utente in base al contesto della richiesta.</span><span class="sxs-lookup"><span data-stu-id="04b4c-153">An authentication scheme's authenticate action is responsible for constructing the user's identity based on request context.</span></span> <span data-ttu-id="04b4c-154">Restituisce un valore <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> che indica se l'autenticazione ha avuto esito positivo e, in caso affermativo, l'identità dell'utente in un ticket di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="04b4c-154">It returns an <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> indicating whether authentication was successful and, if so, the user's identity in an authentication ticket.</span></span> <span data-ttu-id="04b4c-155">Vedere <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="04b4c-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>.</span></span> <span data-ttu-id="04b4c-156">Gli esempi di autenticazione includono:</span><span class="sxs-lookup"><span data-stu-id="04b4c-156">Authenticate examples include:</span></span>

* <span data-ttu-id="04b4c-157">Uno cookie schema di autenticazione che costruisce l'identità dell'utente da cookie s.</span><span class="sxs-lookup"><span data-stu-id="04b4c-157">A cookie authentication scheme constructing the user's identity from cookies.</span></span>
* <span data-ttu-id="04b4c-158">Uno schema di portatore JWT che deserializza e convalida un JWT bearer token per costruire l'identità dell'utente.</span><span class="sxs-lookup"><span data-stu-id="04b4c-158">A JWT bearer scheme deserializing and validating a JWT bearer token to construct the user's identity.</span></span>

### <a name="challenge"></a><span data-ttu-id="04b4c-159">Sfida</span><span class="sxs-lookup"><span data-stu-id="04b4c-159">Challenge</span></span>

<span data-ttu-id="04b4c-160">Una richiesta di autenticazione viene richiamata dall'autorizzazione quando un utente non autenticato richiede un endpoint che richiede l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="04b4c-160">An authentication challenge is invoked by Authorization when an unauthenticated user requests an endpoint that requires authentication.</span></span> <span data-ttu-id="04b4c-161">Viene eseguita una richiesta di autenticazione, ad esempio quando un utente anonimo richiede una risorsa limitata o fa clic su un collegamento di accesso.</span><span class="sxs-lookup"><span data-stu-id="04b4c-161">An authentication challenge is issued, for example, when an anonymous user requests a restricted resource or clicks on a login link.</span></span> <span data-ttu-id="04b4c-162">L'autorizzazione richiama una richiesta di verifica utilizzando gli schemi di autenticazione specificati oppure il valore predefinito se non ne viene specificato alcuno.</span><span class="sxs-lookup"><span data-stu-id="04b4c-162">Authorization invokes a challenge using the specified authentication scheme(s), or the default if none is specified.</span></span> <span data-ttu-id="04b4c-163">Vedere <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="04b4c-163">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>.</span></span> <span data-ttu-id="04b4c-164">Gli esempi di richiesta di autenticazione includono:</span><span class="sxs-lookup"><span data-stu-id="04b4c-164">Authentication challenge examples include:</span></span>

* <span data-ttu-id="04b4c-165">Uno cookie schema di autenticazione che reindirizza l'utente a una pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="04b4c-165">A cookie authentication scheme redirecting the user to a login page.</span></span>
* <span data-ttu-id="04b4c-166">Uno schema di portatore JWT che restituisce un risultato 401 con un' `www-authenticate: bearer` intestazione.</span><span class="sxs-lookup"><span data-stu-id="04b4c-166">A JWT bearer scheme returning a 401 result with a `www-authenticate: bearer` header.</span></span>

<span data-ttu-id="04b4c-167">Un'azione di verifica deve consentire all'utente di conoscere il meccanismo di autenticazione da usare per accedere alla risorsa richiesta.</span><span class="sxs-lookup"><span data-stu-id="04b4c-167">A challenge action should let the user know what authentication mechanism to use to access the requested resource.</span></span>

### <a name="forbid"></a><span data-ttu-id="04b4c-168">Impediscono</span><span class="sxs-lookup"><span data-stu-id="04b4c-168">Forbid</span></span>

<span data-ttu-id="04b4c-169">Un'azione di proibisce dello schema di autenticazione viene chiamata dall'autorizzazione quando un utente autenticato tenta di accedere a una risorsa a cui non è consentito l'accesso.</span><span class="sxs-lookup"><span data-stu-id="04b4c-169">An authentication scheme's forbid action is called by Authorization when an authenticated user attempts to access a resource they are not permitted to access.</span></span> <span data-ttu-id="04b4c-170">Vedere <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="04b4c-170">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>.</span></span> <span data-ttu-id="04b4c-171">Gli esempi di autenticazione proibita includono:</span><span class="sxs-lookup"><span data-stu-id="04b4c-171">Authentication forbid examples include:</span></span>
* <span data-ttu-id="04b4c-172">Uno cookie schema di autenticazione che reindirizza l'utente a una pagina che indica che l'accesso non è consentito.</span><span class="sxs-lookup"><span data-stu-id="04b4c-172">A cookie authentication scheme redirecting the user to a page indicating access was forbidden.</span></span>
* <span data-ttu-id="04b4c-173">Uno schema di porta JWT che restituisce un risultato 403.</span><span class="sxs-lookup"><span data-stu-id="04b4c-173">A JWT bearer scheme returning a 403 result.</span></span>
* <span data-ttu-id="04b4c-174">Uno schema di autenticazione personalizzato che reindirizza a una pagina in cui l'utente può richiedere l'accesso alla risorsa.</span><span class="sxs-lookup"><span data-stu-id="04b4c-174">A custom authentication scheme redirecting to a page where the user can request access to the resource.</span></span>

<span data-ttu-id="04b4c-175">Un'azione proibita può comunicare all'utente:</span><span class="sxs-lookup"><span data-stu-id="04b4c-175">A forbid action can let the user know:</span></span>

* <span data-ttu-id="04b4c-176">Sono autenticati.</span><span class="sxs-lookup"><span data-stu-id="04b4c-176">They are authenticated.</span></span>
* <span data-ttu-id="04b4c-177">Non sono autorizzati ad accedere alla risorsa richiesta.</span><span class="sxs-lookup"><span data-stu-id="04b4c-177">They aren't permitted to access the requested resource.</span></span>

<span data-ttu-id="04b4c-178">Vedere i collegamenti seguenti per le differenze tra la verifica e la proibizione:</span><span class="sxs-lookup"><span data-stu-id="04b4c-178">See the following links for differences between challenge and forbid:</span></span>

* <span data-ttu-id="04b4c-179">[Verifica e Impedisci un gestore di risorse operativo](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span><span class="sxs-lookup"><span data-stu-id="04b4c-179">[Challenge and forbid with an operational resource handler](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span></span>
* <span data-ttu-id="04b4c-180">[Differenze tra la sfida e la proibizione](xref:security/authorization/secure-data#challenge).</span><span class="sxs-lookup"><span data-stu-id="04b4c-180">[Differences between challenge and forbid](xref:security/authorization/secure-data#challenge).</span></span>

## <a name="authentication-providers-per-tenant"></a><span data-ttu-id="04b4c-181">Provider di autenticazione per tenant</span><span class="sxs-lookup"><span data-stu-id="04b4c-181">Authentication providers per tenant</span></span>

<span data-ttu-id="04b4c-182">ASP.NET Core Framework non dispone di una soluzione incorporata per l'autenticazione multi-tenant.</span><span class="sxs-lookup"><span data-stu-id="04b4c-182">ASP.NET Core framework does not have a built-in solution for multi-tenant authentication.</span></span>
<span data-ttu-id="04b4c-183">Sebbene sia certamente possibile che i clienti ne scrivano uno, usando le funzionalità predefinite, si consiglia ai clienti di esaminare [Orchard Core](https://www.orchardcore.net/) a questo scopo.</span><span class="sxs-lookup"><span data-stu-id="04b4c-183">While it's certainly possible for customers to write one, using the built-in features, we recommend customers to look into [Orchard Core](https://www.orchardcore.net/) for this purpose.</span></span>

<span data-ttu-id="04b4c-184">Orchard core è:</span><span class="sxs-lookup"><span data-stu-id="04b4c-184">Orchard Core is:</span></span>

* <span data-ttu-id="04b4c-185">Un Framework di app modulare e multi-tenant open source creato con ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="04b4c-185">An open-source modular and multi-tenant app framework built with ASP.NET Core.</span></span>
* <span data-ttu-id="04b4c-186">Un sistema di gestione dei contenuti (CMS) basato su tale framework di app.</span><span class="sxs-lookup"><span data-stu-id="04b4c-186">A content management system (CMS) built on top of that app framework.</span></span>

<span data-ttu-id="04b4c-187">Vedere l'origine [Orchard Core](https://github.com/OrchardCMS/OrchardCore) per un esempio di provider di autenticazione per ogni tenant.</span><span class="sxs-lookup"><span data-stu-id="04b4c-187">See the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) source for an example of authentication providers per tenant.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="04b4c-188">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="04b4c-188">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
* [<span data-ttu-id="04b4c-189">Richiedi utenti autenticati a livello globale</span><span class="sxs-lookup"><span data-stu-id="04b4c-189">Globally require authenticated users</span></span>](xref:security/authorization/secure-data#rau)