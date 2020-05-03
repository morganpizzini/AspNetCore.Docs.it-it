---
title: Proteggere le Blazor app server ASP.NET Core
author: guardrex
description: Informazioni su come proteggere Blazor le app Server come ASP.NET Core applicazioni.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: 324b3e8ed2d931c81bb41381caa469b0ec2f2fda
ms.sourcegitcommit: c19e388c83c981232e6f128d97440262adfe06e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2020
ms.locfileid: "82727805"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="47d7e-103">Proteggere le app del server ASP.NET Core Blazer</span><span class="sxs-lookup"><span data-stu-id="47d7e-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="47d7e-104">Di [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="47d7e-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="47d7e-105">Modello di progetto server Blazer</span><span class="sxs-lookup"><span data-stu-id="47d7e-105">Blazor Server project template</span></span>

<span data-ttu-id="47d7e-106">Il modello di progetto server blazer può essere configurato per l'autenticazione quando viene creato il progetto.</span><span class="sxs-lookup"><span data-stu-id="47d7e-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="47d7e-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47d7e-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="47d7e-108">Per creare un nuovo progetto server Blazer con un meccanismo di autenticazione, seguire le istruzioni di Visual Studio disponibili nell' <xref:blazor/get-started> articolo.</span><span class="sxs-lookup"><span data-stu-id="47d7e-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="47d7e-109">Dopo aver scelto il modello **App server Blazor** nella finestra di dialogo **Crea una nuova applicazione Web ASP.NET Core**, selezionare **Modifica** in \*\*Autenticazione \*\*.</span><span class="sxs-lookup"><span data-stu-id="47d7e-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="47d7e-110">Viene visualizzata una finestra di dialogo che offre lo stesso set di meccanismi di autenticazione disponibili per altri progetti ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="47d7e-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="47d7e-111">**Nessuna autenticazione**</span><span class="sxs-lookup"><span data-stu-id="47d7e-111">**No Authentication**</span></span>
* <span data-ttu-id="47d7e-112">**Account utente individuali** &ndash; Gli account utente possono essere archiviati:</span><span class="sxs-lookup"><span data-stu-id="47d7e-112">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="47d7e-113">All'interno dell'app usando il sistema di [identità](xref:security/authentication/identity) di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="47d7e-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="47d7e-114">Con [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="47d7e-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="47d7e-115">**Account aziendali o dell'Istituto di istruzione**</span><span class="sxs-lookup"><span data-stu-id="47d7e-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="47d7e-116">**Autenticazione di Windows**</span><span class="sxs-lookup"><span data-stu-id="47d7e-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="47d7e-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="47d7e-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="47d7e-118">Seguire le indicazioni Visual Studio Code nell' <xref:blazor/get-started> articolo per creare un nuovo progetto server Blazer con un meccanismo di autenticazione:</span><span class="sxs-lookup"><span data-stu-id="47d7e-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="47d7e-119">I valori di autenticazione consentiti (`{AUTHENTICATION}`) sono indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="47d7e-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="47d7e-120">Meccanismo di autenticazione</span><span class="sxs-lookup"><span data-stu-id="47d7e-120">Authentication mechanism</span></span> | <span data-ttu-id="47d7e-121">Descrizione</span><span class="sxs-lookup"><span data-stu-id="47d7e-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="47d7e-122">`None` (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="47d7e-122">`None` (default)</span></span>         | <span data-ttu-id="47d7e-123">Nessuna autenticazione</span><span class="sxs-lookup"><span data-stu-id="47d7e-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="47d7e-124">Utenti archiviati nell'app con ASP.NET Core identità</span><span class="sxs-lookup"><span data-stu-id="47d7e-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="47d7e-125">Utenti archiviati in [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="47d7e-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="47d7e-126">Autenticazione organizzativa per un singolo tenant</span><span class="sxs-lookup"><span data-stu-id="47d7e-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="47d7e-127">Autenticazione organizzativa per più tenant</span><span class="sxs-lookup"><span data-stu-id="47d7e-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="47d7e-128">Autenticazione di Windows</span><span class="sxs-lookup"><span data-stu-id="47d7e-128">Windows Authentication</span></span> |

