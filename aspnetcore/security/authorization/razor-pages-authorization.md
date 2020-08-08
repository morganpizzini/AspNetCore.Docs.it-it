---
title: RazorConvenzioni di autorizzazione per le pagine in ASP.NET Core
author: rick-anderson
description: Informazioni su come controllare l'accesso alle pagine con convenzioni che autorizzano gli utenti e consentono agli utenti anonimi di accedere a pagine o cartelle di pagine.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
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
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 5e0c76ea6c6b4af8e24693b1bfe8ac28bf2ce3a9
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022277"
---
# <a name="no-locrazor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="e6200-103">RazorConvenzioni di autorizzazione per le pagine in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e6200-103">Razor Pages authorization conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e6200-104">Un modo per controllare l'accesso all' Razor app pagine consiste nell'usare le convenzioni di autorizzazione all'avvio.</span><span class="sxs-lookup"><span data-stu-id="e6200-104">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="e6200-105">Queste convenzioni consentono di autorizzare gli utenti e consentire agli utenti anonimi di accedere a singole pagine o cartelle di pagine.</span><span class="sxs-lookup"><span data-stu-id="e6200-105">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="e6200-106">Le convenzioni descritte in questo argomento applicano automaticamente i [filtri di autorizzazione](xref:mvc/controllers/filters#authorization-filters) per controllare l'accesso.</span><span class="sxs-lookup"><span data-stu-id="e6200-106">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="e6200-107">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e6200-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e6200-108">L'app di esempio usa l' [ cookie autenticazione Identity senza ASP.NET Core ](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="e6200-108">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="e6200-109">I concetti e gli esempi illustrati in questo argomento si applicano ugualmente alle app che usano ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="e6200-109">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="e6200-110">Per usare ASP.NET Core Identity , seguire le istruzioni riportate in <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="e6200-110">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="e6200-111">Richiedi autorizzazione per accedere a una pagina</span><span class="sxs-lookup"><span data-stu-id="e6200-111">Require authorization to access a page</span></span>

<span data-ttu-id="e6200-112">Utilizzare la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convenzione per aggiungere un oggetto <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> alla pagina nel percorso specificato:</span><span class="sxs-lookup"><span data-stu-id="e6200-112">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="e6200-113">Il percorso specificato è il percorso del motore di visualizzazione, ovvero il Razor percorso relativo della radice delle pagine senza un'estensione e contenente solo barre.</span><span class="sxs-lookup"><span data-stu-id="e6200-113">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="e6200-114">Per specificare un [criterio di autorizzazione](xref:security/authorization/policies), usare un [Overload AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="e6200-114">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="e6200-115">Un oggetto <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> può essere applicato a una classe del modello di pagina con l' `[Authorize]` attributo Filter.</span><span class="sxs-lookup"><span data-stu-id="e6200-115">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="e6200-116">Per ulteriori informazioni, vedere [autorizzazione filtro attributo](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="e6200-116">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="e6200-117">Richiedi autorizzazione per accedere a una cartella di pagine</span><span class="sxs-lookup"><span data-stu-id="e6200-117">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="e6200-118">Utilizzare la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convenzione per aggiungere un oggetto <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> a tutte le pagine di una cartella nel percorso specificato:</span><span class="sxs-lookup"><span data-stu-id="e6200-118">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=4)]

