---
title: Autorizzare con uno schema specifico in ASP.NET Core
author: rick-anderson
description: In questo articolo viene illustrato come limitare l'identità a uno schema specifico quando si utilizzano più metodi di autenticazione.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 4dc86480d40d8ee40b3c03aa7fd2994e6c15b105
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053124"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a><span data-ttu-id="a4f7f-103">Autorizzare con uno schema specifico in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a4f7f-103">Authorize with a specific scheme in ASP.NET Core</span></span>

<span data-ttu-id="a4f7f-104">In alcuni scenari, ad esempio le applicazioni a pagina singola (Spa), è comune usare più metodi di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-104">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="a4f7f-105">Ad esempio, l'app può usare l' :::no-loc(cookie)::: autenticazione basata su per accedere e l'autenticazione di connessione JWT per le richieste JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-105">For example, the app may use :::no-loc(cookie):::-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="a4f7f-106">In alcuni casi, l'app può avere più istanze di un gestore di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-106">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="a4f7f-107">Ad esempio, due :::no-loc(cookie)::: gestori dove uno contiene un'identità di base e uno viene creato quando viene attivata un'autenticazione a più fattori (multi-factor authentication).</span><span class="sxs-lookup"><span data-stu-id="a4f7f-107">For example, two :::no-loc(cookie)::: handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="a4f7f-108">L'autenticazione a più fattori può essere attivata perché l'utente ha richiesto un'operazione che richiede una maggiore sicurezza.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-108">MFA may be triggered because the user requested an operation that requires extra security.</span></span> <span data-ttu-id="a4f7f-109">Per altre informazioni sull'applicazione dell'autenticazione a più fattori quando un utente richiede una risorsa che richiede l'autenticazione a più fattori, vedere la [sezione relativa alla protezione](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195)dei problemi di GitHub con l'autenticazione a</span><span class="sxs-lookup"><span data-stu-id="a4f7f-109">For more information on enforcing MFA when a user requests a resource that requires MFA, see the GitHub issue [Protect section with MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).</span></span>

