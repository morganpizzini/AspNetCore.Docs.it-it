---
title: Autenticazione cloud con Azure Active Directory B2C in ASP.NET Core
author: camsoper
description: Scopri come configurare l'autenticazione Azure Active Directory B2C con ASP.NET Core.
ms.author: casoper
ms.custom: mvc
ms.date: 01/21/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: 4933203b8bdd8f653268c1df7ff83b8e9423341f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405068"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a><span data-ttu-id="6946d-103">Autenticazione cloud con Azure Active Directory B2C in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6946d-103">Cloud authentication with Azure Active Directory B2C in ASP.NET Core</span></span>

<span data-ttu-id="6946d-104">Di [Cam Soper](https://twitter.com/camsoper)</span><span class="sxs-lookup"><span data-stu-id="6946d-104">By [Cam Soper](https://twitter.com/camsoper)</span></span>

<span data-ttu-id="6946d-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure ad B2C) è una soluzione di gestione delle identità cloud per app Web e per dispositivi mobili.</span><span class="sxs-lookup"><span data-stu-id="6946d-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) is a cloud identity management solution for web and mobile apps.</span></span> <span data-ttu-id="6946d-106">Il servizio fornisce l'autenticazione per le app ospitate nel cloud e in locale.</span><span class="sxs-lookup"><span data-stu-id="6946d-106">The service provides authentication for apps hosted in the cloud and on-premises.</span></span> <span data-ttu-id="6946d-107">I tipi di autenticazione includono singoli account, account social network e account aziendali federati.</span><span class="sxs-lookup"><span data-stu-id="6946d-107">Authentication types include individual accounts, social network accounts, and federated enterprise accounts.</span></span> <span data-ttu-id="6946d-108">Inoltre, Azure AD B2C possibile fornire l'autenticazione a più fattori con la configurazione minima.</span><span class="sxs-lookup"><span data-stu-id="6946d-108">Additionally, Azure AD B2C can provide multi-factor authentication with minimal configuration.</span></span>

> [!TIP]
> <span data-ttu-id="6946d-109">Azure Active Directory (Azure AD) e Azure AD B2C sono offerte di prodotti separate.</span><span class="sxs-lookup"><span data-stu-id="6946d-109">Azure Active Directory (Azure AD) and Azure AD B2C are separate product offerings.</span></span> <span data-ttu-id="6946d-110">Un tenant di Azure AD rappresenta un'organizzazione, mentre un tenant Azure AD B2C rappresenta una raccolta di identità da usare con le applicazioni di relying party.</span><span class="sxs-lookup"><span data-stu-id="6946d-110">An Azure AD tenant represents an organization, while an Azure AD B2C tenant represents a collection of identities to be used with relying party applications.</span></span> <span data-ttu-id="6946d-111">Per altre informazioni, vedere [Azure ad B2C: domande frequenti (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span><span class="sxs-lookup"><span data-stu-id="6946d-111">To learn more, see [Azure AD B2C: Frequently asked questions (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span></span>

<span data-ttu-id="6946d-112">In questa esercitazione si apprenderà come:</span><span class="sxs-lookup"><span data-stu-id="6946d-112">In this tutorial, learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6946d-113">Creare un tenant di Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="6946d-113">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="6946d-114">Registrare un'app in Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="6946d-114">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="6946d-115">Usare Visual Studio per creare un'app Web ASP.NET Core configurata per l'uso del tenant Azure AD B2C per l'autenticazione</span><span class="sxs-lookup"><span data-stu-id="6946d-115">Use Visual Studio to create an ASP.NET Core web app configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="6946d-116">Configurare i criteri che controllano il comportamento del tenant Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="6946d-116">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6946d-117">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="6946d-117">Prerequisites</span></span>

<span data-ttu-id="6946d-118">Per questa procedura dettagliata sono necessari gli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="6946d-118">The following are required for this walkthrough:</span></span>