<span data-ttu-id="e6200-119">Il percorso specificato è il percorso del motore di visualizzazione, ovvero il Razor percorso relativo della radice delle pagine.</span><span class="sxs-lookup"><span data-stu-id="e6200-119">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="e6200-120">Per specificare un [criterio di autorizzazione](xref:security/authorization/policies), usare un [Overload AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="e6200-120">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="e6200-121">Richiedi autorizzazione per accedere a una pagina di area</span><span class="sxs-lookup"><span data-stu-id="e6200-121">Require authorization to access an area page</span></span>

<span data-ttu-id="e6200-122">Utilizzare la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convenzione per aggiungere un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> alla pagina area nel percorso specificato:</span><span class="sxs-lookup"><span data-stu-id="e6200-122">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="e6200-123">Il nome della pagina è il percorso del file senza estensione rispetto alla directory radice delle pagine per l'area specificata.</span><span class="sxs-lookup"><span data-stu-id="e6200-123">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="e6200-124">Ad esempio, il nome della pagina per le *aree file/ Identity /pages/Manage/accounts.cshtml* è */Manage/accounts*.</span><span class="sxs-lookup"><span data-stu-id="e6200-124">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="e6200-125">Per specificare un [criterio di autorizzazione](xref:security/authorization/policies), usare un [Overload AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="e6200-125">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="e6200-126">Richiedi autorizzazione per accedere a una cartella di aree</span><span class="sxs-lookup"><span data-stu-id="e6200-126">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="e6200-127">Utilizzare la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convenzione per aggiungere un oggetto <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> a tutte le aree di una cartella nel percorso specificato:</span><span class="sxs-lookup"><span data-stu-id="e6200-127">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="e6200-128">Il percorso della cartella è il percorso della cartella relativa alla directory radice delle pagine per l'area specificata.</span><span class="sxs-lookup"><span data-stu-id="e6200-128">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="e6200-129">Il percorso della cartella per i file in *areas/ Identity /pages/manage/* , ad esempio, è */Manage*.</span><span class="sxs-lookup"><span data-stu-id="e6200-129">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="e6200-130">Per specificare un [criterio di autorizzazione](xref:security/authorization/policies), usare un [Overload AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="e6200-130">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="e6200-131">Consenti accesso anonimo a una pagina</span><span class="sxs-lookup"><span data-stu-id="e6200-131">Allow anonymous access to a page</span></span>

<span data-ttu-id="e6200-132">Utilizzare la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convenzione per aggiungere un oggetto <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a una pagina nel percorso specificato:</span><span class="sxs-lookup"><span data-stu-id="e6200-132">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="e6200-133">Il percorso specificato è il percorso del motore di visualizzazione, ovvero il Razor percorso relativo della radice delle pagine senza un'estensione e contenente solo barre.</span><span class="sxs-lookup"><span data-stu-id="e6200-133">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="e6200-134">Consenti accesso anonimo a una cartella di pagine</span><span class="sxs-lookup"><span data-stu-id="e6200-134">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="e6200-135">Utilizzare la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convenzione per aggiungere un oggetto <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a tutte le pagine di una cartella nel percorso specificato:</span><span class="sxs-lookup"><span data-stu-id="e6200-135">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="e6200-136">Il percorso specificato è il percorso del motore di visualizzazione, ovvero il Razor percorso relativo della radice delle pagine.</span><span class="sxs-lookup"><span data-stu-id="e6200-136">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="e6200-137">Nota sulla combinazione dell'accesso autorizzato e anonimo</span><span class="sxs-lookup"><span data-stu-id="e6200-137">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="e6200-138">È possibile specificare che una cartella di pagine richiede l'autorizzazione e quindi specificare che una pagina all'interno di tale cartella consenta l'accesso anonimo:</span><span class="sxs-lookup"><span data-stu-id="e6200-138">It's valid to specify that a folder of pages requires authorization and then specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="e6200-139">Il contrario, tuttavia, non è valido.</span><span class="sxs-lookup"><span data-stu-id="e6200-139">The reverse, however, isn't valid.</span></span> <span data-ttu-id="e6200-140">Non è possibile dichiarare una cartella di pagine per l'accesso anonimo e quindi specificare una pagina all'interno della cartella che richiede l'autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="e6200-140">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="e6200-141">La richiesta di autorizzazione nella pagina privata non riesce.</span><span class="sxs-lookup"><span data-stu-id="e6200-141">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="e6200-142">Quando <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> e <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> vengono applicati alla pagina, ha la <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> precedenza e controlla l'accesso.</span><span class="sxs-lookup"><span data-stu-id="e6200-142">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e6200-143">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="e6200-143">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e6200-144">Un modo per controllare l'accesso all' Razor app pagine consiste nell'usare le convenzioni di autorizzazione all'avvio.</span><span class="sxs-lookup"><span data-stu-id="e6200-144">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="e6200-145">Queste convenzioni consentono di autorizzare gli utenti e consentire agli utenti anonimi di accedere a singole pagine o cartelle di pagine.</span><span class="sxs-lookup"><span data-stu-id="e6200-145">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="e6200-146">Le convenzioni descritte in questo argomento applicano automaticamente i [filtri di autorizzazione](xref:mvc/controllers/filters#authorization-filters) per controllare l'accesso.</span><span class="sxs-lookup"><span data-stu-id="e6200-146">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="e6200-147">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e6200-147">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e6200-148">L'app di esempio usa l' [ cookie autenticazione Identity senza ASP.NET Core ](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="e6200-148">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="e6200-149">I concetti e gli esempi illustrati in questo argomento si applicano ugualmente alle app che usano ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="e6200-149">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="e6200-150">Per usare ASP.NET Core Identity , seguire le istruzioni riportate in <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="e6200-150">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="e6200-151">Richiedi autorizzazione per accedere a una pagina</span><span class="sxs-lookup"><span data-stu-id="e6200-151">Require authorization to access a page</span></span>

<span data-ttu-id="e6200-152">Utilizzare la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convenzione tramite <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> per aggiungere un oggetto <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> alla pagina nel percorso specificato:</span><span class="sxs-lookup"><span data-stu-id="e6200-152">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="e6200-153">Il percorso specificato è il percorso del motore di visualizzazione, ovvero il Razor percorso relativo della radice delle pagine senza un'estensione e contenente solo barre.</span><span class="sxs-lookup"><span data-stu-id="e6200-153">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="e6200-154">Per specificare un [criterio di autorizzazione](xref:security/authorization/policies), usare un [Overload AuthorizePage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="e6200-154">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="e6200-155">Un oggetto <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> può essere applicato a una classe del modello di pagina con l' `[Authorize]` attributo Filter.</span><span class="sxs-lookup"><span data-stu-id="e6200-155">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="e6200-156">Per ulteriori informazioni, vedere [autorizzazione filtro attributo](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="e6200-156">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="e6200-157">Richiedi autorizzazione per accedere a una cartella di pagine</span><span class="sxs-lookup"><span data-stu-id="e6200-157">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="e6200-158">Utilizzare la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convenzione tramite <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> per aggiungere un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> a tutte le pagine di una cartella nel percorso specificato:</span><span class="sxs-lookup"><span data-stu-id="e6200-158">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="e6200-159">Il percorso specificato è il percorso del motore di visualizzazione, ovvero il Razor percorso relativo della radice delle pagine.</span><span class="sxs-lookup"><span data-stu-id="e6200-159">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="e6200-160">Per specificare un [criterio di autorizzazione](xref:security/authorization/policies), usare un [Overload AuthorizeFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="e6200-160">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="e6200-161">Richiedi autorizzazione per accedere a una pagina di area</span><span class="sxs-lookup"><span data-stu-id="e6200-161">Require authorization to access an area page</span></span>

<span data-ttu-id="e6200-162">Utilizzare la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convenzione tramite <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> per aggiungere un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> alla pagina area nel percorso specificato:</span><span class="sxs-lookup"><span data-stu-id="e6200-162">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="e6200-163">Il nome della pagina è il percorso del file senza estensione rispetto alla directory radice delle pagine per l'area specificata.</span><span class="sxs-lookup"><span data-stu-id="e6200-163">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="e6200-164">Ad esempio, il nome della pagina per le *aree file/ Identity /pages/Manage/accounts.cshtml* è */Manage/accounts*.</span><span class="sxs-lookup"><span data-stu-id="e6200-164">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="e6200-165">Per specificare un [criterio di autorizzazione](xref:security/authorization/policies), usare un [Overload AuthorizeAreaPage](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="e6200-165">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="e6200-166">Richiedi autorizzazione per accedere a una cartella di aree</span><span class="sxs-lookup"><span data-stu-id="e6200-166">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="e6200-167">Utilizzare la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convenzione tramite <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> per aggiungere un <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> a tutte le aree di una cartella nel percorso specificato:</span><span class="sxs-lookup"><span data-stu-id="e6200-167">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="e6200-168">Il percorso della cartella è il percorso della cartella relativa alla directory radice delle pagine per l'area specificata.</span><span class="sxs-lookup"><span data-stu-id="e6200-168">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="e6200-169">Il percorso della cartella per i file in *areas/ Identity /pages/manage/* , ad esempio, è */Manage*.</span><span class="sxs-lookup"><span data-stu-id="e6200-169">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="e6200-170">Per specificare un [criterio di autorizzazione](xref:security/authorization/policies), usare un [Overload AuthorizeAreaFolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="e6200-170">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="e6200-171">Consenti accesso anonimo a una pagina</span><span class="sxs-lookup"><span data-stu-id="e6200-171">Allow anonymous access to a page</span></span>

<span data-ttu-id="e6200-172">Utilizzare la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convenzione tramite <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> per aggiungere un oggetto <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a una pagina nel percorso specificato:</span><span class="sxs-lookup"><span data-stu-id="e6200-172">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="e6200-173">Il percorso specificato è il percorso del motore di visualizzazione, ovvero il Razor percorso relativo della radice delle pagine senza un'estensione e contenente solo barre.</span><span class="sxs-lookup"><span data-stu-id="e6200-173">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="e6200-174">Consenti accesso anonimo a una cartella di pagine</span><span class="sxs-lookup"><span data-stu-id="e6200-174">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="e6200-175">Utilizzare la <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convenzione tramite <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> per aggiungere un <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> a tutte le pagine di una cartella nel percorso specificato:</span><span class="sxs-lookup"><span data-stu-id="e6200-175">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="e6200-176">Il percorso specificato è il percorso del motore di visualizzazione, ovvero il Razor percorso relativo della radice delle pagine.</span><span class="sxs-lookup"><span data-stu-id="e6200-176">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="e6200-177">Nota sulla combinazione dell'accesso autorizzato e anonimo</span><span class="sxs-lookup"><span data-stu-id="e6200-177">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="e6200-178">È possibile specificare che una cartella di pagine che richiede l'autorizzazione e che specifica che una pagina all'interno di tale cartella consenta l'accesso anonimo:</span><span class="sxs-lookup"><span data-stu-id="e6200-178">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="e6200-179">Il contrario, tuttavia, non è valido.</span><span class="sxs-lookup"><span data-stu-id="e6200-179">The reverse, however, isn't valid.</span></span> <span data-ttu-id="e6200-180">Non è possibile dichiarare una cartella di pagine per l'accesso anonimo e quindi specificare una pagina all'interno della cartella che richiede l'autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="e6200-180">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="e6200-181">La richiesta di autorizzazione nella pagina privata non riesce.</span><span class="sxs-lookup"><span data-stu-id="e6200-181">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="e6200-182">Quando <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> e <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> vengono applicati alla pagina, ha la <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> precedenza e controlla l'accesso.</span><span class="sxs-lookup"><span data-stu-id="e6200-182">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e6200-183">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="e6200-183">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
