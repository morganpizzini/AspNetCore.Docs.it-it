---
title: Proteggere le Blazor Server app ASP.NET Core
author: guardrex
description: Informazioni su come proteggere Blazor Server le app come ASP.NET Core applicazioni.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: 69a24fc955a0f2fb524ec817eb50372052f538a1
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944256"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="3d607-103">Proteggere le Blazor Server app ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3d607-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="3d607-104">Di [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3d607-104">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor Server<span data-ttu-id="3d607-105">le app sono configurate per la sicurezza in modo analogo alle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3d607-105"> apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="3d607-106">Per ulteriori informazioni, vedere gli articoli in <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="3d607-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="3d607-107">Gli argomenti di questa panoramica si applicano in modo specifico a Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="3d607-107">Topics under this overview apply specifically to Blazor Server.</span></span> 

## <a name="blazor-server-project-template"></a>Blazor Server<span data-ttu-id="3d607-108">modello di progetto</span><span class="sxs-lookup"><span data-stu-id="3d607-108"> project template</span></span>

<span data-ttu-id="3d607-109">Il Blazor Server modello di progetto può essere configurato per l'autenticazione quando viene creato il progetto.</span><span class="sxs-lookup"><span data-stu-id="3d607-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3d607-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3d607-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3d607-111">Seguire le istruzioni di Visual Studio in <xref:blazor/tooling> per creare un nuovo Blazor Server progetto con un meccanismo di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="3d607-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="3d607-112">Dopo aver scelto il modello \*\* Blazor Server app\*\* nella finestra di dialogo **Crea un nuovo ASP.NET Core applicazione Web** , selezionare **Cambia** in **autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="3d607-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="3d607-113">Viene visualizzata una finestra di dialogo che offre lo stesso set di meccanismi di autenticazione disponibili per altri progetti ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="3d607-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="3d607-114">**Nessuna autenticazione**</span><span class="sxs-lookup"><span data-stu-id="3d607-114">**No Authentication**</span></span>
* <span data-ttu-id="3d607-115">**Account utente singoli**: è possibile archiviare gli account utente:</span><span class="sxs-lookup"><span data-stu-id="3d607-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="3d607-116">All'interno dell'app usando il [Identity](xref:security/authentication/identity) sistema di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3d607-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="3d607-117">Con [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="3d607-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="3d607-118">**Account aziendali o dell'Istituto di istruzione**</span><span class="sxs-lookup"><span data-stu-id="3d607-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="3d607-119">**Autenticazione di Windows**</span><span class="sxs-lookup"><span data-stu-id="3d607-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3d607-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3d607-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3d607-121">Seguire le indicazioni Visual Studio Code in <xref:blazor/tooling> per creare un nuovo Blazor Server progetto con un meccanismo di autenticazione:</span><span class="sxs-lookup"><span data-stu-id="3d607-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="3d607-122">I valori di autenticazione consentiti (`{AUTHENTICATION}`) sono indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="3d607-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="3d607-123">Meccanismo di autenticazione</span><span class="sxs-lookup"><span data-stu-id="3d607-123">Authentication mechanism</span></span> | <span data-ttu-id="3d607-124">Description</span><span class="sxs-lookup"><span data-stu-id="3d607-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="3d607-125">`None` (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="3d607-125">`None` (default)</span></span>         | <span data-ttu-id="3d607-126">Nessuna autenticazione</span><span class="sxs-lookup"><span data-stu-id="3d607-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="3d607-127">Utenti archiviati nell'app con ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="3d607-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="3d607-128">Utenti archiviati in [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="3d607-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="3d607-129">Autenticazione organizzativa per un singolo tenant</span><span class="sxs-lookup"><span data-stu-id="3d607-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="3d607-130">Autenticazione organizzativa per più tenant</span><span class="sxs-lookup"><span data-stu-id="3d607-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="3d607-131">Autenticazione di Windows</span><span class="sxs-lookup"><span data-stu-id="3d607-131">Windows Authentication</span></span> |

