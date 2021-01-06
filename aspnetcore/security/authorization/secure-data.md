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
ms.openlocfilehash: dc70cfe7cb0c0f044f5f1e7ee68a293b3ea7507f
ms.sourcegitcommit: 04a404a9655c59ad1ea02aff5d399ae1b833ad6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854652"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="1417b-104">Creare un'app Web di ASP.NET Core con i dati utente protetti dall'autorizzazione</span><span class="sxs-lookup"><span data-stu-id="1417b-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="1417b-105">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="1417b-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="1417b-106">Vedi [questo PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="1417b-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1417b-107">Questa esercitazione illustra come creare un'app Web di ASP.NET Core con i dati utente protetti dall'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="1417b-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="1417b-108">Viene visualizzato un elenco di contatti creati dagli utenti autenticati (registrati).</span><span class="sxs-lookup"><span data-stu-id="1417b-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="1417b-109">Sono disponibili tre gruppi di sicurezza:</span><span class="sxs-lookup"><span data-stu-id="1417b-109">There are three security groups:</span></span>

* <span data-ttu-id="1417b-110">**Gli utenti registrati** possono visualizzare tutti i dati approvati e possono modificare/eliminare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="1417b-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="1417b-111">I **responsabili** possono approvare o rifiutare i dati di contatto.</span><span class="sxs-lookup"><span data-stu-id="1417b-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="1417b-112">Solo i contatti approvati sono visibili agli utenti.</span><span class="sxs-lookup"><span data-stu-id="1417b-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="1417b-113">Gli **amministratori** possono approvare/rifiutare e modificare/eliminare i dati.</span><span class="sxs-lookup"><span data-stu-id="1417b-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="1417b-114">Le immagini in questo documento non corrispondono esattamente ai modelli più recenti.</span><span class="sxs-lookup"><span data-stu-id="1417b-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="1417b-115">Nell'immagine seguente, l'utente Rick ( `rick@example.com` ) ha eseguito l'accesso.</span><span class="sxs-lookup"><span data-stu-id="1417b-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="1417b-116">Rick può visualizzare solo i contatti approvati e **modificare** / **Delete** / **Crea nuovi** collegamenti per i contatti.</span><span class="sxs-lookup"><span data-stu-id="1417b-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="1417b-117">Solo l'ultimo record, creato da Rick, Visualizza i collegamenti **modifica** ed **Elimina** .</span><span class="sxs-lookup"><span data-stu-id="1417b-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="1417b-118">Gli altri utenti non vedranno l'ultimo record fino a quando un responsabile o un amministratore non modifica lo stato in "approvato".</span><span class="sxs-lookup"><span data-stu-id="1417b-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot che illustra l'accesso a Rick](secure-data/_static/rick.png)

<span data-ttu-id="1417b-120">Nell'immagine seguente, `manager@contoso.com` è stato eseguito l'accesso e nel ruolo del Manager:</span><span class="sxs-lookup"><span data-stu-id="1417b-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot che mostra l' manager@contoso.com accesso](secure-data/_static/manager1.png)

<span data-ttu-id="1417b-122">Nella figura seguente viene illustrata la visualizzazione dettagli Manager di un contatto:</span><span class="sxs-lookup"><span data-stu-id="1417b-122">The following image shows the managers details view of a contact:</span></span>

![Visualizzazione del contatto da un responsabile](secure-data/_static/manager.png)

<span data-ttu-id="1417b-124">I pulsanti **approva** e **rifiuta** vengono visualizzati solo per Manager e amministratori.</span><span class="sxs-lookup"><span data-stu-id="1417b-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="1417b-125">Nell'immagine seguente, `admin@contoso.com` è stato eseguito l'accesso e il ruolo dell'amministratore:</span><span class="sxs-lookup"><span data-stu-id="1417b-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot che mostra l' admin@contoso.com accesso](secure-data/_static/admin.png)

<span data-ttu-id="1417b-127">L'amministratore dispone di tutti i privilegi.</span><span class="sxs-lookup"><span data-stu-id="1417b-127">The administrator has all privileges.</span></span> <span data-ttu-id="1417b-128">Può leggere, modificare ed eliminare qualsiasi contatto e modificare lo stato dei contatti.</span><span class="sxs-lookup"><span data-stu-id="1417b-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="1417b-129">L'app è stata creata mediante l' [impalcatura](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) del `Contact` modello seguente:</span><span class="sxs-lookup"><span data-stu-id="1417b-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="1417b-130">L'esempio contiene i gestori di autorizzazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="1417b-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="1417b-131">`ContactIsOwnerAuthorizationHandler`: Assicura che un utente possa modificare solo i propri dati.</span><span class="sxs-lookup"><span data-stu-id="1417b-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="1417b-132">`ContactManagerAuthorizationHandler`: Consente ai responsabili di approvare o rifiutare i contatti.</span><span class="sxs-lookup"><span data-stu-id="1417b-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="1417b-133">`ContactAdministratorsAuthorizationHandler`: Consente agli amministratori di approvare o rifiutare contatti e di modificare/eliminare contatti.</span><span class="sxs-lookup"><span data-stu-id="1417b-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1417b-134">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="1417b-134">Prerequisites</span></span>

<span data-ttu-id="1417b-135">Questa esercitazione è avanzata.</span><span class="sxs-lookup"><span data-stu-id="1417b-135">This tutorial is advanced.</span></span> <span data-ttu-id="1417b-136">È necessario avere familiarità con:</span><span class="sxs-lookup"><span data-stu-id="1417b-136">You should be familiar with:</span></span>

