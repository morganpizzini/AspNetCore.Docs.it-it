---
title: Condividere l'autenticazione cookie tra le app ASP.NET
author: rick-anderson
description: Informazioni su come condividere l'autenticazione cookie tra ASP.NET 4. x e le app ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
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
uid: security/cookie-sharing
ms.openlocfilehash: 0d43bbbc44015aff040b12dfacb260fe50492e54
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252994"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a><span data-ttu-id="eec0b-103">Condividere l'autenticazione cookie tra le app ASP.NET</span><span class="sxs-lookup"><span data-stu-id="eec0b-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="eec0b-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="eec0b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="eec0b-105">I siti Web sono spesso costituiti da singole app Web che interagiscono.</span><span class="sxs-lookup"><span data-stu-id="eec0b-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="eec0b-106">Per offrire un'esperienza di Single Sign-On (SSO), le app Web all'interno di un sito devono condividere le autenticazioni cookie .</span><span class="sxs-lookup"><span data-stu-id="eec0b-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="eec0b-107">Per supportare questo scenario, lo stack di protezione dei dati consente la condivisione dell' cookie autenticazione Katana e dei cookie ticket di autenticazione ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="eec0b-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="eec0b-108">Negli esempi seguenti:</span><span class="sxs-lookup"><span data-stu-id="eec0b-108">In the examples that follow:</span></span>

