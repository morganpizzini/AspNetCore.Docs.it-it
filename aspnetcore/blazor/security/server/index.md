---
title: Proteggere le Blazor app Server ASP.NET Core
author: guardrex
description: Informazioni su come proteggere Blazor le app Server come ASP.NET Core applicazioni.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: a8604ca6ea60386bb3c54c950205ee695d37c689
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103790"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="95ab2-103">Proteggere le Blazor app Server ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="95ab2-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="95ab2-104">Di [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="95ab2-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="blazor-server-project-template"></a>Blazor<span data-ttu-id="95ab2-105">Modello di progetto server</span><span class="sxs-lookup"><span data-stu-id="95ab2-105"> Server project template</span></span>

<span data-ttu-id="95ab2-106">Il Blazor modello di progetto server può essere configurato per l'autenticazione quando viene creato il progetto.</span><span class="sxs-lookup"><span data-stu-id="95ab2-106">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95ab2-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95ab2-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="95ab2-108"><xref:blazor/get-started>Per creare un nuovo Blazor progetto server con un meccanismo di autenticazione, seguire le istruzioni di Visual Studio disponibili nell'articolo.</span><span class="sxs-lookup"><span data-stu-id="95ab2-108">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="95ab2-109">Dopo aver scelto il modello \*\* Blazor app Server\*\* nella finestra di dialogo **Crea una nuova applicazione Web di ASP.NET Core** , selezionare **Cambia** in **autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="95ab2-109">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="95ab2-110">Viene visualizzata una finestra di dialogo che offre lo stesso set di meccanismi di autenticazione disponibili per altri progetti ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="95ab2-110">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="95ab2-111">**Nessuna autenticazione**</span><span class="sxs-lookup"><span data-stu-id="95ab2-111">**No Authentication**</span></span>
* <span data-ttu-id="95ab2-112">**Account utente singoli**: è possibile archiviare gli account utente:</span><span class="sxs-lookup"><span data-stu-id="95ab2-112">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="95ab2-113">All'interno dell'app usando il [Identity](xref:security/authentication/identity) sistema di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="95ab2-113">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="95ab2-114">Con [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="95ab2-114">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="95ab2-115">**Account aziendali o dell'Istituto di istruzione**</span><span class="sxs-lookup"><span data-stu-id="95ab2-115">**Work or School Accounts**</span></span>
* <span data-ttu-id="95ab2-116">**Autenticazione di Windows**</span><span class="sxs-lookup"><span data-stu-id="95ab2-116">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="95ab2-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95ab2-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="95ab2-118">Seguire le indicazioni Visual Studio Code nell' <xref:blazor/get-started> articolo per creare un nuovo Blazor progetto server con un meccanismo di autenticazione:</span><span class="sxs-lookup"><span data-stu-id="95ab2-118">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="95ab2-119">I valori di autenticazione consentiti (`{AUTHENTICATION}`) sono indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="95ab2-119">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="95ab2-120">Meccanismo di autenticazione</span><span class="sxs-lookup"><span data-stu-id="95ab2-120">Authentication mechanism</span></span> | <span data-ttu-id="95ab2-121">Descrizione</span><span class="sxs-lookup"><span data-stu-id="95ab2-121">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="95ab2-122">`None` (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="95ab2-122">`None` (default)</span></span>         | <span data-ttu-id="95ab2-123">Nessuna autenticazione</span><span class="sxs-lookup"><span data-stu-id="95ab2-123">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="95ab2-124">Utenti archiviati nell'app con ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="95ab2-124">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="95ab2-125">Utenti archiviati in [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="95ab2-125">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="95ab2-126">Autenticazione organizzativa per un singolo tenant</span><span class="sxs-lookup"><span data-stu-id="95ab2-126">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="95ab2-127">Autenticazione organizzativa per più tenant</span><span class="sxs-lookup"><span data-stu-id="95ab2-127">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="95ab2-128">Autenticazione di Windows</span><span class="sxs-lookup"><span data-stu-id="95ab2-128">Windows Authentication</span></span> |

<span data-ttu-id="95ab2-129">Usando l' `-o|--output` opzione, il comando usa il valore fornito per il `{APP NAME}` segnaposto per:</span><span class="sxs-lookup"><span data-stu-id="95ab2-129">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="95ab2-130">Creare una cartella per il progetto.</span><span class="sxs-lookup"><span data-stu-id="95ab2-130">Create a folder for the project.</span></span>
* <span data-ttu-id="95ab2-131">Assegnare un nome al progetto.</span><span class="sxs-lookup"><span data-stu-id="95ab2-131">Name the project.</span></span>

<span data-ttu-id="95ab2-132">Per altre informazioni, vedere il comando [dotnet new](/dotnet/core/tools/dotnet-new) nella guida per .NET Core.</span><span class="sxs-lookup"><span data-stu-id="95ab2-132">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="95ab2-133">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="95ab2-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="95ab2-134">Seguire le indicazioni Visual Studio per Mac nell' <xref:blazor/get-started> articolo.</span><span class="sxs-lookup"><span data-stu-id="95ab2-134">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="95ab2-135">Nel passaggio **Configura l' Blazor app del nuovo server** selezionare **autenticazione singola (in-app) nell'elenco a** discesa **autenticazione** .</span><span class="sxs-lookup"><span data-stu-id="95ab2-135">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="95ab2-136">L'app viene creata per i singoli utenti archiviati nell'app con ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="95ab2-136">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="95ab2-137">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="95ab2-137">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="95ab2-138">Seguire le indicazioni interfaccia della riga di comando di .NET Core nell' <xref:blazor/get-started> articolo per creare un nuovo Blazor progetto server con un meccanismo di autenticazione:</span><span class="sxs-lookup"><span data-stu-id="95ab2-138">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="95ab2-139">I valori di autenticazione consentiti (`{AUTHENTICATION}`) sono indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="95ab2-139">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="95ab2-140">Meccanismo di autenticazione</span><span class="sxs-lookup"><span data-stu-id="95ab2-140">Authentication mechanism</span></span> | <span data-ttu-id="95ab2-141">Descrizione</span><span class="sxs-lookup"><span data-stu-id="95ab2-141">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="95ab2-142">`None` (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="95ab2-142">`None` (default)</span></span>         | <span data-ttu-id="95ab2-143">Nessuna autenticazione</span><span class="sxs-lookup"><span data-stu-id="95ab2-143">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="95ab2-144">Utenti archiviati nell'app con ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="95ab2-144">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="95ab2-145">Utenti archiviati in [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="95ab2-145">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="95ab2-146">Autenticazione organizzativa per un singolo tenant</span><span class="sxs-lookup"><span data-stu-id="95ab2-146">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="95ab2-147">Autenticazione organizzativa per più tenant</span><span class="sxs-lookup"><span data-stu-id="95ab2-147">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="95ab2-148">Autenticazione di Windows</span><span class="sxs-lookup"><span data-stu-id="95ab2-148">Windows Authentication</span></span> |

<span data-ttu-id="95ab2-149">Usando l' `-o|--output` opzione, il comando usa il valore fornito per il `{APP NAME}` segnaposto per:</span><span class="sxs-lookup"><span data-stu-id="95ab2-149">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="95ab2-150">Creare una cartella per il progetto.</span><span class="sxs-lookup"><span data-stu-id="95ab2-150">Create a folder for the project.</span></span>
* <span data-ttu-id="95ab2-151">Assegnare un nome al progetto.</span><span class="sxs-lookup"><span data-stu-id="95ab2-151">Name the project.</span></span>

<span data-ttu-id="95ab2-152">Per altre informazioni, vedere il comando [dotnet new](/dotnet/core/tools/dotnet-new) nella guida per .NET Core.</span><span class="sxs-lookup"><span data-stu-id="95ab2-152">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="secure-an-existing-app"></a><span data-ttu-id="95ab2-153">Proteggere un'app esistente</span><span class="sxs-lookup"><span data-stu-id="95ab2-153">Secure an existing app</span></span>

Blazor<span data-ttu-id="95ab2-154">Le app Server sono configurate per la sicurezza in modo analogo alle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="95ab2-154"> Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="95ab2-155">Per ulteriori informazioni, vedere gli articoli in <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="95ab2-155">For more information, see the articles under <xref:security/index>.</span></span>

## <a name="scaffold-identity"></a><span data-ttu-id="95ab2-156">ScaffoldIdentity</span><span class="sxs-lookup"><span data-stu-id="95ab2-156">Scaffold Identity</span></span>

<span data-ttu-id="95ab2-157">Impalcatura Identity in un Blazor progetto server:</span><span class="sxs-lookup"><span data-stu-id="95ab2-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="95ab2-158">[Senza autorizzazione esistente](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="95ab2-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="95ab2-159">[Con autorizzazione](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="95ab2-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>
