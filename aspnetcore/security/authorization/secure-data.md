---
title: Creare un'app ASP.NET Core con i dati utente protetti dall'autorizzazione
author: rick-anderson
description: Informazioni su come creare un'app Web di ASP.NET Core con i dati utente protetti dall'autorizzazione. Include HTTPS, autenticazione, sicurezza ASP.NET Core Identity .
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
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
uid: security/authorization/secure-data
ms.openlocfilehash: ebd3c0dc9baa63b30f142773d7a3d621ce4082d9
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689305"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="cc411-104">Creare un'app Web di ASP.NET Core con i dati utente protetti dall'autorizzazione</span><span class="sxs-lookup"><span data-stu-id="cc411-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="cc411-105">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="cc411-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="cc411-106">Vedi [questo PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="cc411-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cc411-107">Questa esercitazione illustra come creare un'app Web di ASP.NET Core con i dati utente protetti dall'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="cc411-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="cc411-108">Viene visualizzato un elenco di contatti creati dagli utenti autenticati (registrati).</span><span class="sxs-lookup"><span data-stu-id="cc411-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="cc411-109">Sono disponibili tre gruppi di sicurezza:</span><span class="sxs-lookup"><span data-stu-id="cc411-109">There are three security groups:</span></span>

* <span data-ttu-id="cc411-110">**Gli utenti registrati** possono visualizzare tutti i dati approvati e possono modificare/eliminare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="cc411-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="cc411-111">I **responsabili** possono approvare o rifiutare i dati di contatto.</span><span class="sxs-lookup"><span data-stu-id="cc411-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="cc411-112">Solo i contatti approvati sono visibili agli utenti.</span><span class="sxs-lookup"><span data-stu-id="cc411-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="cc411-113">Gli **amministratori** possono approvare/rifiutare e modificare/eliminare i dati.</span><span class="sxs-lookup"><span data-stu-id="cc411-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="cc411-114">Le immagini in questo documento non corrispondono esattamente ai modelli più recenti.</span><span class="sxs-lookup"><span data-stu-id="cc411-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="cc411-115">Nell'immagine seguente, l'utente Rick ( `rick@example.com` ) ha eseguito l'accesso.</span><span class="sxs-lookup"><span data-stu-id="cc411-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="cc411-116">Rick può visualizzare solo i contatti approvati e **modificare** / **Delete** / **Crea nuovi** collegamenti per i contatti.</span><span class="sxs-lookup"><span data-stu-id="cc411-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="cc411-117">Solo l'ultimo record, creato da Rick, Visualizza i collegamenti **modifica** ed **Elimina** .</span><span class="sxs-lookup"><span data-stu-id="cc411-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="cc411-118">Gli altri utenti non vedranno l'ultimo record fino a quando un responsabile o un amministratore non modifica lo stato in "approvato".</span><span class="sxs-lookup"><span data-stu-id="cc411-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot che illustra l'accesso a Rick](secure-data/_static/rick.png)

<span data-ttu-id="cc411-120">Nell'immagine seguente, `manager@contoso.com` è stato eseguito l'accesso e nel ruolo del Manager:</span><span class="sxs-lookup"><span data-stu-id="cc411-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot che mostra l' manager@contoso.com accesso](secure-data/_static/manager1.png)

<span data-ttu-id="cc411-122">Nella figura seguente viene illustrata la visualizzazione dettagli Manager di un contatto:</span><span class="sxs-lookup"><span data-stu-id="cc411-122">The following image shows the managers details view of a contact:</span></span>

![Visualizzazione del contatto da un responsabile](secure-data/_static/manager.png)

<span data-ttu-id="cc411-124">I pulsanti **approva** e **rifiuta** vengono visualizzati solo per Manager e amministratori.</span><span class="sxs-lookup"><span data-stu-id="cc411-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="cc411-125">Nell'immagine seguente, `admin@contoso.com` è stato eseguito l'accesso e il ruolo dell'amministratore:</span><span class="sxs-lookup"><span data-stu-id="cc411-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot che mostra l' admin@contoso.com accesso](secure-data/_static/admin.png)

<span data-ttu-id="cc411-127">L'amministratore dispone di tutti i privilegi.</span><span class="sxs-lookup"><span data-stu-id="cc411-127">The administrator has all privileges.</span></span> <span data-ttu-id="cc411-128">Può leggere, modificare ed eliminare qualsiasi contatto e modificare lo stato dei contatti.</span><span class="sxs-lookup"><span data-stu-id="cc411-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="cc411-129">L'app è stata creata mediante l' [impalcatura](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) del `Contact` modello seguente:</span><span class="sxs-lookup"><span data-stu-id="cc411-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="cc411-130">L'esempio contiene i gestori di autorizzazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="cc411-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="cc411-131">`ContactIsOwnerAuthorizationHandler`: Assicura che un utente possa modificare solo i propri dati.</span><span class="sxs-lookup"><span data-stu-id="cc411-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="cc411-132">`ContactManagerAuthorizationHandler`: Consente ai responsabili di approvare o rifiutare i contatti.</span><span class="sxs-lookup"><span data-stu-id="cc411-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="cc411-133">`ContactAdministratorsAuthorizationHandler`: Consente agli amministratori di approvare o rifiutare contatti e di modificare/eliminare contatti.</span><span class="sxs-lookup"><span data-stu-id="cc411-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cc411-134">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="cc411-134">Prerequisites</span></span>

<span data-ttu-id="cc411-135">Questa esercitazione è avanzata.</span><span class="sxs-lookup"><span data-stu-id="cc411-135">This tutorial is advanced.</span></span> <span data-ttu-id="cc411-136">È necessario avere familiarità con:</span><span class="sxs-lookup"><span data-stu-id="cc411-136">You should be familiar with:</span></span>

