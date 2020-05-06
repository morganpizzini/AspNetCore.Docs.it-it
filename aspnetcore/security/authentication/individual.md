---
title: Articoli basati su progetti ASP.NET Core creati con account utente singoli
author: rick-anderson
description: Scopri gli articoli in base ai progetti ASP.NET Core creati con singoli account utente.
ms.author: riande
ms.date: 12/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/individual
ms.openlocfilehash: 26f53b6452e307bbd0816c1a3604f38b04c6af15
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768650"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a><span data-ttu-id="de010-103">Articoli basati su progetti ASP.NET Core creati con account utente singoli</span><span class="sxs-lookup"><span data-stu-id="de010-103">Articles based on ASP.NET Core projects created with individual user accounts</span></span>

<span data-ttu-id="de010-104">ASP.NET Core identità è inclusa nei modelli di progetto di Visual Studio con l'opzione "singoli account utente".</span><span class="sxs-lookup"><span data-stu-id="de010-104">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="de010-105">I modelli di autenticazione sono disponibili in interfaccia della riga di comando di .NET Core `-au Individual`con:</span><span class="sxs-lookup"><span data-stu-id="de010-105">The authentication templates are available in .NET Core CLI with `-au Individual`:</span></span>

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

<span data-ttu-id="de010-106">Vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore/issues/5833) per l'autenticazione dell'API Web.</span><span class="sxs-lookup"><span data-stu-id="de010-106">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5833) for web API authentication.</span></span>

<a name="no"></a>

## <a name="no-authentication"></a><span data-ttu-id="de010-107">Nessuna autenticazione</span><span class="sxs-lookup"><span data-stu-id="de010-107">No Authentication</span></span>

<span data-ttu-id="de010-108">L'autenticazione viene specificata nella interfaccia della riga di comando di .NET Core con l' `-au` opzione.</span><span class="sxs-lookup"><span data-stu-id="de010-108">Authentication is specified in the .NET Core CLI with the `-au` option.</span></span> <span data-ttu-id="de010-109">In Visual Studio la finestra di dialogo **Modifica autenticazione** è disponibile per le nuove applicazioni Web.</span><span class="sxs-lookup"><span data-stu-id="de010-109">In Visual Studio, the **Change Authentication** dialog is available for new web applications.</span></span> <span data-ttu-id="de010-110">Il valore predefinito per le nuove app Web in Visual Studio **non è Authentication**.</span><span class="sxs-lookup"><span data-stu-id="de010-110">The default for new web apps in Visual Studio is **No Authentication**.</span></span>

<span data-ttu-id="de010-111">Progetti creati senza autenticazione:</span><span class="sxs-lookup"><span data-stu-id="de010-111">Projects created with no authentication:</span></span>

* <span data-ttu-id="de010-112">Non contenere pagine Web e interfaccia utente per l'accesso e la disconnessione.</span><span class="sxs-lookup"><span data-stu-id="de010-112">Don't contain web pages and UI to sign in and sign out.</span></span>
* <span data-ttu-id="de010-113">Non contiene codice di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="de010-113">Don't contain authentication code.</span></span>

<a name="win"></a>

## <a name="windows-authentication"></a><span data-ttu-id="de010-114">Autenticazione di Windows</span><span class="sxs-lookup"><span data-stu-id="de010-114">Windows Authentication</span></span>

<span data-ttu-id="de010-115">L'autenticazione di Windows viene specificata per le nuove app Web nella interfaccia della riga di comando di .NET Core `-au Windows` con l'opzione.</span><span class="sxs-lookup"><span data-stu-id="de010-115">Windows Authentication is specified for new web apps in the .NET Core CLI with the `-au Windows` option.</span></span> <span data-ttu-id="de010-116">In Visual Studio la finestra di dialogo **Cambia autenticazione** fornisce le opzioni di **autenticazione di Windows** .</span><span class="sxs-lookup"><span data-stu-id="de010-116">In Visual Studio, the **Change Authentication** dialog provides the **Windows Authentication** options.</span></span>

