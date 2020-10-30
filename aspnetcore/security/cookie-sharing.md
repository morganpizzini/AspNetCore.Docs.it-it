---
title: "Condividere l'autenticazione :::no-loc(cookie)::: tra le app ASP.NET"
author: rick-anderson
description: "Informazioni su come condividere l'autenticazione :::no-loc(cookie)::: tra ASP.NET 4. x e le app ASP.NET Core."
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
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
uid: security/:::no-loc(cookie):::-sharing
ms.openlocfilehash: 8f54f2e4894328f8471d5f80c8184839ce47add6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059689"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a><span data-ttu-id="5f5c9-103">Condividere l'autenticazione :::no-loc(cookie)::: tra le app ASP.NET</span><span class="sxs-lookup"><span data-stu-id="5f5c9-103">Share authentication :::no-loc(cookie):::s among ASP.NET apps</span></span>

<span data-ttu-id="5f5c9-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5f5c9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5f5c9-105">I siti Web sono spesso costituiti da singole app Web che interagiscono.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="5f5c9-106">Per offrire un'esperienza di Single Sign-On (SSO), le app Web all'interno di un sito devono condividere le autenticazioni :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="5f5c9-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication :::no-loc(cookie):::s.</span></span> <span data-ttu-id="5f5c9-107">Per supportare questo scenario, lo stack di protezione dei dati consente la condivisione dell' :::no-loc(cookie)::: autenticazione Katana e dei :::no-loc(cookie)::: ticket di autenticazione ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-107">To support this scenario, the data protection stack allows sharing Katana :::no-loc(cookie)::: authentication and ASP.NET Core :::no-loc(cookie)::: authentication tickets.</span></span>

<span data-ttu-id="5f5c9-108">Negli esempi seguenti:</span><span class="sxs-lookup"><span data-stu-id="5f5c9-108">In the examples that follow:</span></span>