* [<span data-ttu-id="1417b-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1417b-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="1417b-138">autenticazione</span><span class="sxs-lookup"><span data-stu-id="1417b-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="1417b-139">Conferma account e recupero password</span><span class="sxs-lookup"><span data-stu-id="1417b-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="1417b-140">Autorizzazione</span><span class="sxs-lookup"><span data-stu-id="1417b-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="1417b-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="1417b-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="1417b-142">App iniziale e completata</span><span class="sxs-lookup"><span data-stu-id="1417b-142">The starter and completed app</span></span>

<span data-ttu-id="1417b-143">[Scaricare](xref:index#how-to-download-a-sample) l'app [completata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="1417b-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="1417b-144">[Testare](#test-the-completed-app) l'app completata in modo da acquisire familiarità con le funzionalità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="1417b-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="1417b-145">App iniziale</span><span class="sxs-lookup"><span data-stu-id="1417b-145">The starter app</span></span>

<span data-ttu-id="1417b-146">[Scaricare](xref:index#how-to-download-a-sample) l'app [Starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="1417b-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="1417b-147">Eseguire l'app, toccare il collegamento **ContactManager** e verificare che sia possibile creare, modificare ed eliminare un contatto.</span><span class="sxs-lookup"><span data-stu-id="1417b-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span> <span data-ttu-id="1417b-148">Per creare l'app iniziale, vedere [creare l'app iniziale](#create-the-starter-app).</span><span class="sxs-lookup"><span data-stu-id="1417b-148">To create the starter app, see [Create the starter app](#create-the-starter-app).</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="1417b-149">Proteggere i dati utente</span><span class="sxs-lookup"><span data-stu-id="1417b-149">Secure user data</span></span>

<span data-ttu-id="1417b-150">Le sezioni seguenti includono tutti i passaggi principali per creare l'app Secure User Data.</span><span class="sxs-lookup"><span data-stu-id="1417b-150">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="1417b-151">Può risultare utile fare riferimento al progetto completato.</span><span class="sxs-lookup"><span data-stu-id="1417b-151">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="1417b-152">Associare i dati di contatto all'utente</span><span class="sxs-lookup"><span data-stu-id="1417b-152">Tie the contact data to the user</span></span>

<span data-ttu-id="1417b-153">Usare l' [Identity](xref:security/authentication/identity) ID utente ASP.NET per assicurarsi che gli utenti possano modificare i dati, ma non i dati di altri utenti.</span><span class="sxs-lookup"><span data-stu-id="1417b-153">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="1417b-154">Aggiungere `OwnerID` e `ContactStatus` al `Contact` modello:</span><span class="sxs-lookup"><span data-stu-id="1417b-154">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="1417b-155">`OwnerID` ID dell'utente della `AspNetUser` tabella nel [Identity](xref:security/authentication/identity) database.</span><span class="sxs-lookup"><span data-stu-id="1417b-155">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="1417b-156">Il `Status` campo determina se un contatto è visualizzabile dagli utenti generali.</span><span class="sxs-lookup"><span data-stu-id="1417b-156">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="1417b-157">Creare una nuova migrazione e aggiornare il database:</span><span class="sxs-lookup"><span data-stu-id="1417b-157">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="1417b-158">Aggiungere servizi ruolo a Identity</span><span class="sxs-lookup"><span data-stu-id="1417b-158">Add Role services to Identity</span></span>

<span data-ttu-id="1417b-159">Aggiungere [Aggiungi ruoli](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) per aggiungere servizi ruolo:</span><span class="sxs-lookup"><span data-stu-id="1417b-159">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="1417b-160">Richiedi utenti autenticati</span><span class="sxs-lookup"><span data-stu-id="1417b-160">Require authenticated users</span></span>

<span data-ttu-id="1417b-161">Impostare i criteri di autenticazione di fallback per richiedere l'autenticazione degli utenti:</span><span class="sxs-lookup"><span data-stu-id="1417b-161">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="1417b-162">Il codice evidenziato precedente imposta i [criteri di autenticazione di fallback](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="1417b-162">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="1417b-163">I criteri di autenticazione di fallback richiedono che *_tutti_* gli utenti siano autenticati, ad eccezione di Razor pagine, controller o metodi di azione con un attributo di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="1417b-163">The fallback authentication policy requires \**_all_* _ users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="1417b-164">Ad esempio, Razor pagine, controller o metodi di azione con `[AllowAnonymous]` o `[Authorize(PolicyName="MyPolicy")]` utilizzano l'attributo di autenticazione applicato anziché i criteri di autenticazione di fallback.</span><span class="sxs-lookup"><span data-stu-id="1417b-164">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="1417b-165">I criteri di autenticazione di fallback:</span><span class="sxs-lookup"><span data-stu-id="1417b-165">The fallback authentication policy:</span></span>

<span data-ttu-id="1417b-166">_ Viene applicato a tutte le richieste che non specificano in modo esplicito i criteri di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="1417b-166">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="1417b-167">Per le richieste gestite dal routing degli endpoint, questo includerebbe qualsiasi endpoint che non specifichi un attributo di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="1417b-167">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="1417b-168">Per le richieste gestite da altri middleware dopo il middleware di autorizzazione, ad esempio [i file statici](xref:fundamentals/static-files), il criterio viene applicato a tutte le richieste.</span><span class="sxs-lookup"><span data-stu-id="1417b-168">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="1417b-169">L'impostazione dei criteri di autenticazione di fallback per richiedere l'autenticazione degli utenti consente di proteggere le Razor pagine e i controller appena aggiunti.</span><span class="sxs-lookup"><span data-stu-id="1417b-169">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="1417b-170">La necessità di eseguire l'autenticazione per impostazione predefinita è più sicura rispetto a quella di fare affidamento su nuovi controller e Razor pagine per includere l' `[Authorize]` attributo.</span><span class="sxs-lookup"><span data-stu-id="1417b-170">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="1417b-171">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> classe contiene anche <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="1417b-171">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="1417b-172">`DefaultPolicy`È il criterio utilizzato con l' `[Authorize]` attributo quando non è specificato alcun criterio.</span><span class="sxs-lookup"><span data-stu-id="1417b-172">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="1417b-173">`[Authorize]` non contiene un criterio denominato, a differenza di `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="1417b-173">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="1417b-174">Per ulteriori informazioni sui criteri, vedere <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="1417b-174">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="1417b-175">Un metodo alternativo per i controller e Razor le pagine MVC per richiedere che tutti gli utenti siano autenticati è l'aggiunta di un filtro di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="1417b-175">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="1417b-176">Il codice precedente usa un filtro di autorizzazione, l'impostazione dei criteri di fallback usa il routing degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="1417b-176">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="1417b-177">L'impostazione dei criteri di fallback è il modo migliore per richiedere l'autenticazione di tutti gli utenti.</span><span class="sxs-lookup"><span data-stu-id="1417b-177">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="1417b-178">Aggiungere [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) alle `Index` pagine e in `Privacy` modo che gli utenti anonimi possano ottenere informazioni sul sito prima della registrazione:</span><span class="sxs-lookup"><span data-stu-id="1417b-178">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="1417b-179">Configurare l'account di test</span><span class="sxs-lookup"><span data-stu-id="1417b-179">Configure the test account</span></span>

<span data-ttu-id="1417b-180">La `SeedData` classe crea due account: Administrator e Manager.</span><span class="sxs-lookup"><span data-stu-id="1417b-180">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="1417b-181">Utilizzare lo [strumento Gestione segreta](xref:security/app-secrets) per impostare una password per questi account.</span><span class="sxs-lookup"><span data-stu-id="1417b-181">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="1417b-182">Impostare la password dalla directory del progetto (la directory contenente *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="1417b-182">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="1417b-183">Se non si specifica una password complessa, viene generata un'eccezione quando `SeedData.Initialize` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="1417b-183">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="1417b-184">Aggiornare `Main` per usare la password di test:</span><span class="sxs-lookup"><span data-stu-id="1417b-184">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="1417b-185">Creare gli account di test e aggiornare i contatti</span><span class="sxs-lookup"><span data-stu-id="1417b-185">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="1417b-186">Aggiornare il `Initialize` metodo nella `SeedData` classe per creare gli account di test:</span><span class="sxs-lookup"><span data-stu-id="1417b-186">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="1417b-187">Aggiungere l'ID utente amministratore e `ContactStatus` ai contatti.</span><span class="sxs-lookup"><span data-stu-id="1417b-187">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="1417b-188">Creare uno dei contatti "inviato" e uno "rifiutato".</span><span class="sxs-lookup"><span data-stu-id="1417b-188">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="1417b-189">Aggiungere l'ID utente e lo stato a tutti i contatti.</span><span class="sxs-lookup"><span data-stu-id="1417b-189">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="1417b-190">Viene visualizzato un solo contatto:</span><span class="sxs-lookup"><span data-stu-id="1417b-190">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="1417b-191">Creazione di gestori di autorizzazioni proprietario, Manager e amministratore</span><span class="sxs-lookup"><span data-stu-id="1417b-191">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="1417b-192">Creare una `ContactIsOwnerAuthorizationHandler` classe nella cartella *authorization* .</span><span class="sxs-lookup"><span data-stu-id="1417b-192">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="1417b-193">`ContactIsOwnerAuthorizationHandler`Verifica che l'utente che agisce su una risorsa appartenga alla risorsa.</span><span class="sxs-lookup"><span data-stu-id="1417b-193">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="1417b-194">`ContactIsOwnerAuthorizationHandler`Contesto delle chiamate [. Ha esito positivo](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se l'utente autenticato corrente è il proprietario del contatto.</span><span class="sxs-lookup"><span data-stu-id="1417b-194">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="1417b-195">Gestori autorizzazioni in genere:</span><span class="sxs-lookup"><span data-stu-id="1417b-195">Authorization handlers generally:</span></span>

* <span data-ttu-id="1417b-196">Restituisce `context.Succeed` quando vengono soddisfatti i requisiti.</span><span class="sxs-lookup"><span data-stu-id="1417b-196">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="1417b-197">Restituisce `Task.CompletedTask` quando i requisiti non vengono soddisfatti.</span><span class="sxs-lookup"><span data-stu-id="1417b-197">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="1417b-198">`Task.CompletedTask` non ha esito positivo o negativo &mdash; consente l'esecuzione di altri gestori di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="1417b-198">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="1417b-199">Se è necessario eseguire in modo esplicito l'errore, restituire [context. Esito negativo](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="1417b-199">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="1417b-200">L'app consente ai proprietari dei contatti di modificare/eliminare/creare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="1417b-200">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="1417b-201">`ContactIsOwnerAuthorizationHandler` non è necessario controllare l'operazione passata nel parametro requirement.</span><span class="sxs-lookup"><span data-stu-id="1417b-201">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="1417b-202">Creazione di un gestore autorizzazioni di gestione</span><span class="sxs-lookup"><span data-stu-id="1417b-202">Create a manager authorization handler</span></span>

<span data-ttu-id="1417b-203">Creare una `ContactManagerAuthorizationHandler` classe nella cartella *authorization* .</span><span class="sxs-lookup"><span data-stu-id="1417b-203">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="1417b-204">`ContactManagerAuthorizationHandler`Verifica che l'utente che agisce sulla risorsa sia un responsabile.</span><span class="sxs-lookup"><span data-stu-id="1417b-204">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="1417b-205">Solo i responsabili possono approvare o rifiutare le modifiche al contenuto (nuove o modificate).</span><span class="sxs-lookup"><span data-stu-id="1417b-205">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="1417b-206">Creazione di un gestore autorizzazioni Amministratore</span><span class="sxs-lookup"><span data-stu-id="1417b-206">Create an administrator authorization handler</span></span>

<span data-ttu-id="1417b-207">Creare una `ContactAdministratorsAuthorizationHandler` classe nella cartella *authorization* .</span><span class="sxs-lookup"><span data-stu-id="1417b-207">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="1417b-208">Il `ContactAdministratorsAuthorizationHandler` Verifica che l'utente che agisce sulla risorsa sia un amministratore.</span><span class="sxs-lookup"><span data-stu-id="1417b-208">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="1417b-209">L'amministratore può eseguire tutte le operazioni.</span><span class="sxs-lookup"><span data-stu-id="1417b-209">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="1417b-210">Registrare i gestori di autorizzazioni</span><span class="sxs-lookup"><span data-stu-id="1417b-210">Register the authorization handlers</span></span>

<span data-ttu-id="1417b-211">I servizi che usano Entity Framework Core devono essere registrati per l' [inserimento di dipendenze](xref:fundamentals/dependency-injection) usando [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="1417b-211">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="1417b-212">`ContactIsOwnerAuthorizationHandler`Usa ASP.NET Core [Identity](xref:security/authentication/identity) , che è basato su Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="1417b-212">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="1417b-213">Registrare i gestori con la raccolta di servizi in modo che siano disponibili per `ContactsController` tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1417b-213">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1417b-214">Aggiungere il codice seguente alla fine di `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1417b-214">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="1417b-215">`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` vengono aggiunti come singleton.</span><span class="sxs-lookup"><span data-stu-id="1417b-215">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="1417b-216">Si tratta di singleton perché non usano EF e tutte le informazioni necessarie si trovano nel `Context` parametro del `HandleRequirementAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="1417b-216">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="1417b-217">Autorizzazione supporto</span><span class="sxs-lookup"><span data-stu-id="1417b-217">Support authorization</span></span>

<span data-ttu-id="1417b-218">In questa sezione si aggiornano le Razor pagine e si aggiunge una classe dei requisiti delle operazioni.</span><span class="sxs-lookup"><span data-stu-id="1417b-218">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="1417b-219">Esaminare la classe dei requisiti delle operazioni di contatto</span><span class="sxs-lookup"><span data-stu-id="1417b-219">Review the contact operations requirements class</span></span>

<span data-ttu-id="1417b-220">Esaminare la `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="1417b-220">Review the `ContactOperations` class.</span></span> <span data-ttu-id="1417b-221">Questa classe contiene i requisiti supportati dall'app:</span><span class="sxs-lookup"><span data-stu-id="1417b-221">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="1417b-222">Creare una classe di base per le Razor pagine contatti</span><span class="sxs-lookup"><span data-stu-id="1417b-222">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="1417b-223">Creare una classe di base che contiene i servizi utilizzati nelle Razor pagine contatti.</span><span class="sxs-lookup"><span data-stu-id="1417b-223">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="1417b-224">La classe base inserisce il codice di inizializzazione in un'unica posizione:</span><span class="sxs-lookup"><span data-stu-id="1417b-224">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="1417b-225">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="1417b-225">The preceding code:</span></span>

* <span data-ttu-id="1417b-226">Aggiunge il `IAuthorizationService` servizio per accedere ai gestori di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="1417b-226">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="1417b-227">Aggiunge il Identity `UserManager` servizio.</span><span class="sxs-lookup"><span data-stu-id="1417b-227">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="1417b-228">Aggiungere l'oggetto `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="1417b-228">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="1417b-229">Aggiornare CreateModel</span><span class="sxs-lookup"><span data-stu-id="1417b-229">Update the CreateModel</span></span>

<span data-ttu-id="1417b-230">Aggiornare il costruttore del modello di pagina create per usare la `DI_BasePageModel` classe di base:</span><span class="sxs-lookup"><span data-stu-id="1417b-230">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="1417b-231">Aggiornare il `CreateModel.OnPostAsync` metodo a:</span><span class="sxs-lookup"><span data-stu-id="1417b-231">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="1417b-232">Aggiungere l'ID utente al `Contact` modello.</span><span class="sxs-lookup"><span data-stu-id="1417b-232">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="1417b-233">Chiamare il gestore autorizzazioni per verificare che l'utente disponga dell'autorizzazione per la creazione di contatti.</span><span class="sxs-lookup"><span data-stu-id="1417b-233">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="1417b-234">Aggiornare IndexModel</span><span class="sxs-lookup"><span data-stu-id="1417b-234">Update the IndexModel</span></span>

<span data-ttu-id="1417b-235">Aggiornare il `OnGetAsync` metodo in modo da visualizzare solo i contatti approvati per gli utenti generali:</span><span class="sxs-lookup"><span data-stu-id="1417b-235">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="1417b-236">Aggiornare EditModel</span><span class="sxs-lookup"><span data-stu-id="1417b-236">Update the EditModel</span></span>

<span data-ttu-id="1417b-237">Aggiungere un gestore autorizzazioni per verificare che l'utente sia proprietario del contatto.</span><span class="sxs-lookup"><span data-stu-id="1417b-237">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="1417b-238">Poiché l'autorizzazione della risorsa viene convalidata, l' `[Authorize]` attributo non è sufficiente.</span><span class="sxs-lookup"><span data-stu-id="1417b-238">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="1417b-239">L'app non ha accesso alla risorsa quando vengono valutati gli attributi.</span><span class="sxs-lookup"><span data-stu-id="1417b-239">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="1417b-240">L'autorizzazione basata sulle risorse deve essere imperativa.</span><span class="sxs-lookup"><span data-stu-id="1417b-240">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="1417b-241">I controlli devono essere eseguiti una volta che l'app può accedere alla risorsa, eseguendo il caricamento nel modello di pagina o caricando l'app all'interno del gestore stesso.</span><span class="sxs-lookup"><span data-stu-id="1417b-241">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="1417b-242">Si accede spesso alla risorsa passando la chiave della risorsa.</span><span class="sxs-lookup"><span data-stu-id="1417b-242">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="1417b-243">Aggiornare DeleteModel</span><span class="sxs-lookup"><span data-stu-id="1417b-243">Update the DeleteModel</span></span>

<span data-ttu-id="1417b-244">Aggiornare il modello di pagina Elimina per utilizzare il gestore autorizzazione per verificare che l'utente disponga dell'autorizzazione DELETE per il contatto.</span><span class="sxs-lookup"><span data-stu-id="1417b-244">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="1417b-245">Inserire il servizio di autorizzazione nelle viste</span><span class="sxs-lookup"><span data-stu-id="1417b-245">Inject the authorization service into the views</span></span>

<span data-ttu-id="1417b-246">Attualmente, l'interfaccia utente Mostra i collegamenti modifica ed Elimina per i contatti che non possono essere modificati dall'utente.</span><span class="sxs-lookup"><span data-stu-id="1417b-246">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="1417b-247">Inserire il servizio di autorizzazione nel file *pages/_ViewImports. cshtml* in modo che sia disponibile per tutte le visualizzazioni:</span><span class="sxs-lookup"><span data-stu-id="1417b-247">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="1417b-248">Il markup precedente aggiunge diverse `using` istruzioni.</span><span class="sxs-lookup"><span data-stu-id="1417b-248">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="1417b-249">Aggiornare i collegamenti **Edit** ed **Delete** in *pages/Contacts/index. cshtml* in modo che vengano sottoposti a rendering solo per gli utenti con le autorizzazioni appropriate:</span><span class="sxs-lookup"><span data-stu-id="1417b-249">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="1417b-250">Nascondere i collegamenti da utenti che non dispongono dell'autorizzazione per modificare i dati non protegge l'app.</span><span class="sxs-lookup"><span data-stu-id="1417b-250">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="1417b-251">Nascondere i collegamenti rende l'app più intuitiva visualizzando solo i collegamenti validi.</span><span class="sxs-lookup"><span data-stu-id="1417b-251">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="1417b-252">Gli utenti possono hackerare gli URL generati per richiamare le operazioni di modifica ed eliminazione sui dati di cui non sono proprietari.</span><span class="sxs-lookup"><span data-stu-id="1417b-252">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="1417b-253">La Razor pagina o il controller deve applicare i controlli di accesso per proteggere i dati.</span><span class="sxs-lookup"><span data-stu-id="1417b-253">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="1417b-254">Dettagli aggiornamento</span><span class="sxs-lookup"><span data-stu-id="1417b-254">Update Details</span></span>

<span data-ttu-id="1417b-255">Aggiornare la visualizzazione dettagli in modo che i responsabili possano approvare o rifiutare i contatti:</span><span class="sxs-lookup"><span data-stu-id="1417b-255">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="1417b-256">Aggiornare il modello della pagina dei dettagli:</span><span class="sxs-lookup"><span data-stu-id="1417b-256">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="1417b-257">Aggiungere o rimuovere un utente in un ruolo</span><span class="sxs-lookup"><span data-stu-id="1417b-257">Add or remove a user to a role</span></span>

<span data-ttu-id="1417b-258">Per informazioni su, vedere [questo problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="1417b-258">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="1417b-259">Rimozione dei privilegi da un utente.</span><span class="sxs-lookup"><span data-stu-id="1417b-259">Removing privileges from a user.</span></span> <span data-ttu-id="1417b-260">Ad esempio, disattivare un utente in un'app di chat.</span><span class="sxs-lookup"><span data-stu-id="1417b-260">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="1417b-261">Aggiunta di privilegi a un utente.</span><span class="sxs-lookup"><span data-stu-id="1417b-261">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="1417b-262">Differenze tra la sfida e la proibizione</span><span class="sxs-lookup"><span data-stu-id="1417b-262">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="1417b-263">Questa app imposta i criteri predefiniti per [richiedere l'autenticazione degli utenti](#rau).</span><span class="sxs-lookup"><span data-stu-id="1417b-263">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="1417b-264">Il codice seguente consente agli utenti anonimi.</span><span class="sxs-lookup"><span data-stu-id="1417b-264">The following code allows anonymous users.</span></span> <span data-ttu-id="1417b-265">Gli utenti anonimi possono mostrare le differenze tra la richiesta di confronto e la proibizione.</span><span class="sxs-lookup"><span data-stu-id="1417b-265">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="1417b-266">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="1417b-266">In the preceding code:</span></span>

* <span data-ttu-id="1417b-267">Quando l'utente **non** è autenticato, `ChallengeResult` viene restituito un oggetto.</span><span class="sxs-lookup"><span data-stu-id="1417b-267">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="1417b-268">Quando `ChallengeResult` viene restituito un oggetto, l'utente viene reindirizzato alla pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="1417b-268">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="1417b-269">Quando l'utente viene autenticato, ma non autorizzato, `ForbidResult` viene restituito un oggetto.</span><span class="sxs-lookup"><span data-stu-id="1417b-269">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="1417b-270">Quando `ForbidResult` viene restituito un oggetto, l'utente viene reindirizzato alla pagina di accesso negato.</span><span class="sxs-lookup"><span data-stu-id="1417b-270">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="1417b-271">Testare l'app completata</span><span class="sxs-lookup"><span data-stu-id="1417b-271">Test the completed app</span></span>

<span data-ttu-id="1417b-272">Se non è già stata impostata una password per gli account utente di cui è stato eseguito il seeding, usare lo [strumento Gestione segreta](xref:security/app-secrets#secret-manager) per impostare una password:</span><span class="sxs-lookup"><span data-stu-id="1417b-272">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="1417b-273">Scegliere una password complessa: usare otto o più caratteri e almeno un carattere maiuscolo, un numero e un simbolo.</span><span class="sxs-lookup"><span data-stu-id="1417b-273">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="1417b-274">Ad esempio, `Passw0rd!` soddisfa i requisiti per le password complesse.</span><span class="sxs-lookup"><span data-stu-id="1417b-274">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="1417b-275">Eseguire il comando seguente dalla cartella del progetto, dove `<PW>` è la password:</span><span class="sxs-lookup"><span data-stu-id="1417b-275">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="1417b-276">Se l'app dispone di contatti:</span><span class="sxs-lookup"><span data-stu-id="1417b-276">If the app has contacts:</span></span>

* <span data-ttu-id="1417b-277">Eliminare tutti i record nella `Contact` tabella.</span><span class="sxs-lookup"><span data-stu-id="1417b-277">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="1417b-278">Riavviare l'app per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="1417b-278">Restart the app to seed the database.</span></span>

<span data-ttu-id="1417b-279">Un modo semplice per testare l'app completata consiste nell'avviare tre diversi browser (o sessioni in incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="1417b-279">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="1417b-280">In un browser registrare un nuovo utente (ad esempio, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="1417b-280">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="1417b-281">Accedere a ogni browser con un altro utente.</span><span class="sxs-lookup"><span data-stu-id="1417b-281">Sign in to each browser with a different user.</span></span> <span data-ttu-id="1417b-282">Verificare le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="1417b-282">Verify the following operations:</span></span>

* <span data-ttu-id="1417b-283">Gli utenti registrati possono visualizzare tutti i dati di contatto approvati.</span><span class="sxs-lookup"><span data-stu-id="1417b-283">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="1417b-284">Gli utenti registrati possono modificare/eliminare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="1417b-284">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="1417b-285">I responsabili possono approvare/rifiutare i dati di contatto.</span><span class="sxs-lookup"><span data-stu-id="1417b-285">Managers can approve/reject contact data.</span></span> <span data-ttu-id="1417b-286">La `Details` visualizzazione Mostra i pulsanti **approva** e **rifiuta** .</span><span class="sxs-lookup"><span data-stu-id="1417b-286">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="1417b-287">Gli amministratori possono approvare/rifiutare e modificare/eliminare tutti i dati.</span><span class="sxs-lookup"><span data-stu-id="1417b-287">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="1417b-288">Utente</span><span class="sxs-lookup"><span data-stu-id="1417b-288">User</span></span>                | <span data-ttu-id="1417b-289">Seeding dall'app</span><span class="sxs-lookup"><span data-stu-id="1417b-289">Seeded by the app</span></span> | <span data-ttu-id="1417b-290">Opzioni</span><span class="sxs-lookup"><span data-stu-id="1417b-290">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="1417b-291">No</span><span class="sxs-lookup"><span data-stu-id="1417b-291">No</span></span>                | <span data-ttu-id="1417b-292">Modificare/eliminare i dati personali.</span><span class="sxs-lookup"><span data-stu-id="1417b-292">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="1417b-293">Sì</span><span class="sxs-lookup"><span data-stu-id="1417b-293">Yes</span></span>               | <span data-ttu-id="1417b-294">Approva/rifiuta e modifica/elimina i dati personali.</span><span class="sxs-lookup"><span data-stu-id="1417b-294">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="1417b-295">Sì</span><span class="sxs-lookup"><span data-stu-id="1417b-295">Yes</span></span>               | <span data-ttu-id="1417b-296">Approva/rifiuta e modifica/elimina tutti i dati.</span><span class="sxs-lookup"><span data-stu-id="1417b-296">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="1417b-297">Creare un contatto nel browser dell'amministratore.</span><span class="sxs-lookup"><span data-stu-id="1417b-297">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="1417b-298">Copiare l'URL da eliminare e modificare dal contatto amministratore.</span><span class="sxs-lookup"><span data-stu-id="1417b-298">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="1417b-299">Incollare questi collegamenti nel browser dell'utente test per verificare che l'utente test non possa eseguire queste operazioni.</span><span class="sxs-lookup"><span data-stu-id="1417b-299">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="1417b-300">Creare l'app Starter</span><span class="sxs-lookup"><span data-stu-id="1417b-300">Create the starter app</span></span>

* <span data-ttu-id="1417b-301">Creare un' Razor app per le pagine denominata "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="1417b-301">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="1417b-302">Creare l'app con **singoli account utente**.</span><span class="sxs-lookup"><span data-stu-id="1417b-302">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="1417b-303">Denominarlo "ContactManager" in modo che lo spazio dei nomi corrisponda allo spazio dei nomi usato nell'esempio.</span><span class="sxs-lookup"><span data-stu-id="1417b-303">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="1417b-304">`-uld` Specifica il database locale anziché SQLite</span><span class="sxs-lookup"><span data-stu-id="1417b-304">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="1417b-305">Aggiungi *modelli/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="1417b-305">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="1417b-306">Impalcatura del `Contact` modello.</span><span class="sxs-lookup"><span data-stu-id="1417b-306">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="1417b-307">Creare la migrazione iniziale e aggiornare il database:</span><span class="sxs-lookup"><span data-stu-id="1417b-307">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="1417b-308">Se si verifica un bug con il `dotnet aspnet-codegenerator razorpage` comando, vedere [questo problema di GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="1417b-308">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="1417b-309">Aggiornare l'ancoraggio **ContactManager** nel file *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="1417b-309">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="1417b-310">Testare l'app creando, modificando ed eliminando un contatto</span><span class="sxs-lookup"><span data-stu-id="1417b-310">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="1417b-311">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="1417b-311">Seed the database</span></span>

<span data-ttu-id="1417b-312">Aggiungere la classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) alla cartella *Data* :</span><span class="sxs-lookup"><span data-stu-id="1417b-312">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="1417b-313">Chiama `SeedData.Initialize` da `Main` :</span><span class="sxs-lookup"><span data-stu-id="1417b-313">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="1417b-314">Verificare che l'app abbia sottoposta a seeding il database.</span><span class="sxs-lookup"><span data-stu-id="1417b-314">Test that the app seeded the database.</span></span> <span data-ttu-id="1417b-315">Se sono presenti righe nel database Contact, il metodo seed non viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="1417b-315">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="1417b-316">Questa esercitazione illustra come creare un'app Web di ASP.NET Core con i dati utente protetti dall'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="1417b-316">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="1417b-317">Viene visualizzato un elenco di contatti creati dagli utenti autenticati (registrati).</span><span class="sxs-lookup"><span data-stu-id="1417b-317">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="1417b-318">Sono disponibili tre gruppi di sicurezza:</span><span class="sxs-lookup"><span data-stu-id="1417b-318">There are three security groups:</span></span>

* <span data-ttu-id="1417b-319">**Gli utenti registrati** possono visualizzare tutti i dati approvati e possono modificare/eliminare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="1417b-319">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="1417b-320">I **responsabili** possono approvare o rifiutare i dati di contatto.</span><span class="sxs-lookup"><span data-stu-id="1417b-320">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="1417b-321">Solo i contatti approvati sono visibili agli utenti.</span><span class="sxs-lookup"><span data-stu-id="1417b-321">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="1417b-322">Gli **amministratori** possono approvare/rifiutare e modificare/eliminare i dati.</span><span class="sxs-lookup"><span data-stu-id="1417b-322">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="1417b-323">Nell'immagine seguente, l'utente Rick ( `rick@example.com` ) ha eseguito l'accesso.</span><span class="sxs-lookup"><span data-stu-id="1417b-323">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="1417b-324">Rick può visualizzare solo i contatti approvati e **modificare** / **Delete** / **Crea nuovi** collegamenti per i contatti.</span><span class="sxs-lookup"><span data-stu-id="1417b-324">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="1417b-325">Solo l'ultimo record, creato da Rick, Visualizza i collegamenti **modifica** ed **Elimina** .</span><span class="sxs-lookup"><span data-stu-id="1417b-325">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="1417b-326">Gli altri utenti non vedranno l'ultimo record fino a quando un responsabile o un amministratore non modifica lo stato in "approvato".</span><span class="sxs-lookup"><span data-stu-id="1417b-326">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot che illustra l'accesso a Rick](secure-data/_static/rick.png)

<span data-ttu-id="1417b-328">Nell'immagine seguente, `manager@contoso.com` è stato eseguito l'accesso e nel ruolo del Manager:</span><span class="sxs-lookup"><span data-stu-id="1417b-328">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot che mostra l' manager@contoso.com accesso](secure-data/_static/manager1.png)

<span data-ttu-id="1417b-330">Nella figura seguente viene illustrata la visualizzazione dettagli Manager di un contatto:</span><span class="sxs-lookup"><span data-stu-id="1417b-330">The following image shows the managers details view of a contact:</span></span>

![Visualizzazione del contatto da un responsabile](secure-data/_static/manager.png)

<span data-ttu-id="1417b-332">I pulsanti **approva** e **rifiuta** vengono visualizzati solo per Manager e amministratori.</span><span class="sxs-lookup"><span data-stu-id="1417b-332">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="1417b-333">Nell'immagine seguente, `admin@contoso.com` è stato eseguito l'accesso e il ruolo dell'amministratore:</span><span class="sxs-lookup"><span data-stu-id="1417b-333">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot che mostra l' admin@contoso.com accesso](secure-data/_static/admin.png)

<span data-ttu-id="1417b-335">L'amministratore dispone di tutti i privilegi.</span><span class="sxs-lookup"><span data-stu-id="1417b-335">The administrator has all privileges.</span></span> <span data-ttu-id="1417b-336">Può leggere, modificare ed eliminare qualsiasi contatto e modificare lo stato dei contatti.</span><span class="sxs-lookup"><span data-stu-id="1417b-336">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="1417b-337">L'app è stata creata mediante l' [impalcatura](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) del `Contact` modello seguente:</span><span class="sxs-lookup"><span data-stu-id="1417b-337">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="1417b-338">L'esempio contiene i gestori di autorizzazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="1417b-338">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="1417b-339">`ContactIsOwnerAuthorizationHandler`: Assicura che un utente possa modificare solo i propri dati.</span><span class="sxs-lookup"><span data-stu-id="1417b-339">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="1417b-340">`ContactManagerAuthorizationHandler`: Consente ai responsabili di approvare o rifiutare i contatti.</span><span class="sxs-lookup"><span data-stu-id="1417b-340">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="1417b-341">`ContactAdministratorsAuthorizationHandler`: Consente agli amministratori di approvare o rifiutare contatti e di modificare/eliminare contatti.</span><span class="sxs-lookup"><span data-stu-id="1417b-341">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1417b-342">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="1417b-342">Prerequisites</span></span>

<span data-ttu-id="1417b-343">Questa esercitazione è avanzata.</span><span class="sxs-lookup"><span data-stu-id="1417b-343">This tutorial is advanced.</span></span> <span data-ttu-id="1417b-344">È necessario avere familiarità con:</span><span class="sxs-lookup"><span data-stu-id="1417b-344">You should be familiar with:</span></span>

* [<span data-ttu-id="1417b-345">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1417b-345">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="1417b-346">autenticazione</span><span class="sxs-lookup"><span data-stu-id="1417b-346">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="1417b-347">Conferma account e recupero password</span><span class="sxs-lookup"><span data-stu-id="1417b-347">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="1417b-348">Autorizzazione</span><span class="sxs-lookup"><span data-stu-id="1417b-348">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="1417b-349">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="1417b-349">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="1417b-350">App iniziale e completata</span><span class="sxs-lookup"><span data-stu-id="1417b-350">The starter and completed app</span></span>

<span data-ttu-id="1417b-351">[Scaricare](xref:index#how-to-download-a-sample) l'app [completata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="1417b-351">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="1417b-352">[Testare](#test-the-completed-app) l'app completata in modo da acquisire familiarità con le funzionalità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="1417b-352">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="1417b-353">App iniziale</span><span class="sxs-lookup"><span data-stu-id="1417b-353">The starter app</span></span>

<span data-ttu-id="1417b-354">[Scaricare](xref:index#how-to-download-a-sample) l'app [Starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="1417b-354">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="1417b-355">Eseguire l'app, toccare il collegamento **ContactManager** e verificare che sia possibile creare, modificare ed eliminare un contatto.</span><span class="sxs-lookup"><span data-stu-id="1417b-355">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="1417b-356">Proteggere i dati utente</span><span class="sxs-lookup"><span data-stu-id="1417b-356">Secure user data</span></span>

<span data-ttu-id="1417b-357">Le sezioni seguenti includono tutti i passaggi principali per creare l'app Secure User Data.</span><span class="sxs-lookup"><span data-stu-id="1417b-357">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="1417b-358">Può risultare utile fare riferimento al progetto completato.</span><span class="sxs-lookup"><span data-stu-id="1417b-358">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="1417b-359">Associare i dati di contatto all'utente</span><span class="sxs-lookup"><span data-stu-id="1417b-359">Tie the contact data to the user</span></span>

<span data-ttu-id="1417b-360">Usare l' [Identity](xref:security/authentication/identity) ID utente ASP.NET per assicurarsi che gli utenti possano modificare i dati, ma non i dati di altri utenti.</span><span class="sxs-lookup"><span data-stu-id="1417b-360">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="1417b-361">Aggiungere `OwnerID` e `ContactStatus` al `Contact` modello:</span><span class="sxs-lookup"><span data-stu-id="1417b-361">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="1417b-362">`OwnerID` ID dell'utente della `AspNetUser` tabella nel [Identity](xref:security/authentication/identity) database.</span><span class="sxs-lookup"><span data-stu-id="1417b-362">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="1417b-363">Il `Status` campo determina se un contatto è visualizzabile dagli utenti generali.</span><span class="sxs-lookup"><span data-stu-id="1417b-363">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="1417b-364">Creare una nuova migrazione e aggiornare il database:</span><span class="sxs-lookup"><span data-stu-id="1417b-364">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="1417b-365">Aggiungere servizi ruolo a Identity</span><span class="sxs-lookup"><span data-stu-id="1417b-365">Add Role services to Identity</span></span>

<span data-ttu-id="1417b-366">Aggiungere [Aggiungi ruoli](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) per aggiungere servizi ruolo:</span><span class="sxs-lookup"><span data-stu-id="1417b-366">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="1417b-367">Richiedi utenti autenticati</span><span class="sxs-lookup"><span data-stu-id="1417b-367">Require authenticated users</span></span>

<span data-ttu-id="1417b-368">Impostare i criteri di autenticazione predefiniti per richiedere l'autenticazione degli utenti:</span><span class="sxs-lookup"><span data-stu-id="1417b-368">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="1417b-369">È possibile rifiutare esplicitamente l'autenticazione a Razor livello di pagina, controller o metodo di azione con l' `[AllowAnonymous]` attributo.</span><span class="sxs-lookup"><span data-stu-id="1417b-369">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="1417b-370">L'impostazione dei criteri di autenticazione predefiniti per richiedere l'autenticazione degli utenti consente di proteggere le Razor pagine e i controller appena aggiunti.</span><span class="sxs-lookup"><span data-stu-id="1417b-370">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="1417b-371">La necessità di eseguire l'autenticazione per impostazione predefinita è più sicura rispetto a quella di fare affidamento su nuovi controller e Razor pagine per includere l' `[Authorize]` attributo.</span><span class="sxs-lookup"><span data-stu-id="1417b-371">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="1417b-372">Aggiungere [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) alle pagine di indice, informazioni e contatti in modo che gli utenti anonimi possano ottenere informazioni sul sito prima della registrazione.</span><span class="sxs-lookup"><span data-stu-id="1417b-372">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="1417b-373">Configurare l'account di test</span><span class="sxs-lookup"><span data-stu-id="1417b-373">Configure the test account</span></span>

<span data-ttu-id="1417b-374">La `SeedData` classe crea due account: Administrator e Manager.</span><span class="sxs-lookup"><span data-stu-id="1417b-374">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="1417b-375">Utilizzare lo [strumento Gestione segreta](xref:security/app-secrets) per impostare una password per questi account.</span><span class="sxs-lookup"><span data-stu-id="1417b-375">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="1417b-376">Impostare la password dalla directory del progetto (la directory contenente *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="1417b-376">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="1417b-377">Se non si specifica una password complessa, viene generata un'eccezione quando `SeedData.Initialize` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="1417b-377">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="1417b-378">Aggiornare `Main` per usare la password di test:</span><span class="sxs-lookup"><span data-stu-id="1417b-378">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="1417b-379">Creare gli account di test e aggiornare i contatti</span><span class="sxs-lookup"><span data-stu-id="1417b-379">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="1417b-380">Aggiornare il `Initialize` metodo nella `SeedData` classe per creare gli account di test:</span><span class="sxs-lookup"><span data-stu-id="1417b-380">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="1417b-381">Aggiungere l'ID utente amministratore e `ContactStatus` ai contatti.</span><span class="sxs-lookup"><span data-stu-id="1417b-381">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="1417b-382">Creare uno dei contatti "inviato" e uno "rifiutato".</span><span class="sxs-lookup"><span data-stu-id="1417b-382">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="1417b-383">Aggiungere l'ID utente e lo stato a tutti i contatti.</span><span class="sxs-lookup"><span data-stu-id="1417b-383">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="1417b-384">Viene visualizzato un solo contatto:</span><span class="sxs-lookup"><span data-stu-id="1417b-384">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="1417b-385">Creazione di gestori di autorizzazioni proprietario, Manager e amministratore</span><span class="sxs-lookup"><span data-stu-id="1417b-385">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="1417b-386">Creare una cartella di *autorizzazione* e crearvi una `ContactIsOwnerAuthorizationHandler` classe.</span><span class="sxs-lookup"><span data-stu-id="1417b-386">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="1417b-387">`ContactIsOwnerAuthorizationHandler`Verifica che l'utente che agisce su una risorsa appartenga alla risorsa.</span><span class="sxs-lookup"><span data-stu-id="1417b-387">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="1417b-388">`ContactIsOwnerAuthorizationHandler`Contesto delle chiamate [. Ha esito positivo](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se l'utente autenticato corrente è il proprietario del contatto.</span><span class="sxs-lookup"><span data-stu-id="1417b-388">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="1417b-389">Gestori autorizzazioni in genere:</span><span class="sxs-lookup"><span data-stu-id="1417b-389">Authorization handlers generally:</span></span>

* <span data-ttu-id="1417b-390">Restituisce `context.Succeed` quando vengono soddisfatti i requisiti.</span><span class="sxs-lookup"><span data-stu-id="1417b-390">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="1417b-391">Restituisce `Task.CompletedTask` quando i requisiti non vengono soddisfatti.</span><span class="sxs-lookup"><span data-stu-id="1417b-391">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="1417b-392">`Task.CompletedTask` non ha esito positivo o negativo &mdash; consente l'esecuzione di altri gestori di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="1417b-392">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="1417b-393">Se è necessario eseguire in modo esplicito l'errore, restituire [context. Esito negativo](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="1417b-393">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="1417b-394">L'app consente ai proprietari dei contatti di modificare/eliminare/creare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="1417b-394">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="1417b-395">`ContactIsOwnerAuthorizationHandler` non è necessario controllare l'operazione passata nel parametro requirement.</span><span class="sxs-lookup"><span data-stu-id="1417b-395">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="1417b-396">Creazione di un gestore autorizzazioni di gestione</span><span class="sxs-lookup"><span data-stu-id="1417b-396">Create a manager authorization handler</span></span>

<span data-ttu-id="1417b-397">Creare una `ContactManagerAuthorizationHandler` classe nella cartella *authorization* .</span><span class="sxs-lookup"><span data-stu-id="1417b-397">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="1417b-398">`ContactManagerAuthorizationHandler`Verifica che l'utente che agisce sulla risorsa sia un responsabile.</span><span class="sxs-lookup"><span data-stu-id="1417b-398">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="1417b-399">Solo i responsabili possono approvare o rifiutare le modifiche al contenuto (nuove o modificate).</span><span class="sxs-lookup"><span data-stu-id="1417b-399">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="1417b-400">Creazione di un gestore autorizzazioni Amministratore</span><span class="sxs-lookup"><span data-stu-id="1417b-400">Create an administrator authorization handler</span></span>

<span data-ttu-id="1417b-401">Creare una `ContactAdministratorsAuthorizationHandler` classe nella cartella *authorization* .</span><span class="sxs-lookup"><span data-stu-id="1417b-401">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="1417b-402">Il `ContactAdministratorsAuthorizationHandler` Verifica che l'utente che agisce sulla risorsa sia un amministratore.</span><span class="sxs-lookup"><span data-stu-id="1417b-402">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="1417b-403">L'amministratore può eseguire tutte le operazioni.</span><span class="sxs-lookup"><span data-stu-id="1417b-403">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="1417b-404">Registrare i gestori di autorizzazioni</span><span class="sxs-lookup"><span data-stu-id="1417b-404">Register the authorization handlers</span></span>

<span data-ttu-id="1417b-405">I servizi che usano Entity Framework Core devono essere registrati per l' [inserimento di dipendenze](xref:fundamentals/dependency-injection) usando [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="1417b-405">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="1417b-406">`ContactIsOwnerAuthorizationHandler`Usa ASP.NET Core [Identity](xref:security/authentication/identity) , che è basato su Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="1417b-406">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="1417b-407">Registrare i gestori con la raccolta di servizi in modo che siano disponibili per `ContactsController` tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1417b-407">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1417b-408">Aggiungere il codice seguente alla fine di `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1417b-408">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="1417b-409">`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` vengono aggiunti come singleton.</span><span class="sxs-lookup"><span data-stu-id="1417b-409">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="1417b-410">Si tratta di singleton perché non usano EF e tutte le informazioni necessarie si trovano nel `Context` parametro del `HandleRequirementAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="1417b-410">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="1417b-411">Autorizzazione supporto</span><span class="sxs-lookup"><span data-stu-id="1417b-411">Support authorization</span></span>

<span data-ttu-id="1417b-412">In questa sezione si aggiornano le Razor pagine e si aggiunge una classe dei requisiti delle operazioni.</span><span class="sxs-lookup"><span data-stu-id="1417b-412">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="1417b-413">Esaminare la classe dei requisiti delle operazioni di contatto</span><span class="sxs-lookup"><span data-stu-id="1417b-413">Review the contact operations requirements class</span></span>

<span data-ttu-id="1417b-414">Esaminare la `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="1417b-414">Review the `ContactOperations` class.</span></span> <span data-ttu-id="1417b-415">Questa classe contiene i requisiti supportati dall'app:</span><span class="sxs-lookup"><span data-stu-id="1417b-415">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="1417b-416">Creare una classe di base per le Razor pagine contatti</span><span class="sxs-lookup"><span data-stu-id="1417b-416">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="1417b-417">Creare una classe di base che contiene i servizi utilizzati nelle Razor pagine contatti.</span><span class="sxs-lookup"><span data-stu-id="1417b-417">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="1417b-418">La classe base inserisce il codice di inizializzazione in un'unica posizione:</span><span class="sxs-lookup"><span data-stu-id="1417b-418">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="1417b-419">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="1417b-419">The preceding code:</span></span>

* <span data-ttu-id="1417b-420">Aggiunge il `IAuthorizationService` servizio per accedere ai gestori di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="1417b-420">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="1417b-421">Aggiunge il Identity `UserManager` servizio.</span><span class="sxs-lookup"><span data-stu-id="1417b-421">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="1417b-422">Aggiungere l'oggetto `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="1417b-422">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="1417b-423">Aggiornare CreateModel</span><span class="sxs-lookup"><span data-stu-id="1417b-423">Update the CreateModel</span></span>

<span data-ttu-id="1417b-424">Aggiornare il costruttore del modello di pagina create per usare la `DI_BasePageModel` classe di base:</span><span class="sxs-lookup"><span data-stu-id="1417b-424">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="1417b-425">Aggiornare il `CreateModel.OnPostAsync` metodo a:</span><span class="sxs-lookup"><span data-stu-id="1417b-425">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="1417b-426">Aggiungere l'ID utente al `Contact` modello.</span><span class="sxs-lookup"><span data-stu-id="1417b-426">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="1417b-427">Chiamare il gestore autorizzazioni per verificare che l'utente disponga dell'autorizzazione per la creazione di contatti.</span><span class="sxs-lookup"><span data-stu-id="1417b-427">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="1417b-428">Aggiornare IndexModel</span><span class="sxs-lookup"><span data-stu-id="1417b-428">Update the IndexModel</span></span>

<span data-ttu-id="1417b-429">Aggiornare il `OnGetAsync` metodo in modo da visualizzare solo i contatti approvati per gli utenti generali:</span><span class="sxs-lookup"><span data-stu-id="1417b-429">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="1417b-430">Aggiornare EditModel</span><span class="sxs-lookup"><span data-stu-id="1417b-430">Update the EditModel</span></span>

<span data-ttu-id="1417b-431">Aggiungere un gestore autorizzazioni per verificare che l'utente sia proprietario del contatto.</span><span class="sxs-lookup"><span data-stu-id="1417b-431">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="1417b-432">Poiché l'autorizzazione della risorsa viene convalidata, l' `[Authorize]` attributo non è sufficiente.</span><span class="sxs-lookup"><span data-stu-id="1417b-432">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="1417b-433">L'app non ha accesso alla risorsa quando vengono valutati gli attributi.</span><span class="sxs-lookup"><span data-stu-id="1417b-433">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="1417b-434">L'autorizzazione basata sulle risorse deve essere imperativa.</span><span class="sxs-lookup"><span data-stu-id="1417b-434">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="1417b-435">I controlli devono essere eseguiti una volta che l'app può accedere alla risorsa, eseguendo il caricamento nel modello di pagina o caricando l'app all'interno del gestore stesso.</span><span class="sxs-lookup"><span data-stu-id="1417b-435">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="1417b-436">Si accede spesso alla risorsa passando la chiave della risorsa.</span><span class="sxs-lookup"><span data-stu-id="1417b-436">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="1417b-437">Aggiornare DeleteModel</span><span class="sxs-lookup"><span data-stu-id="1417b-437">Update the DeleteModel</span></span>

<span data-ttu-id="1417b-438">Aggiornare il modello di pagina Elimina per utilizzare il gestore autorizzazione per verificare che l'utente disponga dell'autorizzazione DELETE per il contatto.</span><span class="sxs-lookup"><span data-stu-id="1417b-438">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="1417b-439">Inserire il servizio di autorizzazione nelle viste</span><span class="sxs-lookup"><span data-stu-id="1417b-439">Inject the authorization service into the views</span></span>

<span data-ttu-id="1417b-440">Attualmente, l'interfaccia utente Mostra i collegamenti modifica ed Elimina per i contatti che non possono essere modificati dall'utente.</span><span class="sxs-lookup"><span data-stu-id="1417b-440">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="1417b-441">Inserire il servizio di autorizzazione nel file *views/_ViewImports. cshtml* in modo che sia disponibile per tutte le visualizzazioni:</span><span class="sxs-lookup"><span data-stu-id="1417b-441">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="1417b-442">Il markup precedente aggiunge diverse `using` istruzioni.</span><span class="sxs-lookup"><span data-stu-id="1417b-442">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="1417b-443">Aggiornare i collegamenti **Edit** ed **Delete** in *pages/Contacts/index. cshtml* in modo che vengano sottoposti a rendering solo per gli utenti con le autorizzazioni appropriate:</span><span class="sxs-lookup"><span data-stu-id="1417b-443">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="1417b-444">Nascondere i collegamenti da utenti che non dispongono dell'autorizzazione per modificare i dati non protegge l'app.</span><span class="sxs-lookup"><span data-stu-id="1417b-444">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="1417b-445">Nascondere i collegamenti rende l'app più intuitiva visualizzando solo i collegamenti validi.</span><span class="sxs-lookup"><span data-stu-id="1417b-445">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="1417b-446">Gli utenti possono hackerare gli URL generati per richiamare le operazioni di modifica ed eliminazione sui dati di cui non sono proprietari.</span><span class="sxs-lookup"><span data-stu-id="1417b-446">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="1417b-447">La Razor pagina o il controller deve applicare i controlli di accesso per proteggere i dati.</span><span class="sxs-lookup"><span data-stu-id="1417b-447">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="1417b-448">Dettagli aggiornamento</span><span class="sxs-lookup"><span data-stu-id="1417b-448">Update Details</span></span>

<span data-ttu-id="1417b-449">Aggiornare la visualizzazione dettagli in modo che i responsabili possano approvare o rifiutare i contatti:</span><span class="sxs-lookup"><span data-stu-id="1417b-449">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="1417b-450">Aggiornare il modello della pagina dei dettagli:</span><span class="sxs-lookup"><span data-stu-id="1417b-450">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="1417b-451">Aggiungere o rimuovere un utente in un ruolo</span><span class="sxs-lookup"><span data-stu-id="1417b-451">Add or remove a user to a role</span></span>

<span data-ttu-id="1417b-452">Per informazioni su, vedere [questo problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="1417b-452">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="1417b-453">Rimozione dei privilegi da un utente.</span><span class="sxs-lookup"><span data-stu-id="1417b-453">Removing privileges from a user.</span></span> <span data-ttu-id="1417b-454">Ad esempio, disattivare un utente in un'app di chat.</span><span class="sxs-lookup"><span data-stu-id="1417b-454">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="1417b-455">Aggiunta di privilegi a un utente.</span><span class="sxs-lookup"><span data-stu-id="1417b-455">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="1417b-456">Testare l'app completata</span><span class="sxs-lookup"><span data-stu-id="1417b-456">Test the completed app</span></span>

<span data-ttu-id="1417b-457">Se non è già stata impostata una password per gli account utente di cui è stato eseguito il seeding, usare lo [strumento Gestione segreta](xref:security/app-secrets#secret-manager) per impostare una password:</span><span class="sxs-lookup"><span data-stu-id="1417b-457">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="1417b-458">Scegliere una password complessa: usare otto o più caratteri e almeno un carattere maiuscolo, un numero e un simbolo.</span><span class="sxs-lookup"><span data-stu-id="1417b-458">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="1417b-459">Ad esempio, `Passw0rd!` soddisfa i requisiti per le password complesse.</span><span class="sxs-lookup"><span data-stu-id="1417b-459">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="1417b-460">Eseguire il comando seguente dalla cartella del progetto, dove `<PW>` è la password:</span><span class="sxs-lookup"><span data-stu-id="1417b-460">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="1417b-461">Eliminare e aggiornare il database</span><span class="sxs-lookup"><span data-stu-id="1417b-461">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="1417b-462">Riavviare l'app per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="1417b-462">Restart the app to seed the database.</span></span>

<span data-ttu-id="1417b-463">Un modo semplice per testare l'app completata consiste nell'avviare tre diversi browser (o sessioni in incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="1417b-463">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="1417b-464">In un browser registrare un nuovo utente (ad esempio, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="1417b-464">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="1417b-465">Accedere a ogni browser con un altro utente.</span><span class="sxs-lookup"><span data-stu-id="1417b-465">Sign in to each browser with a different user.</span></span> <span data-ttu-id="1417b-466">Verificare le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="1417b-466">Verify the following operations:</span></span>

* <span data-ttu-id="1417b-467">Gli utenti registrati possono visualizzare tutti i dati di contatto approvati.</span><span class="sxs-lookup"><span data-stu-id="1417b-467">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="1417b-468">Gli utenti registrati possono modificare/eliminare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="1417b-468">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="1417b-469">I responsabili possono approvare/rifiutare i dati di contatto.</span><span class="sxs-lookup"><span data-stu-id="1417b-469">Managers can approve/reject contact data.</span></span> <span data-ttu-id="1417b-470">La `Details` visualizzazione Mostra i pulsanti **approva** e **rifiuta** .</span><span class="sxs-lookup"><span data-stu-id="1417b-470">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="1417b-471">Gli amministratori possono approvare/rifiutare e modificare/eliminare tutti i dati.</span><span class="sxs-lookup"><span data-stu-id="1417b-471">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="1417b-472">Utente</span><span class="sxs-lookup"><span data-stu-id="1417b-472">User</span></span>                | <span data-ttu-id="1417b-473">Seeding dall'app</span><span class="sxs-lookup"><span data-stu-id="1417b-473">Seeded by the app</span></span> | <span data-ttu-id="1417b-474">Opzioni</span><span class="sxs-lookup"><span data-stu-id="1417b-474">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="1417b-475">No</span><span class="sxs-lookup"><span data-stu-id="1417b-475">No</span></span>                | <span data-ttu-id="1417b-476">Modificare/eliminare i dati personali.</span><span class="sxs-lookup"><span data-stu-id="1417b-476">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="1417b-477">Sì</span><span class="sxs-lookup"><span data-stu-id="1417b-477">Yes</span></span>               | <span data-ttu-id="1417b-478">Approva/rifiuta e modifica/elimina i dati personali.</span><span class="sxs-lookup"><span data-stu-id="1417b-478">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="1417b-479">Sì</span><span class="sxs-lookup"><span data-stu-id="1417b-479">Yes</span></span>               | <span data-ttu-id="1417b-480">Approva/rifiuta e modifica/elimina tutti i dati.</span><span class="sxs-lookup"><span data-stu-id="1417b-480">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="1417b-481">Creare un contatto nel browser dell'amministratore.</span><span class="sxs-lookup"><span data-stu-id="1417b-481">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="1417b-482">Copiare l'URL da eliminare e modificare dal contatto amministratore.</span><span class="sxs-lookup"><span data-stu-id="1417b-482">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="1417b-483">Incollare questi collegamenti nel browser dell'utente test per verificare che l'utente test non possa eseguire queste operazioni.</span><span class="sxs-lookup"><span data-stu-id="1417b-483">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="1417b-484">Creare l'app Starter</span><span class="sxs-lookup"><span data-stu-id="1417b-484">Create the starter app</span></span>

* <span data-ttu-id="1417b-485">Creare un' Razor app per le pagine denominata "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="1417b-485">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="1417b-486">Creare l'app con **singoli account utente**.</span><span class="sxs-lookup"><span data-stu-id="1417b-486">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="1417b-487">Denominarlo "ContactManager" in modo che lo spazio dei nomi corrisponda allo spazio dei nomi usato nell'esempio.</span><span class="sxs-lookup"><span data-stu-id="1417b-487">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="1417b-488">`-uld` Specifica il database locale anziché SQLite</span><span class="sxs-lookup"><span data-stu-id="1417b-488">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="1417b-489">Aggiungi *modelli/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="1417b-489">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="1417b-490">Impalcatura del `Contact` modello.</span><span class="sxs-lookup"><span data-stu-id="1417b-490">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="1417b-491">Creare la migrazione iniziale e aggiornare il database:</span><span class="sxs-lookup"><span data-stu-id="1417b-491">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="1417b-492">Aggiornare l'ancoraggio **ContactManager** nel file *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="1417b-492">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="1417b-493">Testare l'app creando, modificando ed eliminando un contatto</span><span class="sxs-lookup"><span data-stu-id="1417b-493">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="1417b-494">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="1417b-494">Seed the database</span></span>

<span data-ttu-id="1417b-495">Aggiungere la classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) alla cartella *Data* .</span><span class="sxs-lookup"><span data-stu-id="1417b-495">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="1417b-496">Chiama `SeedData.Initialize` da `Main` :</span><span class="sxs-lookup"><span data-stu-id="1417b-496">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="1417b-497">Verificare che l'app abbia sottoposta a seeding il database.</span><span class="sxs-lookup"><span data-stu-id="1417b-497">Test that the app seeded the database.</span></span> <span data-ttu-id="1417b-498">Se sono presenti righe nel database Contact, il metodo seed non viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="1417b-498">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="1417b-499">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="1417b-499">Additional resources</span></span>

* [<span data-ttu-id="1417b-500">Creare un'app Web .NET Core e database SQL nel Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="1417b-500">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="1417b-501">[ASP.NET Core Lab di autorizzazione](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="1417b-501">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="1417b-502">Questo Lab illustra in modo più dettagliato le funzionalità di sicurezza introdotte in questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="1417b-502">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="1417b-503">Autorizzazione personalizzata basata su criteri</span><span class="sxs-lookup"><span data-stu-id="1417b-503">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