<span data-ttu-id="de010-117">Se è selezionata l'autenticazione di Windows, l'app viene configurata per l'uso del [modulo IIS](xref:host-and-deploy/iis/modules)per l'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="de010-117">If Windows Authentication is selected, the app is configured to use the [Windows Authentication IIS module](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="de010-118">L'autenticazione di Windows è destinata ai siti Web Intranet.</span><span class="sxs-lookup"><span data-stu-id="de010-118">Windows Authentication is intended for Intranet web sites.</span></span>

## <a name="dotnet-new-webapp-authentication-options"></a><span data-ttu-id="de010-119">opzioni di autenticazione New webapp DotNet</span><span class="sxs-lookup"><span data-stu-id="de010-119">dotnet new webapp authentication options</span></span>

<span data-ttu-id="de010-120">La tabella seguente illustra le opzioni di autenticazione disponibili per le nuove app Web:</span><span class="sxs-lookup"><span data-stu-id="de010-120">The following table shows the authentication options available for new web apps:</span></span>

| <span data-ttu-id="de010-121">Opzione</span><span class="sxs-lookup"><span data-stu-id="de010-121">Option</span></span> | <span data-ttu-id="de010-122">Tipo di autenticazione</span><span class="sxs-lookup"><span data-stu-id="de010-122">Type of authentication</span></span> | <span data-ttu-id="de010-123">Collegamento per altre informazioni</span><span class="sxs-lookup"><span data-stu-id="de010-123">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="de010-124">nessuno</span><span class="sxs-lookup"><span data-stu-id="de010-124">None</span></span>            |  <span data-ttu-id="de010-125">Nessuna autenticazione</span><span class="sxs-lookup"><span data-stu-id="de010-125">No authentication</span></span> | | 
| <span data-ttu-id="de010-126">Utenti</span><span class="sxs-lookup"><span data-stu-id="de010-126">Individual</span></span>      |  <span data-ttu-id="de010-127">Autenticazione singola</span><span class="sxs-lookup"><span data-stu-id="de010-127">Individual authentication</span></span> | <xref:security/authentication/identity>
| <span data-ttu-id="de010-128">IndividualB2C</span><span class="sxs-lookup"><span data-stu-id="de010-128">IndividualB2C</span></span>   |  <span data-ttu-id="de010-129">Autenticazione Single ospitata nel cloud con Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="de010-129">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="de010-130">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="de010-130">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="de010-131">SingleOrg</span><span class="sxs-lookup"><span data-stu-id="de010-131">SingleOrg</span></span>       |  <span data-ttu-id="de010-132">Autenticazione organizzativa per un singolo tenant</span><span class="sxs-lookup"><span data-stu-id="de010-132">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="de010-133">Azure AD</span><span class="sxs-lookup"><span data-stu-id="de010-133">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="de010-134">MultiOrg</span><span class="sxs-lookup"><span data-stu-id="de010-134">MultiOrg</span></span>        |  <span data-ttu-id="de010-135">Autenticazione organizzativa per più tenant</span><span class="sxs-lookup"><span data-stu-id="de010-135">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="de010-136">Azure AD</span><span class="sxs-lookup"><span data-stu-id="de010-136">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="de010-137">Windows</span><span class="sxs-lookup"><span data-stu-id="de010-137">Windows</span></span>         |  <span data-ttu-id="de010-138">Autenticazione di Windows</span><span class="sxs-lookup"><span data-stu-id="de010-138">Windows authentication</span></span> | [<span data-ttu-id="de010-139">Autenticazione di Windows</span><span class="sxs-lookup"><span data-stu-id="de010-139">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a><span data-ttu-id="de010-140">Nuove opzioni di autenticazione WebApp per Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de010-140">Visual Studio new webapp authentication options</span></span>

<span data-ttu-id="de010-141">La tabella seguente illustra le opzioni di autenticazione disponibili quando si crea una nuova app Web con Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="de010-141">The following table shows the authentication options available when creating a new web app with Visual Studio:</span></span>

| <span data-ttu-id="de010-142">Opzione</span><span class="sxs-lookup"><span data-stu-id="de010-142">Option</span></span> | <span data-ttu-id="de010-143">Tipo di autenticazione</span><span class="sxs-lookup"><span data-stu-id="de010-143">Type of authentication</span></span> | <span data-ttu-id="de010-144">Collegamento per altre informazioni</span><span class="sxs-lookup"><span data-stu-id="de010-144">Link for more information</span></span> |
 | ----------------- | ------------ | ---------- |
| <span data-ttu-id="de010-145">nessuno</span><span class="sxs-lookup"><span data-stu-id="de010-145">None</span></span>            |  <span data-ttu-id="de010-146">Nessuna autenticazione</span><span class="sxs-lookup"><span data-stu-id="de010-146">No authentication</span></span> | | 
| <span data-ttu-id="de010-147">Account utente singoli/archivia account utente in-app</span><span class="sxs-lookup"><span data-stu-id="de010-147">Individual User Accounts / Store user accounts in-app</span></span> |  <span data-ttu-id="de010-148">Autenticazione singola</span><span class="sxs-lookup"><span data-stu-id="de010-148">Individual authentication</span></span> | <xref:security/authentication/identity> |
| <span data-ttu-id="de010-149">Singoli account utente/connettersi a un archivio utente esistente nel cloud</span><span class="sxs-lookup"><span data-stu-id="de010-149">Individual User Accounts / Connect to an existing user store in the cloud</span></span> |  <span data-ttu-id="de010-150">Autenticazione Single ospitata nel cloud con Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="de010-150">Cloud-hosted individual authentication with Azure AD B2C</span></span> | [<span data-ttu-id="de010-151">Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="de010-151">Azure AD B2C</span></span>](/azure/active-directory-b2c/) |
| <span data-ttu-id="de010-152">Cloud aziendale o dell'Istituto di istruzione/organizzazione singola</span><span class="sxs-lookup"><span data-stu-id="de010-152">Work or School Cloud / Single Org</span></span>  |  <span data-ttu-id="de010-153">Autenticazione organizzativa per un singolo tenant</span><span class="sxs-lookup"><span data-stu-id="de010-153">Organizational authentication for a single tenant</span></span> | [<span data-ttu-id="de010-154">Azure AD</span><span class="sxs-lookup"><span data-stu-id="de010-154">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="de010-155">Cloud aziendale o dell'Istituto di istruzione/organizzazione multipla</span><span class="sxs-lookup"><span data-stu-id="de010-155">Work or School Cloud / Multiple Org</span></span> |  <span data-ttu-id="de010-156">Autenticazione organizzativa per più tenant</span><span class="sxs-lookup"><span data-stu-id="de010-156">Organizational authentication for multiple tenants</span></span> | [<span data-ttu-id="de010-157">Azure AD</span><span class="sxs-lookup"><span data-stu-id="de010-157">Azure AD</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| <span data-ttu-id="de010-158">Windows</span><span class="sxs-lookup"><span data-stu-id="de010-158">Windows</span></span>         |  <span data-ttu-id="de010-159">Autenticazione di Windows</span><span class="sxs-lookup"><span data-stu-id="de010-159">Windows authentication</span></span> | [<span data-ttu-id="de010-160">Autenticazione di Windows</span><span class="sxs-lookup"><span data-stu-id="de010-160">Windows Authentication</span></span>](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a><span data-ttu-id="de010-161">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="de010-161">Additional resources</span></span>

<span data-ttu-id="de010-162">Gli articoli seguenti illustrano come usare il codice generato in ASP.NET Core modelli che usano singoli account utente:</span><span class="sxs-lookup"><span data-stu-id="de010-162">The following articles show how to use the code generated in ASP.NET Core templates that use individual user accounts:</span></span>

* <span data-ttu-id="de010-163">[Account confirmation and password recovery in ASP.NET Core](xref:security/authentication/accconfirm) (Conferma dell'account e recupero della password in ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="de010-163">[Account confirmation and password recovery in ASP.NET Core](xref:security/authentication/accconfirm)</span></span>
* [<span data-ttu-id="de010-164">Creare un'app ASP.NET Core con i dati utente protetti dall'autorizzazione</span><span class="sxs-lookup"><span data-stu-id="de010-164">Create an ASP.NET Core app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