<span data-ttu-id="3d607-132">Usando l' `-o|--output` opzione, il comando usa il valore fornito per il `{APP NAME}` segnaposto per:</span><span class="sxs-lookup"><span data-stu-id="3d607-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="3d607-133">Creare una cartella per il progetto.</span><span class="sxs-lookup"><span data-stu-id="3d607-133">Create a folder for the project.</span></span>
* <span data-ttu-id="3d607-134">Assegnare un nome al progetto.</span><span class="sxs-lookup"><span data-stu-id="3d607-134">Name the project.</span></span>

<span data-ttu-id="3d607-135">Per ulteriori informazioni, vedere il [`dotnet new`](/dotnet/core/tools/dotnet-new) comando nella Guida di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3d607-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3d607-136">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="3d607-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3d607-137">Seguire le indicazioni Visual Studio per Mac in <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="3d607-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="3d607-138">Nel passaggio **configurare la nuova Blazor Server app** selezionare **autenticazione singola (in-app) nell'elenco a** discesa **autenticazione** .</span><span class="sxs-lookup"><span data-stu-id="3d607-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="3d607-139">L'app viene creata per i singoli utenti archiviati nell'app con ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="3d607-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3d607-140">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="3d607-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3d607-141">Creare un nuovo Blazor Server progetto con un meccanismo di autenticazione usando il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="3d607-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="3d607-142">I valori di autenticazione consentiti (`{AUTHENTICATION}`) sono indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="3d607-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="3d607-143">Meccanismo di autenticazione</span><span class="sxs-lookup"><span data-stu-id="3d607-143">Authentication mechanism</span></span> | <span data-ttu-id="3d607-144">Description</span><span class="sxs-lookup"><span data-stu-id="3d607-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="3d607-145">`None` (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="3d607-145">`None` (default)</span></span>         | <span data-ttu-id="3d607-146">Nessuna autenticazione</span><span class="sxs-lookup"><span data-stu-id="3d607-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="3d607-147">Utenti archiviati nell'app con ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="3d607-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="3d607-148">Utenti archiviati in [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="3d607-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="3d607-149">Autenticazione organizzativa per un singolo tenant</span><span class="sxs-lookup"><span data-stu-id="3d607-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="3d607-150">Autenticazione organizzativa per più tenant</span><span class="sxs-lookup"><span data-stu-id="3d607-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="3d607-151">Autenticazione di Windows</span><span class="sxs-lookup"><span data-stu-id="3d607-151">Windows Authentication</span></span> |

<span data-ttu-id="3d607-152">Usando l' `-o|--output` opzione, il comando usa il valore fornito per il `{APP NAME}` segnaposto per:</span><span class="sxs-lookup"><span data-stu-id="3d607-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="3d607-153">Creare una cartella per il progetto.</span><span class="sxs-lookup"><span data-stu-id="3d607-153">Create a folder for the project.</span></span>
* <span data-ttu-id="3d607-154">Assegnare un nome al progetto.</span><span class="sxs-lookup"><span data-stu-id="3d607-154">Name the project.</span></span>

<span data-ttu-id="3d607-155">Per ulteriori informazioni, vedere il [`dotnet new`](/dotnet/core/tools/dotnet-new) comando nella Guida di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3d607-155">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="scaffold-identity"></a><span data-ttu-id="3d607-156">ScaffoldIdentity</span><span class="sxs-lookup"><span data-stu-id="3d607-156">Scaffold Identity</span></span>

<span data-ttu-id="3d607-157">Impalcatura Identity in un Blazor Server progetto:</span><span class="sxs-lookup"><span data-stu-id="3d607-157">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="3d607-158">[Senza autorizzazione esistente](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="3d607-158">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="3d607-159">[Con autorizzazione](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="3d607-159">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>