<span data-ttu-id="a4f7f-110">Uno schema di autenticazione viene denominato quando il servizio di autenticazione viene configurato durante l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-110">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="a4f7f-111">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a4f7f-111">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .Add:::no-loc(Cookie):::(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

<span data-ttu-id="a4f7f-112">Nel codice precedente sono stati aggiunti due gestori di autenticazione: uno per :::no-loc(cookie)::: s e uno per Bearer.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-112">In the preceding code, two authentication handlers have been added: one for :::no-loc(cookie):::s and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="a4f7f-113">Se si specifica lo schema predefinito `HttpContext.User` , la proprietà viene impostata su tale identità.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-113">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="a4f7f-114">Se questo comportamento non è necessario, disabilitarlo richiamando il formato senza parametri di `AddAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="a4f7f-114">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="a4f7f-115">Selezione dello schema con l'attributo autorizzi</span><span class="sxs-lookup"><span data-stu-id="a4f7f-115">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="a4f7f-116">Al momento dell'autorizzazione, l'app indica il gestore da usare.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-116">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="a4f7f-117">Selezionare il gestore con cui l'app autorizzerà il passaggio di un elenco delimitato da virgole di schemi di autenticazione a `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="a4f7f-117">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="a4f7f-118">L' `[Authorize]` attributo specifica lo schema o gli schemi di autenticazione da utilizzare, indipendentemente dal fatto che sia configurato un valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-118">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="a4f7f-119">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a4f7f-119">For example:</span></span>

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

<span data-ttu-id="a4f7f-120">Nell'esempio precedente, entrambi i :::no-loc(cookie)::: gestori di porta e sono eseguiti e hanno la possibilità di creare e aggiungere un'identità per l'utente corrente.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-120">In the preceding example, both the :::no-loc(cookie)::: and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="a4f7f-121">Specificando solo un singolo schema, viene eseguito il gestore corrispondente.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-121">By specifying a single scheme only, the corresponding handler runs.</span></span>

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

<span data-ttu-id="a4f7f-122">Nel codice precedente viene eseguito solo il gestore con lo schema "Bearer".</span><span class="sxs-lookup"><span data-stu-id="a4f7f-122">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="a4f7f-123">Qualsiasi :::no-loc(cookie)::: identità basata su viene ignorata.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-123">Any :::no-loc(cookie):::-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="a4f7f-124">Selezione dello schema con i criteri</span><span class="sxs-lookup"><span data-stu-id="a4f7f-124">Selecting the scheme with policies</span></span>

<span data-ttu-id="a4f7f-125">Se si preferisce specificare gli schemi desiderati nel [criterio](xref:security/authorization/policies), è possibile impostare la `AuthenticationSchemes` raccolta quando si aggiungono i criteri:</span><span class="sxs-lookup"><span data-stu-id="a4f7f-125">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add(JwtBearerDefaults.AuthenticationScheme);
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

<span data-ttu-id="a4f7f-126">Nell'esempio precedente, il criterio "Over18" viene eseguito solo con l'identità creata dal gestore "Bearer".</span><span class="sxs-lookup"><span data-stu-id="a4f7f-126">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="a4f7f-127">Usare il criterio impostando la `[Authorize]` proprietà dell'attributo `Policy` :</span><span class="sxs-lookup"><span data-stu-id="a4f7f-127">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a><span data-ttu-id="a4f7f-128">Usare più schemi di autenticazione</span><span class="sxs-lookup"><span data-stu-id="a4f7f-128">Use multiple authentication schemes</span></span>

<span data-ttu-id="a4f7f-129">Alcune app potrebbero dover supportare più tipi di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-129">Some apps may need to support multiple types of authentication.</span></span> <span data-ttu-id="a4f7f-130">Ad esempio, l'app potrebbe autenticare gli utenti da Azure Active Directory e da un database degli utenti.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-130">For example, your app might authenticate users from Azure Active Directory and from a users database.</span></span> <span data-ttu-id="a4f7f-131">Un altro esempio è un'app che autentica gli utenti da Active Directory Federation Services e Azure Active Directory B2C.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-131">Another example is an app that authenticates users from both Active Directory Federation Services and Azure Active Directory B2C.</span></span> <span data-ttu-id="a4f7f-132">In questo caso, l'app deve accettare una bearer token JWT da diverse autorità emittenti.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-132">In this case, the app should accept a JWT bearer token from several issuers.</span></span>

<span data-ttu-id="a4f7f-133">Aggiungere tutti gli schemi di autenticazione che si desidera accettare.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-133">Add all authentication schemes you'd like to accept.</span></span> <span data-ttu-id="a4f7f-134">Ad esempio, il codice seguente in `Startup.ConfigureServices` aggiunge due schemi di autenticazione JWT Bearer con emittenti differenti:</span><span class="sxs-lookup"><span data-stu-id="a4f7f-134">For example, the following code in `Startup.ConfigureServices` adds two JWT bearer authentication schemes with different issuers:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://localhost:5000/identity/";
        })
        .AddJwtBearer("AzureAD", options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://login.microsoftonline.com/eb971100-6f99-4bdc-8611-1bc8edd7f436/";
        });
}
```

> [!NOTE]
> <span data-ttu-id="a4f7f-135">Con lo schema di autenticazione predefinito viene registrata una sola autenticazione JWT Bearer `JwtBearerDefaults.AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="a4f7f-135">Only one JWT bearer authentication is registered with the default authentication scheme `JwtBearerDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="a4f7f-136">È necessario registrare un'autenticazione aggiuntiva con uno schema di autenticazione univoco.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-136">Additional authentication has to be registered with a unique authentication scheme.</span></span>

<span data-ttu-id="a4f7f-137">Il passaggio successivo consiste nell'aggiornare i criteri di autorizzazione predefiniti affinché accettino entrambi gli schemi di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-137">The next step is to update the default authorization policy to accept both authentication schemes.</span></span> <span data-ttu-id="a4f7f-138">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a4f7f-138">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthorization(options =>
    {
        var defaultAuthorizationPolicyBuilder = new AuthorizationPolicyBuilder(
            JwtBearerDefaults.AuthenticationScheme,
            "AzureAD");
        defaultAuthorizationPolicyBuilder = 
            defaultAuthorizationPolicyBuilder.RequireAuthenticatedUser();
        options.DefaultPolicy = defaultAuthorizationPolicyBuilder.Build();
    });
}
```

<span data-ttu-id="a4f7f-139">Quando viene eseguito l'override dei criteri di autorizzazione predefiniti, è possibile usare l' `[Authorize]` attributo nei controller.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-139">As the default authorization policy is overridden, it's possible to use the `[Authorize]` attribute in controllers.</span></span> <span data-ttu-id="a4f7f-140">Il controller accetta quindi le richieste con JWT emesso dal primo o dal secondo emittente.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-140">The controller then accepts requests with JWT issued by the first or second issuer.</span></span>

::: moniker-end