* [<span data-ttu-id="6946d-119">iscriversi a Microsoft Azure</span><span class="sxs-lookup"><span data-stu-id="6946d-119">Microsoft Azure subscription</span></span>](https://azure.microsoft.com/free/dotnet/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [<span data-ttu-id="6946d-120">Visual Studio 2019</span><span class="sxs-lookup"><span data-stu-id="6946d-120">Visual Studio 2019</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a><span data-ttu-id="6946d-121">Creare il tenant di Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="6946d-121">Create the Azure Active Directory B2C tenant</span></span>

<span data-ttu-id="6946d-122">Creare un tenant [di Azure Active Directory B2C come descritto nella documentazione](/azure/active-directory-b2c/active-directory-b2c-get-started).</span><span class="sxs-lookup"><span data-stu-id="6946d-122">Create an Azure Active Directory B2C tenant [as described in the documentation](/azure/active-directory-b2c/active-directory-b2c-get-started).</span></span> <span data-ttu-id="6946d-123">Quando richiesto, l'associazione del tenant a una sottoscrizione di Azure è facoltativa per questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="6946d-123">When prompted, associating the tenant with an Azure subscription is optional for this tutorial.</span></span>

## <a name="register-the-app-in-azure-ad-b2c"></a><span data-ttu-id="6946d-124">Registrare l'app in Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="6946d-124">Register the app in Azure AD B2C</span></span>

<span data-ttu-id="6946d-125">Nel tenant di Azure AD B2C appena creato registrare l'app seguendo [la procedura descritta nella documentazione](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) nella sezione **registrare un'app Web** .</span><span class="sxs-lookup"><span data-stu-id="6946d-125">In the newly created Azure AD B2C tenant, register your app using [the steps in the documentation](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) under the **Register a web app** section.</span></span> <span data-ttu-id="6946d-126">Arrestare la sezione **creare un segreto client dell'app Web** .</span><span class="sxs-lookup"><span data-stu-id="6946d-126">Stop at the **Create a web app client secret** section.</span></span> <span data-ttu-id="6946d-127">Per questa esercitazione non è necessario un segreto client.</span><span class="sxs-lookup"><span data-stu-id="6946d-127">A client secret isn't required for this tutorial.</span></span> 

<span data-ttu-id="6946d-128">Usare i valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="6946d-128">Use the following values:</span></span>

| <span data-ttu-id="6946d-129">Impostazione</span><span class="sxs-lookup"><span data-stu-id="6946d-129">Setting</span></span>                       | <span data-ttu-id="6946d-130">valore</span><span class="sxs-lookup"><span data-stu-id="6946d-130">Value</span></span>                     | <span data-ttu-id="6946d-131">Note</span><span class="sxs-lookup"><span data-stu-id="6946d-131">Notes</span></span>                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="6946d-132">**Nome**</span><span class="sxs-lookup"><span data-stu-id="6946d-132">**Name**</span></span>                      | <span data-ttu-id="6946d-133">*&lt;nome dell'app&gt;*</span><span class="sxs-lookup"><span data-stu-id="6946d-133">*&lt;app name&gt;*</span></span>        | <span data-ttu-id="6946d-134">Immettere un **nome** per l'app che descriva l'app agli utenti.</span><span class="sxs-lookup"><span data-stu-id="6946d-134">Enter a **Name** for the app that describes your app to consumers.</span></span>                                                                                                                                 |
| <span data-ttu-id="6946d-135">**Includi app Web/API Web**</span><span class="sxs-lookup"><span data-stu-id="6946d-135">**Include web app / web API**</span></span> | <span data-ttu-id="6946d-136">Sì</span><span class="sxs-lookup"><span data-stu-id="6946d-136">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="6946d-137">**Consenti il flusso implicito**</span><span class="sxs-lookup"><span data-stu-id="6946d-137">**Allow implicit flow**</span></span>       | <span data-ttu-id="6946d-138">Sì</span><span class="sxs-lookup"><span data-stu-id="6946d-138">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="6946d-139">**URL di risposta**</span><span class="sxs-lookup"><span data-stu-id="6946d-139">**Reply URL**</span></span>                 | `https://localhost:44300/signin-oidc` | <span data-ttu-id="6946d-140">Gli URL di risposta sono gli endpoint a cui Azure AD B2C restituisce eventuali token richiesti dall'app.</span><span class="sxs-lookup"><span data-stu-id="6946d-140">Reply URLs are endpoints where Azure AD B2C returns any tokens that your app requests.</span></span> <span data-ttu-id="6946d-141">Visual Studio fornisce l'URL di risposta da usare.</span><span class="sxs-lookup"><span data-stu-id="6946d-141">Visual Studio provides the Reply URL to use.</span></span> <span data-ttu-id="6946d-142">Per il momento, immettere `https://localhost:44300/signin-oidc` per completare il modulo.</span><span class="sxs-lookup"><span data-stu-id="6946d-142">For now, enter `https://localhost:44300/signin-oidc` to complete the form.</span></span> |
| <span data-ttu-id="6946d-143">**URI ID app**</span><span class="sxs-lookup"><span data-stu-id="6946d-143">**App ID URI**</span></span>                | <span data-ttu-id="6946d-144">Lasciare vuoto</span><span class="sxs-lookup"><span data-stu-id="6946d-144">Leave blank</span></span>               | <span data-ttu-id="6946d-145">Non richiesto per questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="6946d-145">Not required for this tutorial.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="6946d-146">**Includi client nativo**</span><span class="sxs-lookup"><span data-stu-id="6946d-146">**Include native client**</span></span>     | <span data-ttu-id="6946d-147">No</span><span class="sxs-lookup"><span data-stu-id="6946d-147">No</span></span>                        |                                                                                                                                                                                                    |

> [!WARNING]
> <span data-ttu-id="6946d-148">Se si configura un URL di risposta non localhost, tenere presente i [vincoli sugli elementi consentiti nell'elenco URL di risposta](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application).</span><span class="sxs-lookup"><span data-stu-id="6946d-148">If setting up a non-localhost Reply URL, be aware of the [constraints on what is allowed in the Reply URL list](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application).</span></span> 

<span data-ttu-id="6946d-149">Dopo la registrazione dell'app, viene visualizzato l'elenco delle app nel tenant.</span><span class="sxs-lookup"><span data-stu-id="6946d-149">After the app is registered, the list of apps in the tenant is displayed.</span></span> <span data-ttu-id="6946d-150">Selezionare l'app appena registrata.</span><span class="sxs-lookup"><span data-stu-id="6946d-150">Select the app that was just registered.</span></span> <span data-ttu-id="6946d-151">Selezionare l'icona **copia** a destra del campo **ID applicazione** per copiarla negli Appunti.</span><span class="sxs-lookup"><span data-stu-id="6946d-151">Select the **Copy** icon to the right of the **Application ID** field to copy it to the clipboard.</span></span>

<span data-ttu-id="6946d-152">Al momento non è possibile configurare altre informazioni nel tenant di Azure AD B2C, ma lasciare aperta la finestra del browser.</span><span class="sxs-lookup"><span data-stu-id="6946d-152">Nothing more can be configured in the Azure AD B2C tenant at this time, but leave the browser window open.</span></span> <span data-ttu-id="6946d-153">Dopo la creazione dell'app ASP.NET Core è presente una maggiore configurazione.</span><span class="sxs-lookup"><span data-stu-id="6946d-153">There is more configuration after the ASP.NET Core app is created.</span></span>

## <a name="create-an-aspnet-core-app-in-visual-studio"></a><span data-ttu-id="6946d-154">Creare un'app ASP.NET Core in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6946d-154">Create an ASP.NET Core app in Visual Studio</span></span>

<span data-ttu-id="6946d-155">Il modello di applicazione Web di Visual Studio può essere configurato per l'uso del tenant Azure AD B2C per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="6946d-155">The Visual Studio Web Application template can be configured to use the Azure AD B2C tenant for authentication.</span></span>

<span data-ttu-id="6946d-156">In Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="6946d-156">In Visual Studio:</span></span>

1. <span data-ttu-id="6946d-157">Creare una nuova applicazione Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6946d-157">Create a new ASP.NET Core Web Application.</span></span> 
2. <span data-ttu-id="6946d-158">Selezionare **applicazione Web** dall'elenco di modelli.</span><span class="sxs-lookup"><span data-stu-id="6946d-158">Select **Web Application** from the list of templates.</span></span>
3. <span data-ttu-id="6946d-159">Selezionare il pulsante **Modifica autenticazione** .</span><span class="sxs-lookup"><span data-stu-id="6946d-159">Select the **Change Authentication** button.</span></span>
    
    ![Pulsante Modifica autenticazione](./azure-ad-b2c/_static/changeauth.png)

4. <span data-ttu-id="6946d-161">Nella finestra di dialogo **Cambia autenticazione** selezionare **account utente singoli**, quindi selezionare **Connetti a un archivio utente esistente nel cloud** nell'elenco a discesa.</span><span class="sxs-lookup"><span data-stu-id="6946d-161">In the **Change Authentication** dialog, select **Individual User Accounts**, and then select **Connect to an existing user store in the cloud** in the dropdown.</span></span> 
    
    ![Finestra di dialogo Cambia autenticazione](./azure-ad-b2c/_static/changeauthdialog.png)

5. <span data-ttu-id="6946d-163">Completare il modulo con i valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="6946d-163">Complete the form with the following values:</span></span>
    
    | <span data-ttu-id="6946d-164">Impostazione</span><span class="sxs-lookup"><span data-stu-id="6946d-164">Setting</span></span>                       | <span data-ttu-id="6946d-165">valore</span><span class="sxs-lookup"><span data-stu-id="6946d-165">Value</span></span>                                                 |
    |-------------------------------|-------------------------------------------------------|
    | <span data-ttu-id="6946d-166">**Nome di dominio**</span><span class="sxs-lookup"><span data-stu-id="6946d-166">**Domain Name**</span></span>               | <span data-ttu-id="6946d-167">*&lt;nome di dominio del tenant B2C&gt;*</span><span class="sxs-lookup"><span data-stu-id="6946d-167">*&lt;the domain name of your B2C tenant&gt;*</span></span>          |
    | <span data-ttu-id="6946d-168">**ID applicazione**</span><span class="sxs-lookup"><span data-stu-id="6946d-168">**Application ID**</span></span>            | <span data-ttu-id="6946d-169">*&lt;incollare l'ID applicazione dagli Appunti&gt;*</span><span class="sxs-lookup"><span data-stu-id="6946d-169">*&lt;paste the Application ID from the clipboard&gt;*</span></span> |
    | <span data-ttu-id="6946d-170">**Percorso di callback**</span><span class="sxs-lookup"><span data-stu-id="6946d-170">**Callback Path**</span></span>             | <span data-ttu-id="6946d-171">*&lt;Usa il valore predefinito&gt;*</span><span class="sxs-lookup"><span data-stu-id="6946d-171">*&lt;use the default value&gt;*</span></span>                       |
    | <span data-ttu-id="6946d-172">**Criteri di iscrizione o di accesso**</span><span class="sxs-lookup"><span data-stu-id="6946d-172">**Sign-up or sign-in policy**</span></span> | `B2C_1_SiUpIn`                                        |
    | <span data-ttu-id="6946d-173">**Reimposta criteri password**</span><span class="sxs-lookup"><span data-stu-id="6946d-173">**Reset password policy**</span></span>     | `B2C_1_SSPR`                                          |
    | <span data-ttu-id="6946d-174">**Modificare i criteri del profilo**</span><span class="sxs-lookup"><span data-stu-id="6946d-174">**Edit profile policy**</span></span>       | <span data-ttu-id="6946d-175">*&lt;lascia vuoto&gt;*</span><span class="sxs-lookup"><span data-stu-id="6946d-175">*&lt;leave blank&gt;*</span></span>                                 |
    
    <span data-ttu-id="6946d-176">Selezionare il collegamento **copia** accanto a **URI di risposta** per copiare l'URI di risposta negli Appunti.</span><span class="sxs-lookup"><span data-stu-id="6946d-176">Select the **Copy** link next to **Reply URI** to copy the Reply URI to the clipboard.</span></span> <span data-ttu-id="6946d-177">Selezionare **OK** per chiudere la finestra di dialogo **Modifica autenticazione** .</span><span class="sxs-lookup"><span data-stu-id="6946d-177">Select **OK** to close the **Change Authentication** dialog.</span></span> <span data-ttu-id="6946d-178">Selezionare **OK** per creare l'app Web.</span><span class="sxs-lookup"><span data-stu-id="6946d-178">Select **OK** to create the web app.</span></span>

## <a name="finish-the-b2c-app-registration"></a><span data-ttu-id="6946d-179">Completare la registrazione dell'app B2C</span><span class="sxs-lookup"><span data-stu-id="6946d-179">Finish the B2C app registration</span></span>

<span data-ttu-id="6946d-180">Tornare alla finestra del browser con le proprietà dell'app B2C ancora aperte.</span><span class="sxs-lookup"><span data-stu-id="6946d-180">Return to the browser window with the B2C app properties still open.</span></span> <span data-ttu-id="6946d-181">Modificare l' **URL di risposta** temporaneo specificato in precedenza per il valore copiato da Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6946d-181">Change the temporary **Reply URL** specified earlier to the value copied from Visual Studio.</span></span> <span data-ttu-id="6946d-182">Selezionare **Save (Salva** ) nella parte superiore della finestra.</span><span class="sxs-lookup"><span data-stu-id="6946d-182">Select **Save** at the top of the window.</span></span>

> [!TIP]
> <span data-ttu-id="6946d-183">Se l'URL di risposta non è stato copiato, usare l'indirizzo HTTPS dalla scheda debug delle proprietà del progetto Web e aggiungere il valore **CallbackPath** da *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="6946d-183">If you didn't copy the Reply URL, use the HTTPS address from the Debug tab in the web project properties, and append the **CallbackPath** value from *appsettings.json*.</span></span>

## <a name="configure-policies"></a><span data-ttu-id="6946d-184">Configurare i criteri</span><span class="sxs-lookup"><span data-stu-id="6946d-184">Configure policies</span></span>

<span data-ttu-id="6946d-185">Usare la procedura descritta nella documentazione di Azure AD B2C per [creare un criterio di iscrizione o di accesso](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions), quindi [creare un criterio di reimpostazione della password](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span><span class="sxs-lookup"><span data-stu-id="6946d-185">Use the steps in the Azure AD B2C documentation to [create a sign-up or sign-in policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions), and then [create a password reset policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span></span> <span data-ttu-id="6946d-186">Usare i valori di esempio forniti nella documentazione per i \*\* Identity provider\*\*, **gli attributi di iscrizione**e le **attestazioni dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="6946d-186">Use the example values provided in the documentation for **Identity providers**, **Sign-up attributes**, and **Application claims**.</span></span> <span data-ttu-id="6946d-187">Usare il pulsante **Esegui adesso** per testare i criteri come descritto nella documentazione è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="6946d-187">Using the **Run now** button to test the policies as described in the documentation is optional.</span></span>

> [!WARNING]
> <span data-ttu-id="6946d-188">Verificare che i nomi dei criteri siano esattamente come descritto nella documentazione, perché tali criteri sono stati usati nella finestra di dialogo **Cambia autenticazione** in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6946d-188">Ensure the policy names are exactly as described in the documentation, as those policies were used in the **Change Authentication** dialog in Visual Studio.</span></span> <span data-ttu-id="6946d-189">I nomi dei criteri possono essere verificati in *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="6946d-189">The policy names can be verified in *appsettings.json*.</span></span>

## <a name="configure-the-underlying-openidconnectoptionsjwtbearercookie-options"></a><span data-ttu-id="6946d-190">Configurare le opzioni di OpenIdConnectOptions/JwtBearer/cookie sottostanti</span><span class="sxs-lookup"><span data-stu-id="6946d-190">Configure the underlying OpenIdConnectOptions/JwtBearer/Cookie options</span></span>

<span data-ttu-id="6946d-191">Per configurare direttamente le opzioni sottostanti, utilizzare la costante dello schema appropriata in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6946d-191">To configure the underlying options directly, use the appropriate scheme constant in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<CookieAuthenticationOptions>(
    AzureAD[B2C]Defaults.CookieScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a><span data-ttu-id="6946d-192">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="6946d-192">Run the app</span></span>

<span data-ttu-id="6946d-193">In Visual Studio premere **F5** per compilare ed eseguire l'app.</span><span class="sxs-lookup"><span data-stu-id="6946d-193">In Visual Studio, press **F5** to build and run the app.</span></span> <span data-ttu-id="6946d-194">Dopo l'avvio dell'app Web, selezionare **Accept (accetta** ) per accettare l'uso dei cookie (se richiesto) e quindi selezionare **Sign in (accedi**).</span><span class="sxs-lookup"><span data-stu-id="6946d-194">After the web app launches, select **Accept** to accept the use of cookies (if prompted), and then select **Sign in**.</span></span>

![Accedi all'app](./azure-ad-b2c/_static/signin.png)

<span data-ttu-id="6946d-196">Il browser reindirizza al tenant Azure AD B2C.</span><span class="sxs-lookup"><span data-stu-id="6946d-196">The browser redirects to the Azure AD B2C tenant.</span></span> <span data-ttu-id="6946d-197">Accedere con un account esistente (se ne è stato creato il test dei criteri) o selezionare **Iscriviti adesso** per creare un nuovo account.</span><span class="sxs-lookup"><span data-stu-id="6946d-197">Sign in with an existing account (if one was created testing the policies) or select **Sign up now** to create a new account.</span></span> <span data-ttu-id="6946d-198">Il collegamento **password dimenticata?** viene usato per reimpostare una password dimenticata.</span><span class="sxs-lookup"><span data-stu-id="6946d-198">The **Forgot your password?** link is used to reset a forgotten password.</span></span>

![Accesso ad Azure AD B2C](./azure-ad-b2c/_static/b2csts.png)

<span data-ttu-id="6946d-200">Dopo aver eseguito l'accesso, il browser reindirizza all'app Web.</span><span class="sxs-lookup"><span data-stu-id="6946d-200">After successfully signing in, the browser redirects to the web app.</span></span>

![Operazione completata](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a><span data-ttu-id="6946d-202">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="6946d-202">Next steps</span></span>

<span data-ttu-id="6946d-203">In questa esercitazione sono state illustrate le procedure per:</span><span class="sxs-lookup"><span data-stu-id="6946d-203">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6946d-204">Creare un tenant di Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="6946d-204">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="6946d-205">Registrare un'app in Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="6946d-205">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="6946d-206">Usare Visual Studio per creare un'applicazione Web ASP.NET Core configurata per l'uso del tenant Azure AD B2C per l'autenticazione</span><span class="sxs-lookup"><span data-stu-id="6946d-206">Use Visual Studio to create an ASP.NET Core Web Application configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="6946d-207">Configurare i criteri che controllano il comportamento del tenant Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="6946d-207">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

<span data-ttu-id="6946d-208">Ora che l'app ASP.NET Core è configurata in modo da usare Azure AD B2C per l'autenticazione, è possibile usare l' [attributo di autorizzazione](xref:security/authorization/simple) per proteggere l'app.</span><span class="sxs-lookup"><span data-stu-id="6946d-208">Now that the ASP.NET Core app is configured to use Azure AD B2C for authentication, the [Authorize attribute](xref:security/authorization/simple) can be used to secure your app.</span></span> <span data-ttu-id="6946d-209">Continua a sviluppare l'app imparando a:</span><span class="sxs-lookup"><span data-stu-id="6946d-209">Continue developing your app by learning to:</span></span>

* <span data-ttu-id="6946d-210">[Personalizzare l'interfaccia utente di Azure ad B2C](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span><span class="sxs-lookup"><span data-stu-id="6946d-210">[Customize the Azure AD B2C user interface](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span></span>
* <span data-ttu-id="6946d-211">[Configurare i requisiti di complessità delle password](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span><span class="sxs-lookup"><span data-stu-id="6946d-211">[Configure password complexity requirements](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span></span>
* <span data-ttu-id="6946d-212">[Abilitare l'autenticazione a più fattori](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span><span class="sxs-lookup"><span data-stu-id="6946d-212">[Enable multi-factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span></span>
* <span data-ttu-id="6946d-213">Configurare provider di identità aggiuntivi, ad esempio [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)e altri.</span><span class="sxs-lookup"><span data-stu-id="6946d-213">Configure additional identity providers, such as [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), and others.</span></span>
* <span data-ttu-id="6946d-214">[Usare il API Graph Azure ad](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) per recuperare informazioni aggiuntive sull'utente, ad esempio l'appartenenza al gruppo, dal tenant di Azure ad B2C.</span><span class="sxs-lookup"><span data-stu-id="6946d-214">[Use the Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) to retrieve additional user information, such as group membership, from the Azure AD B2C tenant.</span></span>
* <span data-ttu-id="6946d-215">[Proteggere un'API web ASP.NET Core usando Azure ad B2C](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/).</span><span class="sxs-lookup"><span data-stu-id="6946d-215">[Secure an ASP.NET Core web API using Azure AD B2C](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/).</span></span>
* <span data-ttu-id="6946d-216">[Esercitazione: concedere l'accesso a un'API web ASP.NET usando Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-web-api-dotnet).</span><span class="sxs-lookup"><span data-stu-id="6946d-216">[Tutorial: Grant access to an ASP.NET web API using Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-web-api-dotnet).</span></span>