<span data-ttu-id="47d7e-129">Usando l' `-o|--output` opzione, il comando usa il valore fornito per il `{APP NAME}` segnaposto per:</span><span class="sxs-lookup"><span data-stu-id="47d7e-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="47d7e-130">Creare una cartella per il progetto.</span><span class="sxs-lookup"><span data-stu-id="47d7e-130">Create a folder for the project.</span></span>
* <span data-ttu-id="47d7e-131">Assegnare un nome al progetto.</span><span class="sxs-lookup"><span data-stu-id="47d7e-131">Name the project.</span></span>

<span data-ttu-id="47d7e-132">Per altre informazioni, vedere il comando [dotnet new](/dotnet/core/tools/dotnet-new) nella guida per .NET Core.</span><span class="sxs-lookup"><span data-stu-id="47d7e-132">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="47d7e-133">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="47d7e-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="47d7e-134">Seguire le indicazioni Visual Studio per Mac nell' <xref:blazor/get-started> articolo.</span><span class="sxs-lookup"><span data-stu-id="47d7e-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="47d7e-135">Nel passaggio **configurare la nuova app del server Blazer** selezionare **autenticazione singola (in-app) nell'elenco a** discesa **autenticazione** .</span><span class="sxs-lookup"><span data-stu-id="47d7e-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="47d7e-136">L'app viene creata per i singoli utenti archiviati nell'app con ASP.NET Core identità.</span><span class="sxs-lookup"><span data-stu-id="47d7e-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="47d7e-137">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="47d7e-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="47d7e-138">Seguire le indicazioni interfaccia della riga di comando di .NET Core nell' <xref:blazor/get-started> articolo per creare un nuovo progetto server Blazer con un meccanismo di autenticazione:</span><span class="sxs-lookup"><span data-stu-id="47d7e-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="47d7e-139">I valori di autenticazione consentiti (`{AUTHENTICATION}`) sono indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="47d7e-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="47d7e-140">Meccanismo di autenticazione</span><span class="sxs-lookup"><span data-stu-id="47d7e-140">Authentication mechanism</span></span> | <span data-ttu-id="47d7e-141">Descrizione</span><span class="sxs-lookup"><span data-stu-id="47d7e-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="47d7e-142">`None` (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="47d7e-142">`None` (default)</span></span>         | <span data-ttu-id="47d7e-143">Nessuna autenticazione</span><span class="sxs-lookup"><span data-stu-id="47d7e-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="47d7e-144">Utenti archiviati nell'app con ASP.NET Core identità</span><span class="sxs-lookup"><span data-stu-id="47d7e-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="47d7e-145">Utenti archiviati in [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="47d7e-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="47d7e-146">Autenticazione organizzativa per un singolo tenant</span><span class="sxs-lookup"><span data-stu-id="47d7e-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="47d7e-147">Autenticazione organizzativa per più tenant</span><span class="sxs-lookup"><span data-stu-id="47d7e-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="47d7e-148">Autenticazione di Windows</span><span class="sxs-lookup"><span data-stu-id="47d7e-148">Windows Authentication</span></span> |

<span data-ttu-id="47d7e-149">Usando l' `-o|--output` opzione, il comando usa il valore fornito per il `{APP NAME}` segnaposto per:</span><span class="sxs-lookup"><span data-stu-id="47d7e-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="47d7e-150">Creare una cartella per il progetto.</span><span class="sxs-lookup"><span data-stu-id="47d7e-150">Create a folder for the project.</span></span>
* <span data-ttu-id="47d7e-151">Assegnare un nome al progetto.</span><span class="sxs-lookup"><span data-stu-id="47d7e-151">Name the project.</span></span>

<span data-ttu-id="47d7e-152">Per altre informazioni, vedere il comando [dotnet new](/dotnet/core/tools/dotnet-new) nella guida per .NET Core.</span><span class="sxs-lookup"><span data-stu-id="47d7e-152">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="secure-an-existing-app"></a><span data-ttu-id="47d7e-153">Proteggere un'app esistente</span><span class="sxs-lookup"><span data-stu-id="47d7e-153">Secure an existing app</span></span>

Blazor<span data-ttu-id="47d7e-154">Le app Server sono configurate per la sicurezza in modo analogo alle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="47d7e-154"> Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="47d7e-155">Per ulteriori informazioni, vedere gli articoli in <xref:security/index>.</span><span class="sxs-lookup"><span data-stu-id="47d7e-155">For more information, see the articles under <xref:security/index>.</span></span>