* <span data-ttu-id="eec0b-109">Il nome dell'autenticazione cookie è impostato su un valore comune di `.AspNet.SharedCookie` .</span><span class="sxs-lookup"><span data-stu-id="eec0b-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="eec0b-110">L'oggetto `AuthenticationType` è impostato su in `Identity.Application` modo esplicito o per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="eec0b-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="eec0b-111">Un nome di app comune viene usato per consentire al sistema di protezione dei dati di condividere le chiavi di protezione dei dati ( `SharedCookieApp` ).</span><span class="sxs-lookup"><span data-stu-id="eec0b-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="eec0b-112">`Identity.Application` viene usato come schema di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="eec0b-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="eec0b-113">Indipendentemente dallo schema utilizzato, è necessario utilizzarlo *in modo coerente all'interno e tra* le cookie app condivise come schema predefinito o impostarlo in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="eec0b-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="eec0b-114">Lo schema viene usato durante la crittografia e la decrittografia di cookie s, quindi è necessario usare uno schema coerente tra le app.</span><span class="sxs-lookup"><span data-stu-id="eec0b-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="eec0b-115">Viene utilizzata una posizione di archiviazione della [chiave di protezione dati](xref:security/data-protection/implementation/key-management) comune.</span><span class="sxs-lookup"><span data-stu-id="eec0b-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="eec0b-116">In ASP.NET Core Apps <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> viene usato per impostare il percorso di archiviazione delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="eec0b-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="eec0b-117">Nelle app .NET Framework il Cookie middleware di autenticazione usa un'implementazione di <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> .</span><span class="sxs-lookup"><span data-stu-id="eec0b-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="eec0b-118">`DataProtectionProvider` fornisce servizi di protezione dei dati per la crittografia e la decrittografia dei dati del payload di autenticazione cookie .</span><span class="sxs-lookup"><span data-stu-id="eec0b-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="eec0b-119">L' `DataProtectionProvider` istanza è isolata dal sistema di protezione dei dati usato da altre parti dell'app.</span><span class="sxs-lookup"><span data-stu-id="eec0b-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="eec0b-120">[DataProtectionProvider. Create (System. io. DirectoryInfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accetta un oggetto <xref:System.IO.DirectoryInfo> per specificare il percorso per l'archiviazione delle chiavi di protezione dati.</span><span class="sxs-lookup"><span data-stu-id="eec0b-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="eec0b-121">`DataProtectionProvider` richiede il pacchetto NuGet [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :</span><span class="sxs-lookup"><span data-stu-id="eec0b-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="eec0b-122">Nelle app ASP.NET Core 2. x fare riferimento al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="eec0b-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="eec0b-123">In .NET Framework app aggiungere un riferimento al pacchetto a [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span><span class="sxs-lookup"><span data-stu-id="eec0b-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="eec0b-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> imposta il nome comune dell'app.</span><span class="sxs-lookup"><span data-stu-id="eec0b-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a><span data-ttu-id="eec0b-125">Condividi autenticazione cookie con ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="eec0b-125">Share authentication cookies with ASP.NET Core Identity</span></span>

<span data-ttu-id="eec0b-126">Quando si usa ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="eec0b-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="eec0b-127">Le chiavi di protezione dei dati e il nome dell'app devono essere condivise tra le app.</span><span class="sxs-lookup"><span data-stu-id="eec0b-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="eec0b-128">Un percorso di archiviazione delle chiavi comune viene fornito al <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> Metodo negli esempi seguenti.</span><span class="sxs-lookup"><span data-stu-id="eec0b-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="eec0b-129">Usare <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> per configurare un nome comune per l'app condivisa ( `SharedCookieApp` negli esempi seguenti).</span><span class="sxs-lookup"><span data-stu-id="eec0b-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="eec0b-130">Per altre informazioni, vedere <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="eec0b-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="eec0b-131">Utilizzare il <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> metodo di estensione per configurare il servizio di protezione dei dati per cookie .</span><span class="sxs-lookup"><span data-stu-id="eec0b-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="eec0b-132">Il tipo di autenticazione predefinito è `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="eec0b-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="eec0b-133">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="eec0b-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

<span data-ttu-id="eec0b-134">**Nota:** Le istruzioni precedenti non funzionano con `ITicketStore` ( `CookieAuthenticationOptions.SessionStore` ).</span><span class="sxs-lookup"><span data-stu-id="eec0b-134">**Note:** The preceding instructions don't work with `ITicketStore` (`CookieAuthenticationOptions.SessionStore`).</span></span>  <span data-ttu-id="eec0b-135">Per altre informazioni, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/21163).</span><span class="sxs-lookup"><span data-stu-id="eec0b-135">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/21163).</span></span>

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a><span data-ttu-id="eec0b-136">Condividi autenticazione cookie senza ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="eec0b-136">Share authentication cookies without ASP.NET Core Identity</span></span>

<span data-ttu-id="eec0b-137">Quando si usano cookie i direttamente senza ASP.NET Core Identity , configurare la protezione dei dati e l'autenticazione in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eec0b-137">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eec0b-138">Nell'esempio seguente, il tipo di autenticazione è impostato su `Identity.Application` :</span><span class="sxs-lookup"><span data-stu-id="eec0b-138">In the following example, the authentication type is set to `Identity.Application`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-no-loccookies-across-different-base-paths"></a><span data-ttu-id="eec0b-139">Condividi cookie tra percorsi di base diversi</span><span class="sxs-lookup"><span data-stu-id="eec0b-139">Share cookies across different base paths</span></span>

<span data-ttu-id="eec0b-140">cookiePer impostazione predefinita, un'autenticazione usa [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) [ Cookie . Percorso](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span><span class="sxs-lookup"><span data-stu-id="eec0b-140">An authentication cookie uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [Cookie.Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span></span> <span data-ttu-id="eec0b-141">Se l'app cookie deve essere condivisa tra percorsi di base diversi, è `Path` necessario eseguire l'override di:</span><span class="sxs-lookup"><span data-stu-id="eec0b-141">If the app's cookie must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a><span data-ttu-id="eec0b-142">Condividi cookie tra i sottodomini</span><span class="sxs-lookup"><span data-stu-id="eec0b-142">Share cookies across subdomains</span></span>

<span data-ttu-id="eec0b-143">Quando si ospitano le app che condividono cookie s nei sottodomini, specificare un dominio comune in [ Cookie . ](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain)Proprietà di dominio.</span><span class="sxs-lookup"><span data-stu-id="eec0b-143">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="eec0b-144">Per condividere cookie tra app in `contoso.com` , ad esempio `first_subdomain.contoso.com` e `second_subdomain.contoso.com` , specificare `Cookie.Domain` come `.contoso.com` :</span><span class="sxs-lookup"><span data-stu-id="eec0b-144">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="eec0b-145">Crittografare le chiavi di protezione dei dati inattivi</span><span class="sxs-lookup"><span data-stu-id="eec0b-145">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="eec0b-146">Per le distribuzioni di produzione, configurare `DataProtectionProvider` per crittografare le chiavi inattive con DPAPI o un X509Certificate.</span><span class="sxs-lookup"><span data-stu-id="eec0b-146">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="eec0b-147">Per altre informazioni, vedere <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="eec0b-147">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="eec0b-148">Nell'esempio seguente viene fornita un'identificazione personale del certificato per <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :</span><span class="sxs-lookup"><span data-stu-id="eec0b-148">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="eec0b-149">Condividere l'autenticazione cookie tra ASP.NET 4. x e app ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eec0b-149">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="eec0b-150">Le app ASP.NET 4. x che usano Cookie il middleware di autenticazione Katana possono essere configurate per generare le autenticazioni cookie compatibili con il Cookie middleware di autenticazione ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="eec0b-150">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="eec0b-151">In questo modo è possibile aggiornare le singole app di un sito di grandi dimensioni in diversi passaggi garantendo al tempo stesso un'esperienza di accesso SSO uniforme nel sito.</span><span class="sxs-lookup"><span data-stu-id="eec0b-151">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="eec0b-152">Quando un'app usa Cookie il middleware di autenticazione Katana, chiama `UseCookieAuthentication` nel file *Startup.auth.cs* del progetto.</span><span class="sxs-lookup"><span data-stu-id="eec0b-152">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="eec0b-153">Per impostazione predefinita, i progetti di app Web ASP.NET 4. x creati con Visual Studio 2013 e versioni successive usano il Cookie middleware di autenticazione Katana.</span><span class="sxs-lookup"><span data-stu-id="eec0b-153">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="eec0b-154">Sebbene `UseCookieAuthentication` sia obsoleto e non supportato per ASP.NET Core app, la chiamata `UseCookieAuthentication` in un'app ASP.NET 4. x che usa il Cookie middleware di autenticazione Katana è valida.</span><span class="sxs-lookup"><span data-stu-id="eec0b-154">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="eec0b-155">Un'app ASP.NET 4. x deve avere come destinazione .NET Framework 4.5.1 o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="eec0b-155">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="eec0b-156">In caso contrario, non è possibile installare i pacchetti NuGet necessari.</span><span class="sxs-lookup"><span data-stu-id="eec0b-156">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="eec0b-157">Per condividere l'autenticazione cookie tra un'app ASP.NET 4. x e un'app ASP.NET Core, configurare l'app ASP.NET Core come indicato nella sezione [autenticazione di condivisione cookie tra ASP.NET Core app](#share-authentication-cookies-with-aspnet-core-identity) , quindi configurare l'app ASP.NET 4. x come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="eec0b-157">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="eec0b-158">Verificare che i pacchetti dell'app vengano aggiornati alle versioni più recenti.</span><span class="sxs-lookup"><span data-stu-id="eec0b-158">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="eec0b-159">Installare il pacchetto [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) in ogni app ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="eec0b-159">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="eec0b-160">Individuare e modificare la chiamata a `UseCookieAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="eec0b-160">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="eec0b-161">Modificare il cookie nome in modo che corrisponda al nome usato dal Cookie middleware di autenticazione ASP.NET Core ( `.AspNet.SharedCookie` nell'esempio).</span><span class="sxs-lookup"><span data-stu-id="eec0b-161">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="eec0b-162">Nell'esempio seguente, il tipo di autenticazione è impostato su `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="eec0b-162">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="eec0b-163">Fornire un'istanza di un oggetto `DataProtectionProvider` inizializzato al percorso di archiviazione della chiave di protezione dati comune.</span><span class="sxs-lookup"><span data-stu-id="eec0b-163">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="eec0b-164">Verificare che il nome dell'app sia impostato sul nome dell'app comune usato da tutte le app che condividono l'autenticazione cookie ( `SharedCookieApp` nell'esempio).</span><span class="sxs-lookup"><span data-stu-id="eec0b-164">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="eec0b-165">Se non `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` si imposta e `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` , impostare <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> su un'attestazione che distingue gli utenti univoci.</span><span class="sxs-lookup"><span data-stu-id="eec0b-165">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="eec0b-166">*App_start/Startup.auth.cs*:</span><span class="sxs-lookup"><span data-stu-id="eec0b-166">*App_Start/Startup.Auth.cs*:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

<span data-ttu-id="eec0b-167">Quando si genera un'identità utente, il tipo di autenticazione ( `Identity.Application` ) deve corrispondere al tipo definito in `AuthenticationType` set con `UseCookieAuthentication` in *app_start/Startup.auth.cs*.</span><span class="sxs-lookup"><span data-stu-id="eec0b-167">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="eec0b-168">*Modelli/ Identity Models.cs*:</span><span class="sxs-lookup"><span data-stu-id="eec0b-168">*Models/IdentityModels.cs*:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a><span data-ttu-id="eec0b-169">Usare un database utente comune</span><span class="sxs-lookup"><span data-stu-id="eec0b-169">Use a common user database</span></span>

<span data-ttu-id="eec0b-170">Quando le app usano lo stesso Identity schema (stessa versione di Identity ), verificare che il Identity sistema per ogni app punti allo stesso database utente.</span><span class="sxs-lookup"><span data-stu-id="eec0b-170">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="eec0b-171">In caso contrario, il sistema di identità genera errori in fase di esecuzione quando tenta di trovare una corrispondenza tra le informazioni nell'autenticazione e cookie quelle del relativo database.</span><span class="sxs-lookup"><span data-stu-id="eec0b-171">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="eec0b-172">Quando lo Identity schema è diverso tra le app, in genere perché le app usano Identity versioni diverse, la condivisione di un database comune basato sulla versione più recente di Identity non è possibile senza rimappare e aggiungere colonne negli schemi di altre app Identity .</span><span class="sxs-lookup"><span data-stu-id="eec0b-172">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="eec0b-173">È spesso più efficiente aggiornare le altre app per usare la versione più recente, in Identity modo che un database comune possa essere condiviso dalle app.</span><span class="sxs-lookup"><span data-stu-id="eec0b-173">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eec0b-174">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="eec0b-174">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