* [<span data-ttu-id="cc411-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cc411-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="cc411-138">autenticazione</span><span class="sxs-lookup"><span data-stu-id="cc411-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="cc411-139">Conferma account e recupero password</span><span class="sxs-lookup"><span data-stu-id="cc411-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="cc411-140">Autorizzazione</span><span class="sxs-lookup"><span data-stu-id="cc411-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="cc411-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="cc411-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="cc411-142">App iniziale e completata</span><span class="sxs-lookup"><span data-stu-id="cc411-142">The starter and completed app</span></span>

<span data-ttu-id="cc411-143">[Scaricare](xref:index#how-to-download-a-sample) l'app [completata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="cc411-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="cc411-144">[Testare](#test-the-completed-app) l'app completata in modo da acquisire familiarità con le funzionalità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="cc411-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="cc411-145">App iniziale</span><span class="sxs-lookup"><span data-stu-id="cc411-145">The starter app</span></span>

<span data-ttu-id="cc411-146">[Scaricare](xref:index#how-to-download-a-sample) l'app [Starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="cc411-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="cc411-147">Eseguire l'app, toccare il collegamento **ContactManager** e verificare che sia possibile creare, modificare ed eliminare un contatto.</span><span class="sxs-lookup"><span data-stu-id="cc411-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span> <span data-ttu-id="cc411-148">Per creare l'app iniziale, vedere [creare l'app iniziale](#create-the-starter-app).</span><span class="sxs-lookup"><span data-stu-id="cc411-148">To create the starter app, see [Create the starter app](#create-the-starter-app).</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="cc411-149">Proteggere i dati utente</span><span class="sxs-lookup"><span data-stu-id="cc411-149">Secure user data</span></span>

<span data-ttu-id="cc411-150">Le sezioni seguenti includono tutti i passaggi principali per creare l'app Secure User Data.</span><span class="sxs-lookup"><span data-stu-id="cc411-150">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="cc411-151">Può risultare utile fare riferimento al progetto completato.</span><span class="sxs-lookup"><span data-stu-id="cc411-151">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="cc411-152">Associare i dati di contatto all'utente</span><span class="sxs-lookup"><span data-stu-id="cc411-152">Tie the contact data to the user</span></span>

<span data-ttu-id="cc411-153">Usare l' [Identity](xref:security/authentication/identity) ID utente ASP.NET per assicurarsi che gli utenti possano modificare i dati, ma non i dati di altri utenti.</span><span class="sxs-lookup"><span data-stu-id="cc411-153">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="cc411-154">Aggiungere `OwnerID` e `ContactStatus` al `Contact` modello:</span><span class="sxs-lookup"><span data-stu-id="cc411-154">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="cc411-155">`OwnerID` ID dell'utente della `AspNetUser` tabella nel [Identity](xref:security/authentication/identity) database.</span><span class="sxs-lookup"><span data-stu-id="cc411-155">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="cc411-156">Il `Status` campo determina se un contatto è visualizzabile dagli utenti generali.</span><span class="sxs-lookup"><span data-stu-id="cc411-156">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="cc411-157">Creare una nuova migrazione e aggiornare il database:</span><span class="sxs-lookup"><span data-stu-id="cc411-157">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="cc411-158">Aggiungere servizi ruolo a Identity</span><span class="sxs-lookup"><span data-stu-id="cc411-158">Add Role services to Identity</span></span>

<span data-ttu-id="cc411-159">Aggiungere [Aggiungi ruoli](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) per aggiungere servizi ruolo:</span><span class="sxs-lookup"><span data-stu-id="cc411-159">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="cc411-160">Richiedi utenti autenticati</span><span class="sxs-lookup"><span data-stu-id="cc411-160">Require authenticated users</span></span>

<span data-ttu-id="cc411-161">Impostare i criteri di autenticazione di fallback per richiedere l'autenticazione degli utenti:</span><span class="sxs-lookup"><span data-stu-id="cc411-161">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="cc411-162">Il codice evidenziato precedente imposta i [criteri di autenticazione di fallback](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="cc411-162">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="cc411-163">I criteri di autenticazione di fallback richiedono che *_tutti_* gli utenti siano autenticati, ad eccezione di Razor pagine, controller o metodi di azione con un attributo di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="cc411-163">The fallback authentication policy requires \**_all_* _ users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="cc411-164">Ad esempio, Razor pagine, controller o metodi di azione con `[AllowAnonymous]` o `[Authorize(PolicyName="MyPolicy")]` utilizzano l'attributo di autenticazione applicato anziché i criteri di autenticazione di fallback.</span><span class="sxs-lookup"><span data-stu-id="cc411-164">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="cc411-165"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> aggiunge <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> all'istanza corrente che impone che l'utente corrente sia autenticato.</span><span class="sxs-lookup"><span data-stu-id="cc411-165"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> adds <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> to the current instance, which enforces that the current user is authenticated.</span></span>

<span data-ttu-id="cc411-166">I criteri di autenticazione di fallback:</span><span class="sxs-lookup"><span data-stu-id="cc411-166">The fallback authentication policy:</span></span>

<span data-ttu-id="cc411-167">_ Viene applicato a tutte le richieste che non specificano in modo esplicito i criteri di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="cc411-167">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="cc411-168">Per le richieste gestite dal routing degli endpoint, questo includerebbe qualsiasi endpoint che non specifichi un attributo di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="cc411-168">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="cc411-169">Per le richieste gestite da altri middleware dopo il middleware di autorizzazione, ad esempio [i file statici](xref:fundamentals/static-files), il criterio viene applicato a tutte le richieste.</span><span class="sxs-lookup"><span data-stu-id="cc411-169">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="cc411-170">L'impostazione dei criteri di autenticazione di fallback per richiedere l'autenticazione degli utenti consente di proteggere le Razor pagine e i controller appena aggiunti.</span><span class="sxs-lookup"><span data-stu-id="cc411-170">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="cc411-171">La necessità di eseguire l'autenticazione per impostazione predefinita è più sicura rispetto a quella di fare affidamento su nuovi controller e Razor pagine per includere l' `[Authorize]` attributo.</span><span class="sxs-lookup"><span data-stu-id="cc411-171">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="cc411-172">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> classe contiene anche <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="cc411-172">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="cc411-173">`DefaultPolicy`È il criterio utilizzato con l' `[Authorize]` attributo quando non è specificato alcun criterio.</span><span class="sxs-lookup"><span data-stu-id="cc411-173">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="cc411-174">`[Authorize]` non contiene un criterio denominato, a differenza di `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="cc411-174">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="cc411-175">Per ulteriori informazioni sui criteri, vedere <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="cc411-175">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="cc411-176">Un metodo alternativo per i controller e Razor le pagine MVC per richiedere che tutti gli utenti siano autenticati è l'aggiunta di un filtro di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="cc411-176">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="cc411-177">Il codice precedente usa un filtro di autorizzazione, l'impostazione dei criteri di fallback usa il routing degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="cc411-177">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="cc411-178">L'impostazione dei criteri di fallback è il modo migliore per richiedere l'autenticazione di tutti gli utenti.</span><span class="sxs-lookup"><span data-stu-id="cc411-178">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="cc411-179">Aggiungere [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) alle `Index` pagine e in `Privacy` modo che gli utenti anonimi possano ottenere informazioni sul sito prima della registrazione:</span><span class="sxs-lookup"><span data-stu-id="cc411-179">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="cc411-180">Configurare l'account di test</span><span class="sxs-lookup"><span data-stu-id="cc411-180">Configure the test account</span></span>

<span data-ttu-id="cc411-181">La `SeedData` classe crea due account: Administrator e Manager.</span><span class="sxs-lookup"><span data-stu-id="cc411-181">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="cc411-182">Utilizzare lo [strumento Gestione segreta](xref:security/app-secrets) per impostare una password per questi account.</span><span class="sxs-lookup"><span data-stu-id="cc411-182">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="cc411-183">Impostare la password dalla directory del progetto (la directory contenente *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="cc411-183">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="cc411-184">Se non si specifica una password complessa, viene generata un'eccezione quando `SeedData.Initialize` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="cc411-184">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="cc411-185">Aggiornare `Main` per usare la password di test:</span><span class="sxs-lookup"><span data-stu-id="cc411-185">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="cc411-186">Creare gli account di test e aggiornare i contatti</span><span class="sxs-lookup"><span data-stu-id="cc411-186">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="cc411-187">Aggiornare il `Initialize` metodo nella `SeedData` classe per creare gli account di test:</span><span class="sxs-lookup"><span data-stu-id="cc411-187">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="cc411-188">Aggiungere l'ID utente amministratore e `ContactStatus` ai contatti.</span><span class="sxs-lookup"><span data-stu-id="cc411-188">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="cc411-189">Creare uno dei contatti "inviato" e uno "rifiutato".</span><span class="sxs-lookup"><span data-stu-id="cc411-189">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="cc411-190">Aggiungere l'ID utente e lo stato a tutti i contatti.</span><span class="sxs-lookup"><span data-stu-id="cc411-190">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="cc411-191">Viene visualizzato un solo contatto:</span><span class="sxs-lookup"><span data-stu-id="cc411-191">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="cc411-192">Creazione di gestori di autorizzazioni proprietario, Manager e amministratore</span><span class="sxs-lookup"><span data-stu-id="cc411-192">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="cc411-193">Creare una `ContactIsOwnerAuthorizationHandler` classe nella cartella *authorization* .</span><span class="sxs-lookup"><span data-stu-id="cc411-193">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="cc411-194">`ContactIsOwnerAuthorizationHandler`Verifica che l'utente che agisce su una risorsa appartenga alla risorsa.</span><span class="sxs-lookup"><span data-stu-id="cc411-194">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="cc411-195">`ContactIsOwnerAuthorizationHandler`Contesto delle chiamate [. Ha esito positivo](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se l'utente autenticato corrente è il proprietario del contatto.</span><span class="sxs-lookup"><span data-stu-id="cc411-195">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="cc411-196">Gestori autorizzazioni in genere:</span><span class="sxs-lookup"><span data-stu-id="cc411-196">Authorization handlers generally:</span></span>

* <span data-ttu-id="cc411-197">Restituisce `context.Succeed` quando vengono soddisfatti i requisiti.</span><span class="sxs-lookup"><span data-stu-id="cc411-197">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="cc411-198">Restituisce `Task.CompletedTask` quando i requisiti non vengono soddisfatti.</span><span class="sxs-lookup"><span data-stu-id="cc411-198">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="cc411-199">`Task.CompletedTask` non ha esito positivo o negativo &mdash; consente l'esecuzione di altri gestori di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="cc411-199">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="cc411-200">Se è necessario eseguire in modo esplicito l'errore, restituire [context. Esito negativo](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="cc411-200">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="cc411-201">L'app consente ai proprietari dei contatti di modificare/eliminare/creare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="cc411-201">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="cc411-202">`ContactIsOwnerAuthorizationHandler` non è necessario controllare l'operazione passata nel parametro requirement.</span><span class="sxs-lookup"><span data-stu-id="cc411-202">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="cc411-203">Creazione di un gestore autorizzazioni di gestione</span><span class="sxs-lookup"><span data-stu-id="cc411-203">Create a manager authorization handler</span></span>

<span data-ttu-id="cc411-204">Creare una `ContactManagerAuthorizationHandler` classe nella cartella *authorization* .</span><span class="sxs-lookup"><span data-stu-id="cc411-204">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="cc411-205">`ContactManagerAuthorizationHandler`Verifica che l'utente che agisce sulla risorsa sia un responsabile.</span><span class="sxs-lookup"><span data-stu-id="cc411-205">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="cc411-206">Solo i responsabili possono approvare o rifiutare le modifiche al contenuto (nuove o modificate).</span><span class="sxs-lookup"><span data-stu-id="cc411-206">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="cc411-207">Creazione di un gestore autorizzazioni Amministratore</span><span class="sxs-lookup"><span data-stu-id="cc411-207">Create an administrator authorization handler</span></span>

<span data-ttu-id="cc411-208">Creare una `ContactAdministratorsAuthorizationHandler` classe nella cartella *authorization* .</span><span class="sxs-lookup"><span data-stu-id="cc411-208">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="cc411-209">Il `ContactAdministratorsAuthorizationHandler` Verifica che l'utente che agisce sulla risorsa sia un amministratore.</span><span class="sxs-lookup"><span data-stu-id="cc411-209">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="cc411-210">L'amministratore può eseguire tutte le operazioni.</span><span class="sxs-lookup"><span data-stu-id="cc411-210">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="cc411-211">Registrare i gestori di autorizzazioni</span><span class="sxs-lookup"><span data-stu-id="cc411-211">Register the authorization handlers</span></span>

<span data-ttu-id="cc411-212">I servizi che usano Entity Framework Core devono essere registrati per l' [inserimento di dipendenze](xref:fundamentals/dependency-injection) usando [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="cc411-212">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="cc411-213">`ContactIsOwnerAuthorizationHandler`Usa ASP.NET Core [Identity](xref:security/authentication/identity) , che è basato su Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="cc411-213">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="cc411-214">Registrare i gestori con la raccolta di servizi in modo che siano disponibili per `ContactsController` tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cc411-214">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="cc411-215">Aggiungere il codice seguente alla fine di `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cc411-215">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="cc411-216">`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` vengono aggiunti come singleton.</span><span class="sxs-lookup"><span data-stu-id="cc411-216">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="cc411-217">Si tratta di singleton perché non usano EF e tutte le informazioni necessarie si trovano nel `Context` parametro del `HandleRequirementAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="cc411-217">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="cc411-218">Autorizzazione supporto</span><span class="sxs-lookup"><span data-stu-id="cc411-218">Support authorization</span></span>

<span data-ttu-id="cc411-219">In questa sezione si aggiornano le Razor pagine e si aggiunge una classe dei requisiti delle operazioni.</span><span class="sxs-lookup"><span data-stu-id="cc411-219">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="cc411-220">Esaminare la classe dei requisiti delle operazioni di contatto</span><span class="sxs-lookup"><span data-stu-id="cc411-220">Review the contact operations requirements class</span></span>

<span data-ttu-id="cc411-221">Esaminare la `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="cc411-221">Review the `ContactOperations` class.</span></span> <span data-ttu-id="cc411-222">Questa classe contiene i requisiti supportati dall'app:</span><span class="sxs-lookup"><span data-stu-id="cc411-222">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="cc411-223">Creare una classe di base per le Razor pagine contatti</span><span class="sxs-lookup"><span data-stu-id="cc411-223">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="cc411-224">Creare una classe di base che contiene i servizi utilizzati nelle Razor pagine contatti.</span><span class="sxs-lookup"><span data-stu-id="cc411-224">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="cc411-225">La classe base inserisce il codice di inizializzazione in un'unica posizione:</span><span class="sxs-lookup"><span data-stu-id="cc411-225">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="cc411-226">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="cc411-226">The preceding code:</span></span>

* <span data-ttu-id="cc411-227">Aggiunge il `IAuthorizationService` servizio per accedere ai gestori di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="cc411-227">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="cc411-228">Aggiunge il Identity `UserManager` servizio.</span><span class="sxs-lookup"><span data-stu-id="cc411-228">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="cc411-229">Aggiungere l'oggetto `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="cc411-229">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="cc411-230">Aggiornare CreateModel</span><span class="sxs-lookup"><span data-stu-id="cc411-230">Update the CreateModel</span></span>

<span data-ttu-id="cc411-231">Aggiornare il costruttore del modello di pagina create per usare la `DI_BasePageModel` classe di base:</span><span class="sxs-lookup"><span data-stu-id="cc411-231">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="cc411-232">Aggiornare il `CreateModel.OnPostAsync` metodo a:</span><span class="sxs-lookup"><span data-stu-id="cc411-232">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="cc411-233">Aggiungere l'ID utente al `Contact` modello.</span><span class="sxs-lookup"><span data-stu-id="cc411-233">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="cc411-234">Chiamare il gestore autorizzazioni per verificare che l'utente disponga dell'autorizzazione per la creazione di contatti.</span><span class="sxs-lookup"><span data-stu-id="cc411-234">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="cc411-235">Aggiornare IndexModel</span><span class="sxs-lookup"><span data-stu-id="cc411-235">Update the IndexModel</span></span>

<span data-ttu-id="cc411-236">Aggiornare il `OnGetAsync` metodo in modo da visualizzare solo i contatti approvati per gli utenti generali:</span><span class="sxs-lookup"><span data-stu-id="cc411-236">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="cc411-237">Aggiornare EditModel</span><span class="sxs-lookup"><span data-stu-id="cc411-237">Update the EditModel</span></span>

<span data-ttu-id="cc411-238">Aggiungere un gestore autorizzazioni per verificare che l'utente sia proprietario del contatto.</span><span class="sxs-lookup"><span data-stu-id="cc411-238">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="cc411-239">Poiché l'autorizzazione della risorsa viene convalidata, l' `[Authorize]` attributo non è sufficiente.</span><span class="sxs-lookup"><span data-stu-id="cc411-239">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="cc411-240">L'app non ha accesso alla risorsa quando vengono valutati gli attributi.</span><span class="sxs-lookup"><span data-stu-id="cc411-240">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="cc411-241">L'autorizzazione basata sulle risorse deve essere imperativa.</span><span class="sxs-lookup"><span data-stu-id="cc411-241">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="cc411-242">I controlli devono essere eseguiti una volta che l'app può accedere alla risorsa, eseguendo il caricamento nel modello di pagina o caricando l'app all'interno del gestore stesso.</span><span class="sxs-lookup"><span data-stu-id="cc411-242">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="cc411-243">Si accede spesso alla risorsa passando la chiave della risorsa.</span><span class="sxs-lookup"><span data-stu-id="cc411-243">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="cc411-244">Aggiornare DeleteModel</span><span class="sxs-lookup"><span data-stu-id="cc411-244">Update the DeleteModel</span></span>

<span data-ttu-id="cc411-245">Aggiornare il modello di pagina Elimina per utilizzare il gestore autorizzazione per verificare che l'utente disponga dell'autorizzazione DELETE per il contatto.</span><span class="sxs-lookup"><span data-stu-id="cc411-245">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="cc411-246">Inserire il servizio di autorizzazione nelle viste</span><span class="sxs-lookup"><span data-stu-id="cc411-246">Inject the authorization service into the views</span></span>

<span data-ttu-id="cc411-247">Attualmente, l'interfaccia utente Mostra i collegamenti modifica ed Elimina per i contatti che non possono essere modificati dall'utente.</span><span class="sxs-lookup"><span data-stu-id="cc411-247">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="cc411-248">Inserire il servizio di autorizzazione nel file *pages/_ViewImports. cshtml* in modo che sia disponibile per tutte le visualizzazioni:</span><span class="sxs-lookup"><span data-stu-id="cc411-248">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="cc411-249">Il markup precedente aggiunge diverse `using` istruzioni.</span><span class="sxs-lookup"><span data-stu-id="cc411-249">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="cc411-250">Aggiornare i collegamenti **Edit** ed **Delete** in *pages/Contacts/index. cshtml* in modo che vengano sottoposti a rendering solo per gli utenti con le autorizzazioni appropriate:</span><span class="sxs-lookup"><span data-stu-id="cc411-250">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="cc411-251">Nascondere i collegamenti da utenti che non dispongono dell'autorizzazione per modificare i dati non protegge l'app.</span><span class="sxs-lookup"><span data-stu-id="cc411-251">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="cc411-252">Nascondere i collegamenti rende l'app più intuitiva visualizzando solo i collegamenti validi.</span><span class="sxs-lookup"><span data-stu-id="cc411-252">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="cc411-253">Gli utenti possono hackerare gli URL generati per richiamare le operazioni di modifica ed eliminazione sui dati di cui non sono proprietari.</span><span class="sxs-lookup"><span data-stu-id="cc411-253">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="cc411-254">La Razor pagina o il controller deve applicare i controlli di accesso per proteggere i dati.</span><span class="sxs-lookup"><span data-stu-id="cc411-254">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="cc411-255">Dettagli aggiornamento</span><span class="sxs-lookup"><span data-stu-id="cc411-255">Update Details</span></span>

<span data-ttu-id="cc411-256">Aggiornare la visualizzazione dettagli in modo che i responsabili possano approvare o rifiutare i contatti:</span><span class="sxs-lookup"><span data-stu-id="cc411-256">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="cc411-257">Aggiornare il modello della pagina dei dettagli:</span><span class="sxs-lookup"><span data-stu-id="cc411-257">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="cc411-258">Aggiungere o rimuovere un utente in un ruolo</span><span class="sxs-lookup"><span data-stu-id="cc411-258">Add or remove a user to a role</span></span>

<span data-ttu-id="cc411-259">Per informazioni su, vedere [questo problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="cc411-259">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="cc411-260">Rimozione dei privilegi da un utente.</span><span class="sxs-lookup"><span data-stu-id="cc411-260">Removing privileges from a user.</span></span> <span data-ttu-id="cc411-261">Ad esempio, disattivare un utente in un'app di chat.</span><span class="sxs-lookup"><span data-stu-id="cc411-261">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="cc411-262">Aggiunta di privilegi a un utente.</span><span class="sxs-lookup"><span data-stu-id="cc411-262">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="cc411-263">Differenze tra la sfida e la proibizione</span><span class="sxs-lookup"><span data-stu-id="cc411-263">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="cc411-264">Questa app imposta i criteri predefiniti per [richiedere l'autenticazione degli utenti](#rau).</span><span class="sxs-lookup"><span data-stu-id="cc411-264">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="cc411-265">Il codice seguente consente agli utenti anonimi.</span><span class="sxs-lookup"><span data-stu-id="cc411-265">The following code allows anonymous users.</span></span> <span data-ttu-id="cc411-266">Gli utenti anonimi possono mostrare le differenze tra la richiesta di confronto e la proibizione.</span><span class="sxs-lookup"><span data-stu-id="cc411-266">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="cc411-267">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="cc411-267">In the preceding code:</span></span>

* <span data-ttu-id="cc411-268">Quando l'utente **non** è autenticato, `ChallengeResult` viene restituito un oggetto.</span><span class="sxs-lookup"><span data-stu-id="cc411-268">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="cc411-269">Quando `ChallengeResult` viene restituito un oggetto, l'utente viene reindirizzato alla pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="cc411-269">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="cc411-270">Quando l'utente viene autenticato, ma non autorizzato, `ForbidResult` viene restituito un oggetto.</span><span class="sxs-lookup"><span data-stu-id="cc411-270">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="cc411-271">Quando `ForbidResult` viene restituito un oggetto, l'utente viene reindirizzato alla pagina di accesso negato.</span><span class="sxs-lookup"><span data-stu-id="cc411-271">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="cc411-272">Testare l'app completata</span><span class="sxs-lookup"><span data-stu-id="cc411-272">Test the completed app</span></span>

<span data-ttu-id="cc411-273">Se non è già stata impostata una password per gli account utente di cui è stato eseguito il seeding, usare lo [strumento Gestione segreta](xref:security/app-secrets#secret-manager) per impostare una password:</span><span class="sxs-lookup"><span data-stu-id="cc411-273">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="cc411-274">Scegliere una password complessa: usare otto o più caratteri e almeno un carattere maiuscolo, un numero e un simbolo.</span><span class="sxs-lookup"><span data-stu-id="cc411-274">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="cc411-275">Ad esempio, `Passw0rd!` soddisfa i requisiti per le password complesse.</span><span class="sxs-lookup"><span data-stu-id="cc411-275">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="cc411-276">Eseguire il comando seguente dalla cartella del progetto, dove `<PW>` è la password:</span><span class="sxs-lookup"><span data-stu-id="cc411-276">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="cc411-277">Se l'app dispone di contatti:</span><span class="sxs-lookup"><span data-stu-id="cc411-277">If the app has contacts:</span></span>

* <span data-ttu-id="cc411-278">Eliminare tutti i record nella `Contact` tabella.</span><span class="sxs-lookup"><span data-stu-id="cc411-278">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="cc411-279">Riavviare l'app per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="cc411-279">Restart the app to seed the database.</span></span>

<span data-ttu-id="cc411-280">Un modo semplice per testare l'app completata consiste nell'avviare tre diversi browser (o sessioni in incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="cc411-280">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="cc411-281">In un browser registrare un nuovo utente (ad esempio, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="cc411-281">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="cc411-282">Accedere a ogni browser con un altro utente.</span><span class="sxs-lookup"><span data-stu-id="cc411-282">Sign in to each browser with a different user.</span></span> <span data-ttu-id="cc411-283">Verificare le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="cc411-283">Verify the following operations:</span></span>

* <span data-ttu-id="cc411-284">Gli utenti registrati possono visualizzare tutti i dati di contatto approvati.</span><span class="sxs-lookup"><span data-stu-id="cc411-284">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="cc411-285">Gli utenti registrati possono modificare/eliminare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="cc411-285">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="cc411-286">I responsabili possono approvare/rifiutare i dati di contatto.</span><span class="sxs-lookup"><span data-stu-id="cc411-286">Managers can approve/reject contact data.</span></span> <span data-ttu-id="cc411-287">La `Details` visualizzazione Mostra i pulsanti **approva** e **rifiuta** .</span><span class="sxs-lookup"><span data-stu-id="cc411-287">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="cc411-288">Gli amministratori possono approvare/rifiutare e modificare/eliminare tutti i dati.</span><span class="sxs-lookup"><span data-stu-id="cc411-288">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="cc411-289">User</span><span class="sxs-lookup"><span data-stu-id="cc411-289">User</span></span>                | <span data-ttu-id="cc411-290">Seeding dall'app</span><span class="sxs-lookup"><span data-stu-id="cc411-290">Seeded by the app</span></span> | <span data-ttu-id="cc411-291">Opzioni</span><span class="sxs-lookup"><span data-stu-id="cc411-291">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="cc411-292">No</span><span class="sxs-lookup"><span data-stu-id="cc411-292">No</span></span>                | <span data-ttu-id="cc411-293">Modificare/eliminare i dati personali.</span><span class="sxs-lookup"><span data-stu-id="cc411-293">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="cc411-294">Sì</span><span class="sxs-lookup"><span data-stu-id="cc411-294">Yes</span></span>               | <span data-ttu-id="cc411-295">Approva/rifiuta e modifica/elimina i dati personali.</span><span class="sxs-lookup"><span data-stu-id="cc411-295">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="cc411-296">Sì</span><span class="sxs-lookup"><span data-stu-id="cc411-296">Yes</span></span>               | <span data-ttu-id="cc411-297">Approva/rifiuta e modifica/elimina tutti i dati.</span><span class="sxs-lookup"><span data-stu-id="cc411-297">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="cc411-298">Creare un contatto nel browser dell'amministratore.</span><span class="sxs-lookup"><span data-stu-id="cc411-298">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="cc411-299">Copiare l'URL da eliminare e modificare dal contatto amministratore.</span><span class="sxs-lookup"><span data-stu-id="cc411-299">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="cc411-300">Incollare questi collegamenti nel browser dell'utente test per verificare che l'utente test non possa eseguire queste operazioni.</span><span class="sxs-lookup"><span data-stu-id="cc411-300">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="cc411-301">Creare l'app Starter</span><span class="sxs-lookup"><span data-stu-id="cc411-301">Create the starter app</span></span>

* <span data-ttu-id="cc411-302">Creare un' Razor app per le pagine denominata "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="cc411-302">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="cc411-303">Creare l'app con **singoli account utente**.</span><span class="sxs-lookup"><span data-stu-id="cc411-303">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="cc411-304">Denominarlo "ContactManager" in modo che lo spazio dei nomi corrisponda allo spazio dei nomi usato nell'esempio.</span><span class="sxs-lookup"><span data-stu-id="cc411-304">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="cc411-305">`-uld` Specifica il database locale anziché SQLite</span><span class="sxs-lookup"><span data-stu-id="cc411-305">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="cc411-306">Aggiungi *modelli/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="cc411-306">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="cc411-307">Impalcatura del `Contact` modello.</span><span class="sxs-lookup"><span data-stu-id="cc411-307">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="cc411-308">Creare la migrazione iniziale e aggiornare il database:</span><span class="sxs-lookup"><span data-stu-id="cc411-308">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="cc411-309">Se si verifica un bug con il `dotnet aspnet-codegenerator razorpage` comando, vedere [questo problema di GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="cc411-309">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="cc411-310">Aggiornare l'ancoraggio **ContactManager** nel file *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="cc411-310">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="cc411-311">Testare l'app creando, modificando ed eliminando un contatto</span><span class="sxs-lookup"><span data-stu-id="cc411-311">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="cc411-312">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="cc411-312">Seed the database</span></span>

<span data-ttu-id="cc411-313">Aggiungere la classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) alla cartella *Data* :</span><span class="sxs-lookup"><span data-stu-id="cc411-313">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="cc411-314">Chiama `SeedData.Initialize` da `Main` :</span><span class="sxs-lookup"><span data-stu-id="cc411-314">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="cc411-315">Verificare che l'app abbia sottoposta a seeding il database.</span><span class="sxs-lookup"><span data-stu-id="cc411-315">Test that the app seeded the database.</span></span> <span data-ttu-id="cc411-316">Se sono presenti righe nel database Contact, il metodo seed non viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="cc411-316">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="cc411-317">Questa esercitazione illustra come creare un'app Web di ASP.NET Core con i dati utente protetti dall'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="cc411-317">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="cc411-318">Viene visualizzato un elenco di contatti creati dagli utenti autenticati (registrati).</span><span class="sxs-lookup"><span data-stu-id="cc411-318">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="cc411-319">Sono disponibili tre gruppi di sicurezza:</span><span class="sxs-lookup"><span data-stu-id="cc411-319">There are three security groups:</span></span>

* <span data-ttu-id="cc411-320">**Gli utenti registrati** possono visualizzare tutti i dati approvati e possono modificare/eliminare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="cc411-320">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="cc411-321">I **responsabili** possono approvare o rifiutare i dati di contatto.</span><span class="sxs-lookup"><span data-stu-id="cc411-321">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="cc411-322">Solo i contatti approvati sono visibili agli utenti.</span><span class="sxs-lookup"><span data-stu-id="cc411-322">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="cc411-323">Gli **amministratori** possono approvare/rifiutare e modificare/eliminare i dati.</span><span class="sxs-lookup"><span data-stu-id="cc411-323">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="cc411-324">Nell'immagine seguente, l'utente Rick ( `rick@example.com` ) ha eseguito l'accesso.</span><span class="sxs-lookup"><span data-stu-id="cc411-324">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="cc411-325">Rick può visualizzare solo i contatti approvati e **modificare** / **Delete** / **Crea nuovi** collegamenti per i contatti.</span><span class="sxs-lookup"><span data-stu-id="cc411-325">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="cc411-326">Solo l'ultimo record, creato da Rick, Visualizza i collegamenti **modifica** ed **Elimina** .</span><span class="sxs-lookup"><span data-stu-id="cc411-326">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="cc411-327">Gli altri utenti non vedranno l'ultimo record fino a quando un responsabile o un amministratore non modifica lo stato in "approvato".</span><span class="sxs-lookup"><span data-stu-id="cc411-327">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot che illustra l'accesso a Rick](secure-data/_static/rick.png)

<span data-ttu-id="cc411-329">Nell'immagine seguente, `manager@contoso.com` è stato eseguito l'accesso e nel ruolo del Manager:</span><span class="sxs-lookup"><span data-stu-id="cc411-329">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot che mostra l' manager@contoso.com accesso](secure-data/_static/manager1.png)

<span data-ttu-id="cc411-331">Nella figura seguente viene illustrata la visualizzazione dettagli Manager di un contatto:</span><span class="sxs-lookup"><span data-stu-id="cc411-331">The following image shows the managers details view of a contact:</span></span>

![Visualizzazione del contatto da un responsabile](secure-data/_static/manager.png)

<span data-ttu-id="cc411-333">I pulsanti **approva** e **rifiuta** vengono visualizzati solo per Manager e amministratori.</span><span class="sxs-lookup"><span data-stu-id="cc411-333">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="cc411-334">Nell'immagine seguente, `admin@contoso.com` è stato eseguito l'accesso e il ruolo dell'amministratore:</span><span class="sxs-lookup"><span data-stu-id="cc411-334">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot che mostra l' admin@contoso.com accesso](secure-data/_static/admin.png)

<span data-ttu-id="cc411-336">L'amministratore dispone di tutti i privilegi.</span><span class="sxs-lookup"><span data-stu-id="cc411-336">The administrator has all privileges.</span></span> <span data-ttu-id="cc411-337">Può leggere, modificare ed eliminare qualsiasi contatto e modificare lo stato dei contatti.</span><span class="sxs-lookup"><span data-stu-id="cc411-337">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="cc411-338">L'app è stata creata mediante l' [impalcatura](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) del `Contact` modello seguente:</span><span class="sxs-lookup"><span data-stu-id="cc411-338">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="cc411-339">L'esempio contiene i gestori di autorizzazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="cc411-339">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="cc411-340">`ContactIsOwnerAuthorizationHandler`: Assicura che un utente possa modificare solo i propri dati.</span><span class="sxs-lookup"><span data-stu-id="cc411-340">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="cc411-341">`ContactManagerAuthorizationHandler`: Consente ai responsabili di approvare o rifiutare i contatti.</span><span class="sxs-lookup"><span data-stu-id="cc411-341">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="cc411-342">`ContactAdministratorsAuthorizationHandler`: Consente agli amministratori di approvare o rifiutare contatti e di modificare/eliminare contatti.</span><span class="sxs-lookup"><span data-stu-id="cc411-342">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cc411-343">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="cc411-343">Prerequisites</span></span>

<span data-ttu-id="cc411-344">Questa esercitazione è avanzata.</span><span class="sxs-lookup"><span data-stu-id="cc411-344">This tutorial is advanced.</span></span> <span data-ttu-id="cc411-345">È necessario avere familiarità con:</span><span class="sxs-lookup"><span data-stu-id="cc411-345">You should be familiar with:</span></span>

* [<span data-ttu-id="cc411-346">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cc411-346">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="cc411-347">autenticazione</span><span class="sxs-lookup"><span data-stu-id="cc411-347">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="cc411-348">Conferma account e recupero password</span><span class="sxs-lookup"><span data-stu-id="cc411-348">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="cc411-349">Autorizzazione</span><span class="sxs-lookup"><span data-stu-id="cc411-349">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="cc411-350">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="cc411-350">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="cc411-351">App iniziale e completata</span><span class="sxs-lookup"><span data-stu-id="cc411-351">The starter and completed app</span></span>

<span data-ttu-id="cc411-352">[Scaricare](xref:index#how-to-download-a-sample) l'app [completata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="cc411-352">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="cc411-353">[Testare](#test-the-completed-app) l'app completata in modo da acquisire familiarità con le funzionalità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="cc411-353">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="cc411-354">App iniziale</span><span class="sxs-lookup"><span data-stu-id="cc411-354">The starter app</span></span>

<span data-ttu-id="cc411-355">[Scaricare](xref:index#how-to-download-a-sample) l'app [Starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="cc411-355">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="cc411-356">Eseguire l'app, toccare il collegamento **ContactManager** e verificare che sia possibile creare, modificare ed eliminare un contatto.</span><span class="sxs-lookup"><span data-stu-id="cc411-356">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="cc411-357">Proteggere i dati utente</span><span class="sxs-lookup"><span data-stu-id="cc411-357">Secure user data</span></span>

<span data-ttu-id="cc411-358">Le sezioni seguenti includono tutti i passaggi principali per creare l'app Secure User Data.</span><span class="sxs-lookup"><span data-stu-id="cc411-358">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="cc411-359">Può risultare utile fare riferimento al progetto completato.</span><span class="sxs-lookup"><span data-stu-id="cc411-359">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="cc411-360">Associare i dati di contatto all'utente</span><span class="sxs-lookup"><span data-stu-id="cc411-360">Tie the contact data to the user</span></span>

<span data-ttu-id="cc411-361">Usare l' [Identity](xref:security/authentication/identity) ID utente ASP.NET per assicurarsi che gli utenti possano modificare i dati, ma non i dati di altri utenti.</span><span class="sxs-lookup"><span data-stu-id="cc411-361">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="cc411-362">Aggiungere `OwnerID` e `ContactStatus` al `Contact` modello:</span><span class="sxs-lookup"><span data-stu-id="cc411-362">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="cc411-363">`OwnerID` ID dell'utente della `AspNetUser` tabella nel [Identity](xref:security/authentication/identity) database.</span><span class="sxs-lookup"><span data-stu-id="cc411-363">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="cc411-364">Il `Status` campo determina se un contatto è visualizzabile dagli utenti generali.</span><span class="sxs-lookup"><span data-stu-id="cc411-364">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="cc411-365">Creare una nuova migrazione e aggiornare il database:</span><span class="sxs-lookup"><span data-stu-id="cc411-365">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="cc411-366">Aggiungere servizi ruolo a Identity</span><span class="sxs-lookup"><span data-stu-id="cc411-366">Add Role services to Identity</span></span>

<span data-ttu-id="cc411-367">Aggiungere [Aggiungi ruoli](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) per aggiungere servizi ruolo:</span><span class="sxs-lookup"><span data-stu-id="cc411-367">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="cc411-368">Richiedi utenti autenticati</span><span class="sxs-lookup"><span data-stu-id="cc411-368">Require authenticated users</span></span>

<span data-ttu-id="cc411-369">Impostare i criteri di autenticazione predefiniti per richiedere l'autenticazione degli utenti:</span><span class="sxs-lookup"><span data-stu-id="cc411-369">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="cc411-370">È possibile rifiutare esplicitamente l'autenticazione a Razor livello di pagina, controller o metodo di azione con l' `[AllowAnonymous]` attributo.</span><span class="sxs-lookup"><span data-stu-id="cc411-370">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="cc411-371">L'impostazione dei criteri di autenticazione predefiniti per richiedere l'autenticazione degli utenti consente di proteggere le Razor pagine e i controller appena aggiunti.</span><span class="sxs-lookup"><span data-stu-id="cc411-371">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="cc411-372">La necessità di eseguire l'autenticazione per impostazione predefinita è più sicura rispetto a quella di fare affidamento su nuovi controller e Razor pagine per includere l' `[Authorize]` attributo.</span><span class="sxs-lookup"><span data-stu-id="cc411-372">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="cc411-373">Aggiungere [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) alle pagine di indice, informazioni e contatti in modo che gli utenti anonimi possano ottenere informazioni sul sito prima della registrazione.</span><span class="sxs-lookup"><span data-stu-id="cc411-373">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="cc411-374">Configurare l'account di test</span><span class="sxs-lookup"><span data-stu-id="cc411-374">Configure the test account</span></span>

<span data-ttu-id="cc411-375">La `SeedData` classe crea due account: Administrator e Manager.</span><span class="sxs-lookup"><span data-stu-id="cc411-375">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="cc411-376">Utilizzare lo [strumento Gestione segreta](xref:security/app-secrets) per impostare una password per questi account.</span><span class="sxs-lookup"><span data-stu-id="cc411-376">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="cc411-377">Impostare la password dalla directory del progetto (la directory contenente *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="cc411-377">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="cc411-378">Se non si specifica una password complessa, viene generata un'eccezione quando `SeedData.Initialize` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="cc411-378">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="cc411-379">Aggiornare `Main` per usare la password di test:</span><span class="sxs-lookup"><span data-stu-id="cc411-379">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="cc411-380">Creare gli account di test e aggiornare i contatti</span><span class="sxs-lookup"><span data-stu-id="cc411-380">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="cc411-381">Aggiornare il `Initialize` metodo nella `SeedData` classe per creare gli account di test:</span><span class="sxs-lookup"><span data-stu-id="cc411-381">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="cc411-382">Aggiungere l'ID utente amministratore e `ContactStatus` ai contatti.</span><span class="sxs-lookup"><span data-stu-id="cc411-382">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="cc411-383">Creare uno dei contatti "inviato" e uno "rifiutato".</span><span class="sxs-lookup"><span data-stu-id="cc411-383">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="cc411-384">Aggiungere l'ID utente e lo stato a tutti i contatti.</span><span class="sxs-lookup"><span data-stu-id="cc411-384">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="cc411-385">Viene visualizzato un solo contatto:</span><span class="sxs-lookup"><span data-stu-id="cc411-385">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="cc411-386">Creazione di gestori di autorizzazioni proprietario, Manager e amministratore</span><span class="sxs-lookup"><span data-stu-id="cc411-386">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="cc411-387">Creare una cartella di *autorizzazione* e crearvi una `ContactIsOwnerAuthorizationHandler` classe.</span><span class="sxs-lookup"><span data-stu-id="cc411-387">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="cc411-388">`ContactIsOwnerAuthorizationHandler`Verifica che l'utente che agisce su una risorsa appartenga alla risorsa.</span><span class="sxs-lookup"><span data-stu-id="cc411-388">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="cc411-389">`ContactIsOwnerAuthorizationHandler`Contesto delle chiamate [. Ha esito positivo](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se l'utente autenticato corrente è il proprietario del contatto.</span><span class="sxs-lookup"><span data-stu-id="cc411-389">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="cc411-390">Gestori autorizzazioni in genere:</span><span class="sxs-lookup"><span data-stu-id="cc411-390">Authorization handlers generally:</span></span>

* <span data-ttu-id="cc411-391">Restituisce `context.Succeed` quando vengono soddisfatti i requisiti.</span><span class="sxs-lookup"><span data-stu-id="cc411-391">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="cc411-392">Restituisce `Task.CompletedTask` quando i requisiti non vengono soddisfatti.</span><span class="sxs-lookup"><span data-stu-id="cc411-392">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="cc411-393">`Task.CompletedTask` non ha esito positivo o negativo &mdash; consente l'esecuzione di altri gestori di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="cc411-393">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="cc411-394">Se è necessario eseguire in modo esplicito l'errore, restituire [context. Esito negativo](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="cc411-394">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="cc411-395">L'app consente ai proprietari dei contatti di modificare/eliminare/creare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="cc411-395">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="cc411-396">`ContactIsOwnerAuthorizationHandler` non è necessario controllare l'operazione passata nel parametro requirement.</span><span class="sxs-lookup"><span data-stu-id="cc411-396">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="cc411-397">Creazione di un gestore autorizzazioni di gestione</span><span class="sxs-lookup"><span data-stu-id="cc411-397">Create a manager authorization handler</span></span>

<span data-ttu-id="cc411-398">Creare una `ContactManagerAuthorizationHandler` classe nella cartella *authorization* .</span><span class="sxs-lookup"><span data-stu-id="cc411-398">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="cc411-399">`ContactManagerAuthorizationHandler`Verifica che l'utente che agisce sulla risorsa sia un responsabile.</span><span class="sxs-lookup"><span data-stu-id="cc411-399">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="cc411-400">Solo i responsabili possono approvare o rifiutare le modifiche al contenuto (nuove o modificate).</span><span class="sxs-lookup"><span data-stu-id="cc411-400">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="cc411-401">Creazione di un gestore autorizzazioni Amministratore</span><span class="sxs-lookup"><span data-stu-id="cc411-401">Create an administrator authorization handler</span></span>

<span data-ttu-id="cc411-402">Creare una `ContactAdministratorsAuthorizationHandler` classe nella cartella *authorization* .</span><span class="sxs-lookup"><span data-stu-id="cc411-402">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="cc411-403">Il `ContactAdministratorsAuthorizationHandler` Verifica che l'utente che agisce sulla risorsa sia un amministratore.</span><span class="sxs-lookup"><span data-stu-id="cc411-403">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="cc411-404">L'amministratore può eseguire tutte le operazioni.</span><span class="sxs-lookup"><span data-stu-id="cc411-404">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="cc411-405">Registrare i gestori di autorizzazioni</span><span class="sxs-lookup"><span data-stu-id="cc411-405">Register the authorization handlers</span></span>

<span data-ttu-id="cc411-406">I servizi che usano Entity Framework Core devono essere registrati per l' [inserimento di dipendenze](xref:fundamentals/dependency-injection) usando [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="cc411-406">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="cc411-407">`ContactIsOwnerAuthorizationHandler`Usa ASP.NET Core [Identity](xref:security/authentication/identity) , che è basato su Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="cc411-407">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="cc411-408">Registrare i gestori con la raccolta di servizi in modo che siano disponibili per `ContactsController` tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cc411-408">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="cc411-409">Aggiungere il codice seguente alla fine di `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cc411-409">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="cc411-410">`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` vengono aggiunti come singleton.</span><span class="sxs-lookup"><span data-stu-id="cc411-410">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="cc411-411">Si tratta di singleton perché non usano EF e tutte le informazioni necessarie si trovano nel `Context` parametro del `HandleRequirementAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="cc411-411">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="cc411-412">Autorizzazione supporto</span><span class="sxs-lookup"><span data-stu-id="cc411-412">Support authorization</span></span>

<span data-ttu-id="cc411-413">In questa sezione si aggiornano le Razor pagine e si aggiunge una classe dei requisiti delle operazioni.</span><span class="sxs-lookup"><span data-stu-id="cc411-413">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="cc411-414">Esaminare la classe dei requisiti delle operazioni di contatto</span><span class="sxs-lookup"><span data-stu-id="cc411-414">Review the contact operations requirements class</span></span>

<span data-ttu-id="cc411-415">Esaminare la `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="cc411-415">Review the `ContactOperations` class.</span></span> <span data-ttu-id="cc411-416">Questa classe contiene i requisiti supportati dall'app:</span><span class="sxs-lookup"><span data-stu-id="cc411-416">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="cc411-417">Creare una classe di base per le Razor pagine contatti</span><span class="sxs-lookup"><span data-stu-id="cc411-417">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="cc411-418">Creare una classe di base che contiene i servizi utilizzati nelle Razor pagine contatti.</span><span class="sxs-lookup"><span data-stu-id="cc411-418">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="cc411-419">La classe base inserisce il codice di inizializzazione in un'unica posizione:</span><span class="sxs-lookup"><span data-stu-id="cc411-419">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="cc411-420">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="cc411-420">The preceding code:</span></span>

* <span data-ttu-id="cc411-421">Aggiunge il `IAuthorizationService` servizio per accedere ai gestori di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="cc411-421">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="cc411-422">Aggiunge il Identity `UserManager` servizio.</span><span class="sxs-lookup"><span data-stu-id="cc411-422">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="cc411-423">Aggiungere l'oggetto `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="cc411-423">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="cc411-424">Aggiornare CreateModel</span><span class="sxs-lookup"><span data-stu-id="cc411-424">Update the CreateModel</span></span>

<span data-ttu-id="cc411-425">Aggiornare il costruttore del modello di pagina create per usare la `DI_BasePageModel` classe di base:</span><span class="sxs-lookup"><span data-stu-id="cc411-425">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="cc411-426">Aggiornare il `CreateModel.OnPostAsync` metodo a:</span><span class="sxs-lookup"><span data-stu-id="cc411-426">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="cc411-427">Aggiungere l'ID utente al `Contact` modello.</span><span class="sxs-lookup"><span data-stu-id="cc411-427">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="cc411-428">Chiamare il gestore autorizzazioni per verificare che l'utente disponga dell'autorizzazione per la creazione di contatti.</span><span class="sxs-lookup"><span data-stu-id="cc411-428">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="cc411-429">Aggiornare IndexModel</span><span class="sxs-lookup"><span data-stu-id="cc411-429">Update the IndexModel</span></span>

<span data-ttu-id="cc411-430">Aggiornare il `OnGetAsync` metodo in modo da visualizzare solo i contatti approvati per gli utenti generali:</span><span class="sxs-lookup"><span data-stu-id="cc411-430">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="cc411-431">Aggiornare EditModel</span><span class="sxs-lookup"><span data-stu-id="cc411-431">Update the EditModel</span></span>

<span data-ttu-id="cc411-432">Aggiungere un gestore autorizzazioni per verificare che l'utente sia proprietario del contatto.</span><span class="sxs-lookup"><span data-stu-id="cc411-432">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="cc411-433">Poiché l'autorizzazione della risorsa viene convalidata, l' `[Authorize]` attributo non è sufficiente.</span><span class="sxs-lookup"><span data-stu-id="cc411-433">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="cc411-434">L'app non ha accesso alla risorsa quando vengono valutati gli attributi.</span><span class="sxs-lookup"><span data-stu-id="cc411-434">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="cc411-435">L'autorizzazione basata sulle risorse deve essere imperativa.</span><span class="sxs-lookup"><span data-stu-id="cc411-435">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="cc411-436">I controlli devono essere eseguiti una volta che l'app può accedere alla risorsa, eseguendo il caricamento nel modello di pagina o caricando l'app all'interno del gestore stesso.</span><span class="sxs-lookup"><span data-stu-id="cc411-436">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="cc411-437">Si accede spesso alla risorsa passando la chiave della risorsa.</span><span class="sxs-lookup"><span data-stu-id="cc411-437">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="cc411-438">Aggiornare DeleteModel</span><span class="sxs-lookup"><span data-stu-id="cc411-438">Update the DeleteModel</span></span>

<span data-ttu-id="cc411-439">Aggiornare il modello di pagina Elimina per utilizzare il gestore autorizzazione per verificare che l'utente disponga dell'autorizzazione DELETE per il contatto.</span><span class="sxs-lookup"><span data-stu-id="cc411-439">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="cc411-440">Inserire il servizio di autorizzazione nelle viste</span><span class="sxs-lookup"><span data-stu-id="cc411-440">Inject the authorization service into the views</span></span>

<span data-ttu-id="cc411-441">Attualmente, l'interfaccia utente Mostra i collegamenti modifica ed Elimina per i contatti che non possono essere modificati dall'utente.</span><span class="sxs-lookup"><span data-stu-id="cc411-441">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="cc411-442">Inserire il servizio di autorizzazione nel file *views/_ViewImports. cshtml* in modo che sia disponibile per tutte le visualizzazioni:</span><span class="sxs-lookup"><span data-stu-id="cc411-442">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="cc411-443">Il markup precedente aggiunge diverse `using` istruzioni.</span><span class="sxs-lookup"><span data-stu-id="cc411-443">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="cc411-444">Aggiornare i collegamenti **Edit** ed **Delete** in *pages/Contacts/index. cshtml* in modo che vengano sottoposti a rendering solo per gli utenti con le autorizzazioni appropriate:</span><span class="sxs-lookup"><span data-stu-id="cc411-444">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="cc411-445">Nascondere i collegamenti da utenti che non dispongono dell'autorizzazione per modificare i dati non protegge l'app.</span><span class="sxs-lookup"><span data-stu-id="cc411-445">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="cc411-446">Nascondere i collegamenti rende l'app più intuitiva visualizzando solo i collegamenti validi.</span><span class="sxs-lookup"><span data-stu-id="cc411-446">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="cc411-447">Gli utenti possono hackerare gli URL generati per richiamare le operazioni di modifica ed eliminazione sui dati di cui non sono proprietari.</span><span class="sxs-lookup"><span data-stu-id="cc411-447">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="cc411-448">La Razor pagina o il controller deve applicare i controlli di accesso per proteggere i dati.</span><span class="sxs-lookup"><span data-stu-id="cc411-448">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="cc411-449">Dettagli aggiornamento</span><span class="sxs-lookup"><span data-stu-id="cc411-449">Update Details</span></span>

<span data-ttu-id="cc411-450">Aggiornare la visualizzazione dettagli in modo che i responsabili possano approvare o rifiutare i contatti:</span><span class="sxs-lookup"><span data-stu-id="cc411-450">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="cc411-451">Aggiornare il modello della pagina dei dettagli:</span><span class="sxs-lookup"><span data-stu-id="cc411-451">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="cc411-452">Aggiungere o rimuovere un utente in un ruolo</span><span class="sxs-lookup"><span data-stu-id="cc411-452">Add or remove a user to a role</span></span>

<span data-ttu-id="cc411-453">Per informazioni su, vedere [questo problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="cc411-453">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="cc411-454">Rimozione dei privilegi da un utente.</span><span class="sxs-lookup"><span data-stu-id="cc411-454">Removing privileges from a user.</span></span> <span data-ttu-id="cc411-455">Ad esempio, disattivare un utente in un'app di chat.</span><span class="sxs-lookup"><span data-stu-id="cc411-455">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="cc411-456">Aggiunta di privilegi a un utente.</span><span class="sxs-lookup"><span data-stu-id="cc411-456">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="cc411-457">Testare l'app completata</span><span class="sxs-lookup"><span data-stu-id="cc411-457">Test the completed app</span></span>

<span data-ttu-id="cc411-458">Se non è già stata impostata una password per gli account utente di cui è stato eseguito il seeding, usare lo [strumento Gestione segreta](xref:security/app-secrets#secret-manager) per impostare una password:</span><span class="sxs-lookup"><span data-stu-id="cc411-458">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="cc411-459">Scegliere una password complessa: usare otto o più caratteri e almeno un carattere maiuscolo, un numero e un simbolo.</span><span class="sxs-lookup"><span data-stu-id="cc411-459">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="cc411-460">Ad esempio, `Passw0rd!` soddisfa i requisiti per le password complesse.</span><span class="sxs-lookup"><span data-stu-id="cc411-460">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="cc411-461">Eseguire il comando seguente dalla cartella del progetto, dove `<PW>` è la password:</span><span class="sxs-lookup"><span data-stu-id="cc411-461">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="cc411-462">Eliminare e aggiornare il database</span><span class="sxs-lookup"><span data-stu-id="cc411-462">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="cc411-463">Riavviare l'app per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="cc411-463">Restart the app to seed the database.</span></span>

<span data-ttu-id="cc411-464">Un modo semplice per testare l'app completata consiste nell'avviare tre diversi browser (o sessioni in incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="cc411-464">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="cc411-465">In un browser registrare un nuovo utente (ad esempio, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="cc411-465">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="cc411-466">Accedere a ogni browser con un altro utente.</span><span class="sxs-lookup"><span data-stu-id="cc411-466">Sign in to each browser with a different user.</span></span> <span data-ttu-id="cc411-467">Verificare le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="cc411-467">Verify the following operations:</span></span>

* <span data-ttu-id="cc411-468">Gli utenti registrati possono visualizzare tutti i dati di contatto approvati.</span><span class="sxs-lookup"><span data-stu-id="cc411-468">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="cc411-469">Gli utenti registrati possono modificare/eliminare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="cc411-469">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="cc411-470">I responsabili possono approvare/rifiutare i dati di contatto.</span><span class="sxs-lookup"><span data-stu-id="cc411-470">Managers can approve/reject contact data.</span></span> <span data-ttu-id="cc411-471">La `Details` visualizzazione Mostra i pulsanti **approva** e **rifiuta** .</span><span class="sxs-lookup"><span data-stu-id="cc411-471">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="cc411-472">Gli amministratori possono approvare/rifiutare e modificare/eliminare tutti i dati.</span><span class="sxs-lookup"><span data-stu-id="cc411-472">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="cc411-473">User</span><span class="sxs-lookup"><span data-stu-id="cc411-473">User</span></span>                | <span data-ttu-id="cc411-474">Seeding dall'app</span><span class="sxs-lookup"><span data-stu-id="cc411-474">Seeded by the app</span></span> | <span data-ttu-id="cc411-475">Opzioni</span><span class="sxs-lookup"><span data-stu-id="cc411-475">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="cc411-476">No</span><span class="sxs-lookup"><span data-stu-id="cc411-476">No</span></span>                | <span data-ttu-id="cc411-477">Modificare/eliminare i dati personali.</span><span class="sxs-lookup"><span data-stu-id="cc411-477">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="cc411-478">Sì</span><span class="sxs-lookup"><span data-stu-id="cc411-478">Yes</span></span>               | <span data-ttu-id="cc411-479">Approva/rifiuta e modifica/elimina i dati personali.</span><span class="sxs-lookup"><span data-stu-id="cc411-479">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="cc411-480">Sì</span><span class="sxs-lookup"><span data-stu-id="cc411-480">Yes</span></span>               | <span data-ttu-id="cc411-481">Approva/rifiuta e modifica/elimina tutti i dati.</span><span class="sxs-lookup"><span data-stu-id="cc411-481">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="cc411-482">Creare un contatto nel browser dell'amministratore.</span><span class="sxs-lookup"><span data-stu-id="cc411-482">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="cc411-483">Copiare l'URL da eliminare e modificare dal contatto amministratore.</span><span class="sxs-lookup"><span data-stu-id="cc411-483">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="cc411-484">Incollare questi collegamenti nel browser dell'utente test per verificare che l'utente test non possa eseguire queste operazioni.</span><span class="sxs-lookup"><span data-stu-id="cc411-484">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="cc411-485">Creare l'app Starter</span><span class="sxs-lookup"><span data-stu-id="cc411-485">Create the starter app</span></span>

* <span data-ttu-id="cc411-486">Creare un' Razor app per le pagine denominata "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="cc411-486">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="cc411-487">Creare l'app con **singoli account utente**.</span><span class="sxs-lookup"><span data-stu-id="cc411-487">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="cc411-488">Denominarlo "ContactManager" in modo che lo spazio dei nomi corrisponda allo spazio dei nomi usato nell'esempio.</span><span class="sxs-lookup"><span data-stu-id="cc411-488">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="cc411-489">`-uld` Specifica il database locale anziché SQLite</span><span class="sxs-lookup"><span data-stu-id="cc411-489">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="cc411-490">Aggiungi *modelli/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="cc411-490">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="cc411-491">Impalcatura del `Contact` modello.</span><span class="sxs-lookup"><span data-stu-id="cc411-491">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="cc411-492">Creare la migrazione iniziale e aggiornare il database:</span><span class="sxs-lookup"><span data-stu-id="cc411-492">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="cc411-493">Aggiornare l'ancoraggio **ContactManager** nel file *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="cc411-493">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="cc411-494">Testare l'app creando, modificando ed eliminando un contatto</span><span class="sxs-lookup"><span data-stu-id="cc411-494">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="cc411-495">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="cc411-495">Seed the database</span></span>

<span data-ttu-id="cc411-496">Aggiungere la classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) alla cartella *Data* .</span><span class="sxs-lookup"><span data-stu-id="cc411-496">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="cc411-497">Chiama `SeedData.Initialize` da `Main` :</span><span class="sxs-lookup"><span data-stu-id="cc411-497">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="cc411-498">Verificare che l'app abbia sottoposta a seeding il database.</span><span class="sxs-lookup"><span data-stu-id="cc411-498">Test that the app seeded the database.</span></span> <span data-ttu-id="cc411-499">Se sono presenti righe nel database Contact, il metodo seed non viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="cc411-499">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="cc411-500">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="cc411-500">Additional resources</span></span>

* [<span data-ttu-id="cc411-501">Creare un'app Web .NET Core e database SQL nel Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="cc411-501">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="cc411-502">[ASP.NET Core Lab di autorizzazione](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="cc411-502">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="cc411-503">Questo Lab illustra in modo più dettagliato le funzionalità di sicurezza introdotte in questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="cc411-503">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="cc411-504">Autorizzazione personalizzata basata su criteri</span><span class="sxs-lookup"><span data-stu-id="cc411-504">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
