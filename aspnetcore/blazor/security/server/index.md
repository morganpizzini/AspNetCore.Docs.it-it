---
title: Proteggere le Blazor Server app ASP.NET Core
author: guardrex
description: Informazioni su come proteggere Blazor Server le app come ASP.NET Core applicazioni.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: aa24def1a003a2c2608691e6168066c740f47205
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024626"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="f43a6-103">Proteggere le Blazor Server app ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f43a6-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="f43a6-104">Di [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f43a6-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f43a6-105">Blazor Server le app sono configurate per la sicurezza in modo analogo alle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f43a6-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="f43a6-106">Per ulteriori informazioni, vedere gli articoli in <xref:security/index> .</span><span class="sxs-lookup"><span data-stu-id="f43a6-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="f43a6-107">Gli argomenti di questa panoramica si applicano in modo specifico a Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="f43a6-107">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="f43a6-108">Blazor Server modello di progetto</span><span class="sxs-lookup"><span data-stu-id="f43a6-108">Blazor Server project template</span></span>

<span data-ttu-id="f43a6-109">Il Blazor Server modello di progetto può essere configurato per l'autenticazione quando viene creato il progetto.</span><span class="sxs-lookup"><span data-stu-id="f43a6-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f43a6-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f43a6-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f43a6-111">Seguire le istruzioni di Visual Studio in <xref:blazor/tooling> per creare un nuovo Blazor Server progetto con un meccanismo di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="f43a6-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="f43a6-112">Dopo aver scelto il modello **Blazor Server app** nella finestra di dialogo **Crea un nuovo ASP.NET Core applicazione Web** , selezionare **Cambia** in **autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="f43a6-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="f43a6-113">Viene visualizzata una finestra di dialogo che offre lo stesso set di meccanismi di autenticazione disponibili per altri progetti ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f43a6-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="f43a6-114">**Nessuna autenticazione**</span><span class="sxs-lookup"><span data-stu-id="f43a6-114">**No Authentication**</span></span>
* <span data-ttu-id="f43a6-115">**Account utente singoli**: è possibile archiviare gli account utente:</span><span class="sxs-lookup"><span data-stu-id="f43a6-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="f43a6-116">All'interno dell'app usando il [Identity](xref:security/authentication/identity) sistema di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f43a6-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="f43a6-117">Con [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="f43a6-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="f43a6-118">**Account aziendali o dell'Istituto di istruzione**</span><span class="sxs-lookup"><span data-stu-id="f43a6-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="f43a6-119">**Autenticazione di Windows**</span><span class="sxs-lookup"><span data-stu-id="f43a6-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f43a6-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f43a6-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f43a6-121">Seguire le indicazioni Visual Studio Code in <xref:blazor/tooling> per creare un nuovo Blazor Server progetto con un meccanismo di autenticazione:</span><span class="sxs-lookup"><span data-stu-id="f43a6-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="f43a6-122">I valori di autenticazione consentiti (`{AUTHENTICATION}`) sono indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="f43a6-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="f43a6-123">Meccanismo di autenticazione</span><span class="sxs-lookup"><span data-stu-id="f43a6-123">Authentication mechanism</span></span> | <span data-ttu-id="f43a6-124">Descrizione</span><span class="sxs-lookup"><span data-stu-id="f43a6-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="f43a6-125">`None` (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="f43a6-125">`None` (default)</span></span>         | <span data-ttu-id="f43a6-126">Nessuna autenticazione</span><span class="sxs-lookup"><span data-stu-id="f43a6-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="f43a6-127">Utenti archiviati nell'app con ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="f43a6-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="f43a6-128">Utenti archiviati in [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="f43a6-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="f43a6-129">Autenticazione organizzativa per un singolo tenant</span><span class="sxs-lookup"><span data-stu-id="f43a6-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="f43a6-130">Autenticazione organizzativa per più tenant</span><span class="sxs-lookup"><span data-stu-id="f43a6-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="f43a6-131">Autenticazione di Windows</span><span class="sxs-lookup"><span data-stu-id="f43a6-131">Windows Authentication</span></span> |

<span data-ttu-id="f43a6-132">Usando l' `-o|--output` opzione, il comando usa il valore fornito per il `{APP NAME}` segnaposto per:</span><span class="sxs-lookup"><span data-stu-id="f43a6-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="f43a6-133">Creare una cartella per il progetto.</span><span class="sxs-lookup"><span data-stu-id="f43a6-133">Create a folder for the project.</span></span>
* <span data-ttu-id="f43a6-134">Assegnare un nome al progetto.</span><span class="sxs-lookup"><span data-stu-id="f43a6-134">Name the project.</span></span>