* <span data-ttu-id="5f5c9-109">Il nome dell'autenticazione :::no-loc(cookie)::: è impostato su un valore comune di `.AspNet.Shared:::no-loc(Cookie):::` .</span><span class="sxs-lookup"><span data-stu-id="5f5c9-109">The authentication :::no-loc(cookie)::: name is set to a common value of `.AspNet.Shared:::no-loc(Cookie):::`.</span></span>
* <span data-ttu-id="5f5c9-110">L'oggetto `AuthenticationType` è impostato su in `:::no-loc(Identity):::.Application` modo esplicito o per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-110">The `AuthenticationType` is set to `:::no-loc(Identity):::.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="5f5c9-111">Un nome di app comune viene usato per consentire al sistema di protezione dei dati di condividere le chiavi di protezione dei dati ( `Shared:::no-loc(Cookie):::App` ).</span><span class="sxs-lookup"><span data-stu-id="5f5c9-111">A common app name is used to enable the data protection system to share data protection keys (`Shared:::no-loc(Cookie):::App`).</span></span>
* <span data-ttu-id="5f5c9-112">`:::no-loc(Identity):::.Application` viene usato come schema di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-112">`:::no-loc(Identity):::.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="5f5c9-113">Indipendentemente dallo schema utilizzato, è necessario utilizzarlo *in modo coerente all'interno e tra* le :::no-loc(cookie)::: app condivise come schema predefinito o impostarlo in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-113">Whatever scheme is used, it must be used consistently *within and across* the shared :::no-loc(cookie)::: apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="5f5c9-114">Lo schema viene usato durante la crittografia e la decrittografia di :::no-loc(cookie)::: s, quindi è necessario usare uno schema coerente tra le app.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-114">The scheme is used when encrypting and decrypting :::no-loc(cookie):::s, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="5f5c9-115">Viene utilizzata una posizione di archiviazione della [chiave di protezione dati](xref:security/data-protection/implementation/key-management) comune.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="5f5c9-116">In ASP.NET Core Apps <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> viene usato per impostare il percorso di archiviazione delle chiavi.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="5f5c9-117">Nelle app .NET Framework il :::no-loc(Cookie)::: middleware di autenticazione usa un'implementazione di <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> .</span><span class="sxs-lookup"><span data-stu-id="5f5c9-117">In .NET Framework apps, :::no-loc(Cookie)::: Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="5f5c9-118">`DataProtectionProvider` fornisce servizi di protezione dei dati per la crittografia e la decrittografia dei dati del payload di autenticazione :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="5f5c9-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication :::no-loc(cookie)::: payload data.</span></span> <span data-ttu-id="5f5c9-119">L' `DataProtectionProvider` istanza è isolata dal sistema di protezione dei dati usato da altre parti dell'app.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="5f5c9-120">[DataProtectionProvider. Create (System. io. DirectoryInfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accetta un oggetto <xref:System.IO.DirectoryInfo> per specificare il percorso per l'archiviazione delle chiavi di protezione dati.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="5f5c9-121">`DataProtectionProvider` richiede il pacchetto NuGet [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :</span><span class="sxs-lookup"><span data-stu-id="5f5c9-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="5f5c9-122">Nelle app ASP.NET Core 2. x fare riferimento al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="5f5c9-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="5f5c9-123">In .NET Framework app aggiungere un riferimento al pacchetto a [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span><span class="sxs-lookup"><span data-stu-id="5f5c9-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="5f5c9-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> imposta il nome comune dell'app.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a><span data-ttu-id="5f5c9-125">Condividi autenticazione :::no-loc(cookie)::: con :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="5f5c9-125">Share authentication :::no-loc(cookie):::s with :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="5f5c9-126">Quando si usa :::no-loc(ASP.NET Core Identity)::::</span><span class="sxs-lookup"><span data-stu-id="5f5c9-126">When using :::no-loc(ASP.NET Core Identity)::::</span></span>

* <span data-ttu-id="5f5c9-127">Le chiavi di protezione dei dati e il nome dell'app devono essere condivise tra le app.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="5f5c9-128">Un percorso di archiviazione delle chiavi comune viene fornito al <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> Metodo negli esempi seguenti.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="5f5c9-129">Usare <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> per configurare un nome comune per l'app condivisa ( `Shared:::no-loc(Cookie):::App` negli esempi seguenti).</span><span class="sxs-lookup"><span data-stu-id="5f5c9-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`Shared:::no-loc(Cookie):::App` in the following examples).</span></span> <span data-ttu-id="5f5c9-130">Per altre informazioni, vedere <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="5f5c9-131">Utilizzare il <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.ConfigureApplication:::no-loc(Cookie):::*> metodo di estensione per configurare il servizio di protezione dei dati per :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="5f5c9-131">Use the <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.ConfigureApplication:::no-loc(Cookie):::*> extension method to set up the data protection service for :::no-loc(cookie):::s.</span></span>
* <span data-ttu-id="5f5c9-132">Il tipo di autenticazione predefinito è `:::no-loc(Identity):::.Application` .</span><span class="sxs-lookup"><span data-stu-id="5f5c9-132">The default authentication type is `:::no-loc(Identity):::.Application`.</span></span>

<span data-ttu-id="5f5c9-133">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5f5c9-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("Shared:::no-loc(Cookie):::App");

services.ConfigureApplication:::no-loc(Cookie):::(options => {
    options.:::no-loc(Cookie):::.Name = ".AspNet.Shared:::no-loc(Cookie):::";
});
```

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a><span data-ttu-id="5f5c9-134">Condividi autenticazione :::no-loc(cookie)::: senza :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="5f5c9-134">Share authentication :::no-loc(cookie):::s without :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="5f5c9-135">Quando si usano :::no-loc(cookie)::: i direttamente senza :::no-loc(ASP.NET Core Identity)::: , configurare la protezione dei dati e l'autenticazione in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5f5c9-135">When using :::no-loc(cookie):::s directly without :::no-loc(ASP.NET Core Identity):::, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5f5c9-136">Nell'esempio seguente, il tipo di autenticazione è impostato su `:::no-loc(Identity):::.Application` :</span><span class="sxs-lookup"><span data-stu-id="5f5c9-136">In the following example, the authentication type is set to `:::no-loc(Identity):::.Application`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("Shared:::no-loc(Cookie):::App");

services.AddAuthentication(":::no-loc(Identity):::.Application")
    .Add:::no-loc(Cookie):::(":::no-loc(Identity):::.Application", options =>
    {
        options.:::no-loc(Cookie):::.Name = ".AspNet.Shared:::no-loc(Cookie):::";
    });
```

## <a name="share-no-loccookies-across-different-base-paths"></a><span data-ttu-id="5f5c9-137">Condividi :::no-loc(cookie)::: tra percorsi di base diversi</span><span class="sxs-lookup"><span data-stu-id="5f5c9-137">Share :::no-loc(cookie):::s across different base paths</span></span>

<span data-ttu-id="5f5c9-138">:::no-loc(cookie):::Per impostazione predefinita, un'autenticazione usa [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) [ :::no-loc(Cookie)::: . Percorso](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Path).</span><span class="sxs-lookup"><span data-stu-id="5f5c9-138">An authentication :::no-loc(cookie)::: uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [:::no-loc(Cookie):::.Path](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Path).</span></span> <span data-ttu-id="5f5c9-139">Se l'app :::no-loc(cookie)::: deve essere condivisa tra percorsi di base diversi, è `Path` necessario eseguire l'override di:</span><span class="sxs-lookup"><span data-stu-id="5f5c9-139">If the app's :::no-loc(cookie)::: must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("Shared:::no-loc(Cookie):::App");

services.ConfigureApplication:::no-loc(Cookie):::(options => {
    options.:::no-loc(Cookie):::.Name = ".AspNet.Shared:::no-loc(Cookie):::";
    options.:::no-loc(Cookie):::.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a><span data-ttu-id="5f5c9-140">Condividi :::no-loc(cookie)::: tra i sottodomini</span><span class="sxs-lookup"><span data-stu-id="5f5c9-140">Share :::no-loc(cookie):::s across subdomains</span></span>

<span data-ttu-id="5f5c9-141">Quando si ospitano le app che condividono :::no-loc(cookie)::: s nei sottodomini, specificare un dominio comune in [ :::no-loc(Cookie)::: . ](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Domain)Proprietà di dominio.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-141">When hosting apps that share :::no-loc(cookie):::s across subdomains, specify a common domain in the [:::no-loc(Cookie):::.Domain](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Domain) property.</span></span> <span data-ttu-id="5f5c9-142">Per condividere :::no-loc(cookie)::: tra app in `contoso.com` , ad esempio `first_subdomain.contoso.com` e `second_subdomain.contoso.com` , specificare `:::no-loc(Cookie):::.Domain` come `.contoso.com` :</span><span class="sxs-lookup"><span data-stu-id="5f5c9-142">To share :::no-loc(cookie):::s across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `:::no-loc(Cookie):::.Domain` as `.contoso.com`:</span></span>

```csharp
options.:::no-loc(Cookie):::.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="5f5c9-143">Crittografare le chiavi di protezione dei dati inattivi</span><span class="sxs-lookup"><span data-stu-id="5f5c9-143">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="5f5c9-144">Per le distribuzioni di produzione, configurare `DataProtectionProvider` per crittografare le chiavi inattive con DPAPI o un X509Certificate.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-144">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="5f5c9-145">Per altre informazioni, vedere <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-145">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="5f5c9-146">Nell'esempio seguente viene fornita un'identificazione personale del certificato per <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :</span><span class="sxs-lookup"><span data-stu-id="5f5c9-146">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="5f5c9-147">Condividere l'autenticazione :::no-loc(cookie)::: tra ASP.NET 4. x e app ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5f5c9-147">Share authentication :::no-loc(cookie):::s between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="5f5c9-148">Le app ASP.NET 4. x che usano :::no-loc(Cookie)::: il middleware di autenticazione Katana possono essere configurate per generare le autenticazioni :::no-loc(cookie)::: compatibili con il :::no-loc(Cookie)::: middleware di autenticazione ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-148">ASP.NET 4.x apps that use Katana :::no-loc(Cookie)::: Authentication Middleware can be configured to generate authentication :::no-loc(cookie):::s that are compatible with the ASP.NET Core :::no-loc(Cookie)::: Authentication Middleware.</span></span> <span data-ttu-id="5f5c9-149">In questo modo è possibile aggiornare le singole app di un sito di grandi dimensioni in diversi passaggi garantendo al tempo stesso un'esperienza di accesso SSO uniforme nel sito.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-149">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="5f5c9-150">Quando un'app usa :::no-loc(Cookie)::: il middleware di autenticazione Katana, chiama `Use:::no-loc(Cookie):::Authentication` nel file *Startup.auth.cs* del progetto.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-150">When an app uses Katana :::no-loc(Cookie)::: Authentication Middleware, it calls `Use:::no-loc(Cookie):::Authentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="5f5c9-151">Per impostazione predefinita, i progetti di app Web ASP.NET 4. x creati con Visual Studio 2013 e versioni successive usano il :::no-loc(Cookie)::: middleware di autenticazione Katana.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-151">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana :::no-loc(Cookie)::: Authentication Middleware by default.</span></span> <span data-ttu-id="5f5c9-152">Sebbene `Use:::no-loc(Cookie):::Authentication` sia obsoleto e non supportato per ASP.NET Core app, la chiamata `Use:::no-loc(Cookie):::Authentication` in un'app ASP.NET 4. x che usa il :::no-loc(Cookie)::: middleware di autenticazione Katana è valida.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-152">Although `Use:::no-loc(Cookie):::Authentication` is obsolete and unsupported for ASP.NET Core apps, calling `Use:::no-loc(Cookie):::Authentication` in an ASP.NET 4.x app that uses Katana :::no-loc(Cookie)::: Authentication Middleware is valid.</span></span>

<span data-ttu-id="5f5c9-153">Un'app ASP.NET 4. x deve avere come destinazione .NET Framework 4.5.1 o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-153">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="5f5c9-154">In caso contrario, non è possibile installare i pacchetti NuGet necessari.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-154">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="5f5c9-155">Per condividere l'autenticazione :::no-loc(cookie)::: tra un'app ASP.NET 4. x e un'app ASP.NET Core, configurare l'app ASP.NET Core come indicato nella sezione [autenticazione di condivisione :::no-loc(cookie)::: tra ASP.NET Core app](#share-authentication-:::no-loc(cookie):::s-with-aspnet-core-identity) , quindi configurare l'app ASP.NET 4. x come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-155">To share authentication :::no-loc(cookie):::s between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication :::no-loc(cookie):::s among ASP.NET Core apps](#share-authentication-:::no-loc(cookie):::s-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="5f5c9-156">Verificare che i pacchetti dell'app vengano aggiornati alle versioni più recenti.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-156">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="5f5c9-157">Installare il pacchetto [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) in ogni app ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-157">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="5f5c9-158">Individuare e modificare la chiamata a `Use:::no-loc(Cookie):::Authentication` :</span><span class="sxs-lookup"><span data-stu-id="5f5c9-158">Locate and modify the call to `Use:::no-loc(Cookie):::Authentication`:</span></span>

* <span data-ttu-id="5f5c9-159">Modificare il :::no-loc(cookie)::: nome in modo che corrisponda al nome usato dal :::no-loc(Cookie)::: middleware di autenticazione ASP.NET Core ( `.AspNet.Shared:::no-loc(Cookie):::` nell'esempio).</span><span class="sxs-lookup"><span data-stu-id="5f5c9-159">Change the :::no-loc(cookie)::: name to match the name used by the ASP.NET Core :::no-loc(Cookie)::: Authentication Middleware (`.AspNet.Shared:::no-loc(Cookie):::` in the example).</span></span>
* <span data-ttu-id="5f5c9-160">Nell'esempio seguente, il tipo di autenticazione è impostato su `:::no-loc(Identity):::.Application` .</span><span class="sxs-lookup"><span data-stu-id="5f5c9-160">In the following example, the authentication type is set to `:::no-loc(Identity):::.Application`.</span></span>
* <span data-ttu-id="5f5c9-161">Fornire un'istanza di un oggetto `DataProtectionProvider` inizializzato al percorso di archiviazione della chiave di protezione dati comune.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-161">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="5f5c9-162">Verificare che il nome dell'app sia impostato sul nome dell'app comune usato da tutte le app che condividono l'autenticazione :::no-loc(cookie)::: ( `Shared:::no-loc(Cookie):::App` nell'esempio).</span><span class="sxs-lookup"><span data-stu-id="5f5c9-162">Confirm that the app name is set to the common app name used by all apps that share authentication :::no-loc(cookie):::s (`Shared:::no-loc(Cookie):::App` in the example).</span></span>

<span data-ttu-id="5f5c9-163">Se non `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` si imposta e `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` , impostare <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> su un'attestazione che distingue gli utenti univoci.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-163">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="5f5c9-164">*App_start/Startup.auth.cs* :</span><span class="sxs-lookup"><span data-stu-id="5f5c9-164">*App_Start/Startup.Auth.cs* :</span></span>

```csharp
app.Use:::no-loc(Cookie):::Authentication(new :::no-loc(Cookie):::AuthenticationOptions
{
    AuthenticationType = ":::no-loc(Identity):::.Application",
    :::no-loc(Cookie):::Name = ".AspNet.Shared:::no-loc(Cookie):::",
    LoginPath = new PathString("/Account/Login"),
    Provider = new :::no-loc(Cookie):::AuthenticationProvider
    {
        OnValidate:::no-loc(Identity)::: =
            SecurityStampValidator
                .OnValidate:::no-loc(Identity):::<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerate:::no-loc(Identity):::: (manager, user) =>
                        user.GenerateUser:::no-loc(Identity):::Async(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("Shared:::no-loc(Cookie):::App"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s." +
                    ":::no-loc(Cookie):::AuthenticationMiddleware",
                ":::no-loc(Identity):::.Application",
                "v2"))),
    :::no-loc(Cookie):::Manager = new Chunking:::no-loc(Cookie):::Manager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

<span data-ttu-id="5f5c9-165">Quando si genera un'identità utente, il tipo di autenticazione ( `:::no-loc(Identity):::.Application` ) deve corrispondere al tipo definito in `AuthenticationType` set con `Use:::no-loc(Cookie):::Authentication` in *app_start/Startup.auth.cs* .</span><span class="sxs-lookup"><span data-stu-id="5f5c9-165">When generating a user identity, the authentication type (`:::no-loc(Identity):::.Application`) must match the type defined in `AuthenticationType` set with `Use:::no-loc(Cookie):::Authentication` in *App_Start/Startup.Auth.cs* .</span></span>

<span data-ttu-id="5f5c9-166">*Modelli/ :::no-loc(Identity)::: Models.cs* :</span><span class="sxs-lookup"><span data-stu-id="5f5c9-166">*Models/:::no-loc(Identity):::Models.cs* :</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public async Task<Claims:::no-loc(Identity):::> GenerateUser:::no-loc(Identity):::Async(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // :::no-loc(Cookie):::AuthenticationOptions.AuthenticationType
        var user:::no-loc(Identity)::: = 
            await manager.Create:::no-loc(Identity):::Async(this, ":::no-loc(Identity):::.Application");

        // Add custom user claims here

        return user:::no-loc(Identity):::;
    }
}
```

## <a name="use-a-common-user-database"></a><span data-ttu-id="5f5c9-167">Usare un database utente comune</span><span class="sxs-lookup"><span data-stu-id="5f5c9-167">Use a common user database</span></span>

<span data-ttu-id="5f5c9-168">Quando le app usano lo stesso :::no-loc(Identity)::: schema (stessa versione di :::no-loc(Identity)::: ), verificare che il :::no-loc(Identity)::: sistema per ogni app punti allo stesso database utente.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-168">When apps use the same :::no-loc(Identity)::: schema (same version of :::no-loc(Identity):::), confirm that the :::no-loc(Identity)::: system for each app is pointed at the same user database.</span></span> <span data-ttu-id="5f5c9-169">In caso contrario, il sistema di identità genera errori in fase di esecuzione quando tenta di trovare una corrispondenza tra le informazioni nell'autenticazione e :::no-loc(cookie)::: quelle del relativo database.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-169">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication :::no-loc(cookie)::: against the information in its database.</span></span>

<span data-ttu-id="5f5c9-170">Quando lo :::no-loc(Identity)::: schema è diverso tra le app, in genere perché le app usano :::no-loc(Identity)::: versioni diverse, la condivisione di un database comune basato sulla versione più recente di :::no-loc(Identity)::: non è possibile senza rimappare e aggiungere colonne negli schemi di altre app :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="5f5c9-170">When the :::no-loc(Identity)::: schema is different among apps, usually because apps are using different :::no-loc(Identity)::: versions, sharing a common database based on the latest version of :::no-loc(Identity)::: isn't possible without remapping and adding columns in other app's :::no-loc(Identity)::: schemas.</span></span> <span data-ttu-id="5f5c9-171">È spesso più efficiente aggiornare le altre app per usare la versione più recente, in :::no-loc(Identity)::: modo che un database comune possa essere condiviso dalle app.</span><span class="sxs-lookup"><span data-stu-id="5f5c9-171">It's often more efficient to upgrade the other apps to use the latest :::no-loc(Identity)::: version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5f5c9-172">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="5f5c9-172">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
