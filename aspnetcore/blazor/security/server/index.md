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
ms.openlocfilehash: 2811e08fd2f6c66112ffa0bb40f474158f4c7a59
ms.sourcegitcommit: 5e462c3328c70f95969d02adce9c71592049f54c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85292685"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="bf7a0-103">Proteggere le Blazor app Server ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf7a0-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="bf7a0-104">Di [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bf7a0-104">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor<span data-ttu-id="bf7a0-105">Le app Server sono configurate per la sicurezza in modo analogo alle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bf7a0-105"> Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="bf7a0-106">Per ulteriori informazioni, vedere gli articoli in <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="bf7a0-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="bf7a0-107">Gli argomenti di questa panoramica si applicano in modo specifico al Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="bf7a0-107">Topics under this overview apply specifically to Blazor Server.</span></span> 

## <a name="blazor-server-project-template"></a>Blazor<span data-ttu-id="bf7a0-108">Modello di progetto server</span><span class="sxs-lookup"><span data-stu-id="bf7a0-108"> Server project template</span></span>

<span data-ttu-id="bf7a0-109">Il Blazor modello di progetto server può essere configurato per l'autenticazione quando viene creato il progetto.</span><span class="sxs-lookup"><span data-stu-id="bf7a0-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bf7a0-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf7a0-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bf7a0-111"><xref:blazor/get-started>Per creare un nuovo Blazor progetto server con un meccanismo di autenticazione, seguire le istruzioni di Visual Studio disponibili nell'articolo.</span><span class="sxs-lookup"><span data-stu-id="bf7a0-111">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="bf7a0-112">Dopo aver scelto il modello \*\* Blazor app Server\*\* nella finestra di dialogo **Crea una nuova applicazione Web di ASP.NET Core** , selezionare **Cambia** in **autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="bf7a0-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="bf7a0-113">Viene visualizzata una finestra di dialogo che offre lo stesso set di meccanismi di autenticazione disponibili per altri progetti ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="bf7a0-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="bf7a0-114">**Nessuna autenticazione**</span><span class="sxs-lookup"><span data-stu-id="bf7a0-114">**No Authentication**</span></span>
* <span data-ttu-id="bf7a0-115">**Account utente singoli**: è possibile archiviare gli account utente:</span><span class="sxs-lookup"><span data-stu-id="bf7a0-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="bf7a0-116">All'interno dell'app usando il [Identity](xref:security/authentication/identity) sistema di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bf7a0-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="bf7a0-117">Con [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="bf7a0-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="bf7a0-118">**Account aziendali o dell'Istituto di istruzione**</span><span class="sxs-lookup"><span data-stu-id="bf7a0-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="bf7a0-119">**Autenticazione di Windows**</span><span class="sxs-lookup"><span data-stu-id="bf7a0-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bf7a0-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bf7a0-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bf7a0-121">Seguire le indicazioni Visual Studio Code nell' <xref:blazor/get-started> articolo per creare un nuovo Blazor progetto server con un meccanismo di autenticazione:</span><span class="sxs-lookup"><span data-stu-id="bf7a0-121">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="bf7a0-122">I valori di autenticazione consentiti (`{AUTHENTICATION}`) sono indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="bf7a0-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="bf7a0-123">Meccanismo di autenticazione</span><span class="sxs-lookup"><span data-stu-id="bf7a0-123">Authentication mechanism</span></span> | <span data-ttu-id="bf7a0-124">Descrizione</span><span class="sxs-lookup"><span data-stu-id="bf7a0-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="bf7a0-125">`None` (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="bf7a0-125">`None` (default)</span></span>         | <span data-ttu-id="bf7a0-126">Nessuna autenticazione</span><span class="sxs-lookup"><span data-stu-id="bf7a0-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="bf7a0-127">Utenti archiviati nell'app con ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="bf7a0-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="bf7a0-128">Utenti archiviati in [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="bf7a0-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="bf7a0-129">Autenticazione organizzativa per un singolo tenant</span><span class="sxs-lookup"><span data-stu-id="bf7a0-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="bf7a0-130">Autenticazione organizzativa per più tenant</span><span class="sxs-lookup"><span data-stu-id="bf7a0-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="bf7a0-131">Autenticazione di Windows</span><span class="sxs-lookup"><span data-stu-id="bf7a0-131">Windows Authentication</span></span> |

<span data-ttu-id="bf7a0-132">Usando l' `-o|--output` opzione, il comando usa il valore fornito per il `{APP NAME}` segnaposto per:</span><span class="sxs-lookup"><span data-stu-id="bf7a0-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="bf7a0-133">Creare una cartella per il progetto.</span><span class="sxs-lookup"><span data-stu-id="bf7a0-133">Create a folder for the project.</span></span>
* <span data-ttu-id="bf7a0-134">Assegnare un nome al progetto.</span><span class="sxs-lookup"><span data-stu-id="bf7a0-134">Name the project.</span></span>

<span data-ttu-id="bf7a0-135">Per ulteriori informazioni, vedere il [`dotnet new`](/dotnet/core/tools/dotnet-new) comando nella Guida di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="bf7a0-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bf7a0-136">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="bf7a0-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="bf7a0-137">Seguire le indicazioni Visual Studio per Mac nell' <xref:blazor/get-started> articolo.</span><span class="sxs-lookup"><span data-stu-id="bf7a0-137">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="bf7a0-138">Nel passaggio **Configura l' Blazor app del nuovo server** selezionare **autenticazione singola (in-app) nell'elenco a** discesa **autenticazione** .</span><span class="sxs-lookup"><span data-stu-id="bf7a0-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="bf7a0-139">L'app viene creata per i singoli utenti archiviati nell'app con ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="bf7a0-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="bf7a0-140">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="bf7a0-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="bf7a0-141">Seguire le indicazioni interfaccia della riga di comando di .NET Core nell' <xref:blazor/get-started> articolo per creare un nuovo Blazor progetto server con un meccanismo di autenticazione:</span><span class="sxs-lookup"><span data-stu-id="bf7a0-141">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="bf7a0-142">I valori di autenticazione consentiti (`{AUTHENTICATION}`) sono indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="bf7a0-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="bf7a0-143">Meccanismo di autenticazione</span><span class="sxs-lookup"><span data-stu-id="bf7a0-143">Authentication mechanism</span></span> | <span data-ttu-id="bf7a0-144">Descrizione</span><span class="sxs-lookup"><span data-stu-id="bf7a0-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="bf7a0-145">`None` (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="bf7a0-145">`None` (default)</span></span>         | <span data-ttu-id="bf7a0-146">Nessuna autenticazione</span><span class="sxs-lookup"><span data-stu-id="bf7a0-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="bf7a0-147">Utenti archiviati nell'app con ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="bf7a0-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="bf7a0-148">Utenti archiviati in [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="bf7a0-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="bf7a0-149">Autenticazione organizzativa per un singolo tenant</span><span class="sxs-lookup"><span data-stu-id="bf7a0-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="bf7a0-150">Autenticazione organizzativa per più tenant</span><span class="sxs-lookup"><span data-stu-id="bf7a0-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="bf7a0-151">Autenticazione di Windows</span><span class="sxs-lookup"><span data-stu-id="bf7a0-151">Windows Authentication</span></span> |

<span data-ttu-id="bf7a0-152">Usando l' `-o|--output` opzione, il comando usa il valore fornito per il `{APP NAME}` segnaposto per:</span><span class="sxs-lookup"><span data-stu-id="bf7a0-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="bf7a0-153">Creare una cartella per il progetto.</span><span class="sxs-lookup"><span data-stu-id="bf7a0-153">Create a folder for the project.</span></span>
* <span data-ttu-id="bf7a0-154">Assegnare un nome al progetto.</span><span class="sxs-lookup"><span data-stu-id="bf7a0-154">Name the project.</span></span>

<span data-ttu-id="bf7a0-155">Per ulteriori informazioni, vedere il [`dotnet new`](/dotnet/core/tools/dotnet-new) comando nella Guida di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="bf7a0-155">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="scaffold-identity"></a><span data-ttu-id="bf7a0-156">ScaffoldIdentity</span><span class="sxs-lookup"><span data-stu-id="bf7a0-156">Scaffold Identity</span></span>

<span data-ttu-id="bf7a0-157">Impalcatura Identity in un Blazor progetto server:</span><span class="sxs-lookup"><span data-stu-id="bf7a0-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="bf7a0-158">[Senza autorizzazione esistente](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="bf7a0-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="bf7a0-159">[Con autorizzazione](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="bf7a0-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>