<span data-ttu-id="f43a6-135">Per ulteriori informazioni, vedere il [`dotnet new`](/dotnet/core/tools/dotnet-new) comando nella Guida di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f43a6-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f43a6-136">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="f43a6-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f43a6-137">Seguire le indicazioni Visual Studio per Mac in <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="f43a6-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="f43a6-138">Nel passaggio **configurare la nuova Blazor Server app** selezionare **autenticazione singola (in-app) nell'elenco a** discesa **autenticazione** .</span><span class="sxs-lookup"><span data-stu-id="f43a6-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="f43a6-139">L'app viene creata per i singoli utenti archiviati nell'app con ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="f43a6-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f43a6-140">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="f43a6-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f43a6-141">Creare un nuovo Blazor Server progetto con un meccanismo di autenticazione usando il comando seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="f43a6-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="f43a6-142">I valori di autenticazione consentiti (`{AUTHENTICATION}`) sono indicati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="f43a6-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="f43a6-143">Meccanismo di autenticazione</span><span class="sxs-lookup"><span data-stu-id="f43a6-143">Authentication mechanism</span></span> | <span data-ttu-id="f43a6-144">Descrizione</span><span class="sxs-lookup"><span data-stu-id="f43a6-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="f43a6-145">`None` (impostazione predefinita)</span><span class="sxs-lookup"><span data-stu-id="f43a6-145">`None` (default)</span></span>         | <span data-ttu-id="f43a6-146">Nessuna autenticazione</span><span class="sxs-lookup"><span data-stu-id="f43a6-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="f43a6-147">Utenti archiviati nell'app con ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="f43a6-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="f43a6-148">Utenti archiviati in [Azure ad B2C](xref:security/authentication/azure-ad-b2c)</span><span class="sxs-lookup"><span data-stu-id="f43a6-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="f43a6-149">Autenticazione organizzativa per un singolo tenant</span><span class="sxs-lookup"><span data-stu-id="f43a6-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="f43a6-150">Autenticazione organizzativa per più tenant</span><span class="sxs-lookup"><span data-stu-id="f43a6-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="f43a6-151">Autenticazione di Windows</span><span class="sxs-lookup"><span data-stu-id="f43a6-151">Windows Authentication</span></span> |

<span data-ttu-id="f43a6-152">Usando l' `-o|--output` opzione, il comando usa il valore fornito per il `{APP NAME}` segnaposto per:</span><span class="sxs-lookup"><span data-stu-id="f43a6-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="f43a6-153">Creare una cartella per il progetto.</span><span class="sxs-lookup"><span data-stu-id="f43a6-153">Create a folder for the project.</span></span>
* <span data-ttu-id="f43a6-154">Assegnare un nome al progetto.</span><span class="sxs-lookup"><span data-stu-id="f43a6-154">Name the project.</span></span>

<span data-ttu-id="f43a6-155">Per altre informazioni:</span><span class="sxs-lookup"><span data-stu-id="f43a6-155">For more information:</span></span>

* <span data-ttu-id="f43a6-156">Vedere il [`dotnet new`](/dotnet/core/tools/dotnet-new) comando nella Guida di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f43a6-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="f43a6-157">Eseguire il comando della Guida per il Blazor Server modello ( `blazorserver` ) in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="f43a6-157">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="f43a6-158">Scaffold Identity</span><span class="sxs-lookup"><span data-stu-id="f43a6-158">Scaffold Identity</span></span>

<span data-ttu-id="f43a6-159">Impalcatura Identity in un Blazor Server progetto:</span><span class="sxs-lookup"><span data-stu-id="f43a6-159">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="f43a6-160">[Senza autorizzazione esistente](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span><span class="sxs-lookup"><span data-stu-id="f43a6-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="f43a6-161">[Con autorizzazione](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="f43a6-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f43a6-162">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="f43a6-162">Additional resources</span></span>

* [<span data-ttu-id="f43a6-163">Avvio rapido: Aggiungere l'accesso con Microsoft a un'app Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f43a6-163">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="f43a6-164">Avvio rapido: Proteggere un'API Web ASP.NET Core con Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="f43a6-164">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <span data-ttu-id="f43a6-165"><xref:host-and-deploy/proxy-load-balancer>: Include informazioni aggiuntive su:</span><span class="sxs-lookup"><span data-stu-id="f43a6-165"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="f43a6-166">Uso del middleware intestazioni con inoltro per mantenere le informazioni sullo schema HTTPS tra i server proxy e le reti interne.</span><span class="sxs-lookup"><span data-stu-id="f43a6-166">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="f43a6-167">Scenari e casi d'uso aggiuntivi, tra cui la configurazione manuale dello schema, la modifica del percorso della richiesta per il routing delle richieste corretto e l'inoltro dello schema di richiesta per i proxy inversi Linux e non IIS.</span><span class="sxs-lookup"><span data-stu-id="f43a6-167">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
