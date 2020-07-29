---
title: Creare un'app ASP.NET Core con i dati utente protetti dall'autorizzazione
author: rick-anderson
description: Informazioni su come creare un'app Web di ASP.NET Core con i dati utente protetti dall'autorizzazione. Include HTTPS, autenticazione, sicurezza ASP.NET Core Identity .
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/secure-data
ms.openlocfilehash: 7d4c10fa0b1c569179fc3e0a518917ec0185c51f
ms.sourcegitcommit: 1b89fc58114a251926abadfd5c69c120f1ba12d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87160274"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="f4b4c-104">Creare un'app Web di ASP.NET Core con i dati utente protetti dall'autorizzazione</span><span class="sxs-lookup"><span data-stu-id="f4b4c-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="f4b4c-105">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="f4b4c-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="f4b4c-106">Vedi [questo PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="f4b4c-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f4b4c-107">Questa esercitazione illustra come creare un'app Web di ASP.NET Core con i dati utente protetti dall'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="f4b4c-108">Viene visualizzato un elenco di contatti creati dagli utenti autenticati (registrati).</span><span class="sxs-lookup"><span data-stu-id="f4b4c-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="f4b4c-109">Sono disponibili tre gruppi di sicurezza:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-109">There are three security groups:</span></span>

* <span data-ttu-id="f4b4c-110">**Gli utenti registrati** possono visualizzare tutti i dati approvati e possono modificare/eliminare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="f4b4c-111">I **responsabili** possono approvare o rifiutare i dati di contatto.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="f4b4c-112">Solo i contatti approvati sono visibili agli utenti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="f4b4c-113">Gli **amministratori** possono approvare/rifiutare e modificare/eliminare i dati.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="f4b4c-114">Le immagini in questo documento non corrispondono esattamente ai modelli più recenti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="f4b4c-115">Nell'immagine seguente, l'utente Rick ( `rick@example.com` ) ha eseguito l'accesso.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="f4b4c-116">Rick può visualizzare solo i contatti approvati e **modificare** / **Delete** / **Crea nuovi** collegamenti per i contatti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="f4b4c-117">Solo l'ultimo record, creato da Rick, Visualizza i collegamenti **modifica** ed **Elimina** .</span><span class="sxs-lookup"><span data-stu-id="f4b4c-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="f4b4c-118">Gli altri utenti non vedranno l'ultimo record fino a quando un responsabile o un amministratore non modifica lo stato in "approvato".</span><span class="sxs-lookup"><span data-stu-id="f4b4c-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot che illustra l'accesso a Rick](secure-data/_static/rick.png)

<span data-ttu-id="f4b4c-120">Nell'immagine seguente, `manager@contoso.com` è stato eseguito l'accesso e nel ruolo del Manager:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot che mostra l' manager@contoso.com accesso](secure-data/_static/manager1.png)

<span data-ttu-id="f4b4c-122">Nella figura seguente viene illustrata la visualizzazione dettagli Manager di un contatto:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-122">The following image shows the managers details view of a contact:</span></span>

![Visualizzazione del contatto da un responsabile](secure-data/_static/manager.png)

<span data-ttu-id="f4b4c-124">I pulsanti **approva** e **rifiuta** vengono visualizzati solo per Manager e amministratori.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="f4b4c-125">Nell'immagine seguente, `admin@contoso.com` è stato eseguito l'accesso e il ruolo dell'amministratore:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot che mostra l' admin@contoso.com accesso](secure-data/_static/admin.png)

<span data-ttu-id="f4b4c-127">L'amministratore dispone di tutti i privilegi.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-127">The administrator has all privileges.</span></span> <span data-ttu-id="f4b4c-128">Può leggere, modificare ed eliminare qualsiasi contatto e modificare lo stato dei contatti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="f4b4c-129">L'app è stata creata mediante l' [impalcatura](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) del `Contact` modello seguente:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="f4b4c-130">L'esempio contiene i gestori di autorizzazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="f4b4c-131">`ContactIsOwnerAuthorizationHandler`: Assicura che un utente possa modificare solo i propri dati.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="f4b4c-132">`ContactManagerAuthorizationHandler`: Consente ai responsabili di approvare o rifiutare i contatti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="f4b4c-133">`ContactAdministratorsAuthorizationHandler`: Consente agli amministratori di approvare o rifiutare contatti e di modificare/eliminare contatti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f4b4c-134">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="f4b4c-134">Prerequisites</span></span>

<span data-ttu-id="f4b4c-135">Questa esercitazione è avanzata.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-135">This tutorial is advanced.</span></span> <span data-ttu-id="f4b4c-136">È necessario avere familiarità con:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-136">You should be familiar with:</span></span>

* [<span data-ttu-id="f4b4c-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f4b4c-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="f4b4c-138">autenticazione</span><span class="sxs-lookup"><span data-stu-id="f4b4c-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="f4b4c-139">Conferma account e recupero password</span><span class="sxs-lookup"><span data-stu-id="f4b4c-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="f4b4c-140">Autorizzazione</span><span class="sxs-lookup"><span data-stu-id="f4b4c-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="f4b4c-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="f4b4c-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="f4b4c-142">App iniziale e completata</span><span class="sxs-lookup"><span data-stu-id="f4b4c-142">The starter and completed app</span></span>

<span data-ttu-id="f4b4c-143">[Scaricare](xref:index#how-to-download-a-sample) l'app [completata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="f4b4c-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="f4b4c-144">[Testare](#test-the-completed-app) l'app completata in modo da acquisire familiarità con le funzionalità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="f4b4c-145">App iniziale</span><span class="sxs-lookup"><span data-stu-id="f4b4c-145">The starter app</span></span>

<span data-ttu-id="f4b4c-146">[Scaricare](xref:index#how-to-download-a-sample) l'app [Starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="f4b4c-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="f4b4c-147">Eseguire l'app, toccare il collegamento **ContactManager** e verificare che sia possibile creare, modificare ed eliminare un contatto.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="f4b4c-148">Proteggere i dati utente</span><span class="sxs-lookup"><span data-stu-id="f4b4c-148">Secure user data</span></span>

<span data-ttu-id="f4b4c-149">Le sezioni seguenti includono tutti i passaggi principali per creare l'app Secure User Data.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-149">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="f4b4c-150">Può risultare utile fare riferimento al progetto completato.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-150">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="f4b4c-151">Associare i dati di contatto all'utente</span><span class="sxs-lookup"><span data-stu-id="f4b4c-151">Tie the contact data to the user</span></span>

<span data-ttu-id="f4b4c-152">Usare l' [Identity](xref:security/authentication/identity) ID utente ASP.NET per assicurarsi che gli utenti possano modificare i dati, ma non i dati di altri utenti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-152">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="f4b4c-153">Aggiungere `OwnerID` e `ContactStatus` al `Contact` modello:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-153">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="f4b4c-154">`OwnerID`ID dell'utente della `AspNetUser` tabella nel [Identity](xref:security/authentication/identity) database.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-154">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="f4b4c-155">Il `Status` campo determina se un contatto è visualizzabile dagli utenti generali.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-155">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="f4b4c-156">Creare una nuova migrazione e aggiornare il database:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-156">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="f4b4c-157">Aggiungere servizi ruolo aIdentity</span><span class="sxs-lookup"><span data-stu-id="f4b4c-157">Add Role services to Identity</span></span>

<span data-ttu-id="f4b4c-158">Aggiungere [Aggiungi ruoli](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) per aggiungere servizi ruolo:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-158">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="f4b4c-159">Richiedi utenti autenticati</span><span class="sxs-lookup"><span data-stu-id="f4b4c-159">Require authenticated users</span></span>

<span data-ttu-id="f4b4c-160">Impostare i criteri di autenticazione di fallback per richiedere l'autenticazione degli utenti:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-160">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="f4b4c-161">Il codice evidenziato precedente imposta i [criteri di autenticazione di fallback](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="f4b4c-161">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="f4b4c-162">Per i criteri di autenticazione di fallback è necessario autenticare ***tutti*** gli utenti, ad eccezione di Razor pagine, controller o metodi di azione con un attributo di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-162">The fallback authentication policy requires ***all*** users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="f4b4c-163">Ad esempio, Razor pagine, controller o metodi di azione con `[AllowAnonymous]` o `[Authorize(PolicyName="MyPolicy")]` utilizzano l'attributo di autenticazione applicato anziché i criteri di autenticazione di fallback.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-163">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="f4b4c-164">I criteri di autenticazione di fallback:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-164">The fallback authentication policy:</span></span>

* <span data-ttu-id="f4b4c-165">Viene applicato a tutte le richieste che non specificano in modo esplicito i criteri di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-165">Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="f4b4c-166">Per le richieste gestite dal routing degli endpoint, questo includerebbe qualsiasi endpoint che non specifichi un attributo di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-166">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="f4b4c-167">Per le richieste gestite da altri middleware dopo il middleware di autorizzazione, ad esempio [i file statici](xref:fundamentals/static-files), il criterio viene applicato a tutte le richieste.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-167">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="f4b4c-168">L'impostazione dei criteri di autenticazione di fallback per richiedere l'autenticazione degli utenti consente di proteggere le Razor pagine e i controller appena aggiunti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-168">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="f4b4c-169">La necessità di eseguire l'autenticazione per impostazione predefinita è più sicura rispetto a quella di fare affidamento su nuovi controller e Razor pagine per includere l' `[Authorize]` attributo.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-169">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="f4b4c-170">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> classe contiene anche <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="f4b4c-170">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="f4b4c-171">`DefaultPolicy`È il criterio utilizzato con l' `[Authorize]` attributo quando non è specificato alcun criterio.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-171">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="f4b4c-172">`[Authorize]`non contiene un criterio denominato, a differenza di `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="f4b4c-172">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="f4b4c-173">Per ulteriori informazioni sui criteri, vedere <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="f4b4c-173">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="f4b4c-174">Un metodo alternativo per i controller e Razor le pagine MVC per richiedere che tutti gli utenti siano autenticati è l'aggiunta di un filtro di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-174">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="f4b4c-175">Il codice precedente usa un filtro di autorizzazione, l'impostazione dei criteri di fallback usa il routing degli endpoint.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-175">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="f4b4c-176">L'impostazione dei criteri di fallback è il modo migliore per richiedere l'autenticazione di tutti gli utenti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-176">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="f4b4c-177">Aggiungere [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) alle `Index` pagine e in `Privacy` modo che gli utenti anonimi possano ottenere informazioni sul sito prima della registrazione:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-177">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="f4b4c-178">Configurare l'account di test</span><span class="sxs-lookup"><span data-stu-id="f4b4c-178">Configure the test account</span></span>

<span data-ttu-id="f4b4c-179">La `SeedData` classe crea due account: Administrator e Manager.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-179">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="f4b4c-180">Utilizzare lo [strumento Gestione segreta](xref:security/app-secrets) per impostare una password per questi account.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-180">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="f4b4c-181">Impostare la password dalla directory del progetto (la directory contenente *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="f4b4c-181">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="f4b4c-182">Se non si specifica una password complessa, viene generata un'eccezione quando `SeedData.Initialize` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-182">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="f4b4c-183">Aggiornare `Main` per usare la password di test:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-183">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="f4b4c-184">Creare gli account di test e aggiornare i contatti</span><span class="sxs-lookup"><span data-stu-id="f4b4c-184">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="f4b4c-185">Aggiornare il `Initialize` metodo nella `SeedData` classe per creare gli account di test:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-185">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="f4b4c-186">Aggiungere l'ID utente amministratore e `ContactStatus` ai contatti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-186">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="f4b4c-187">Creare uno dei contatti "inviato" e uno "rifiutato".</span><span class="sxs-lookup"><span data-stu-id="f4b4c-187">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="f4b4c-188">Aggiungere l'ID utente e lo stato a tutti i contatti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-188">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="f4b4c-189">Viene visualizzato un solo contatto:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-189">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="f4b4c-190">Creazione di gestori di autorizzazioni proprietario, Manager e amministratore</span><span class="sxs-lookup"><span data-stu-id="f4b4c-190">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="f4b4c-191">Creare una `ContactIsOwnerAuthorizationHandler` classe nella cartella *authorization* .</span><span class="sxs-lookup"><span data-stu-id="f4b4c-191">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="f4b4c-192">`ContactIsOwnerAuthorizationHandler`Verifica che l'utente che agisce su una risorsa appartenga alla risorsa.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-192">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="f4b4c-193">`ContactIsOwnerAuthorizationHandler`Contesto delle chiamate [. Ha esito positivo](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se l'utente autenticato corrente è il proprietario del contatto.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-193">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="f4b4c-194">Gestori autorizzazioni in genere:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-194">Authorization handlers generally:</span></span>

* <span data-ttu-id="f4b4c-195">Restituisce `context.Succeed` quando vengono soddisfatti i requisiti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-195">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="f4b4c-196">Restituisce `Task.CompletedTask` quando i requisiti non vengono soddisfatti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-196">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="f4b4c-197">`Task.CompletedTask`non ha esito positivo o negativo &mdash; consente l'esecuzione di altri gestori di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-197">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="f4b4c-198">Se è necessario eseguire in modo esplicito l'errore, restituire [context. Esito negativo](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="f4b4c-198">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="f4b4c-199">L'app consente ai proprietari dei contatti di modificare/eliminare/creare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-199">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="f4b4c-200">`ContactIsOwnerAuthorizationHandler`non è necessario controllare l'operazione passata nel parametro requirement.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-200">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="f4b4c-201">Creazione di un gestore autorizzazioni di gestione</span><span class="sxs-lookup"><span data-stu-id="f4b4c-201">Create a manager authorization handler</span></span>

<span data-ttu-id="f4b4c-202">Creare una `ContactManagerAuthorizationHandler` classe nella cartella *authorization* .</span><span class="sxs-lookup"><span data-stu-id="f4b4c-202">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="f4b4c-203">`ContactManagerAuthorizationHandler`Verifica che l'utente che agisce sulla risorsa sia un responsabile.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-203">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="f4b4c-204">Solo i responsabili possono approvare o rifiutare le modifiche al contenuto (nuove o modificate).</span><span class="sxs-lookup"><span data-stu-id="f4b4c-204">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="f4b4c-205">Creazione di un gestore autorizzazioni Amministratore</span><span class="sxs-lookup"><span data-stu-id="f4b4c-205">Create an administrator authorization handler</span></span>

<span data-ttu-id="f4b4c-206">Creare una `ContactAdministratorsAuthorizationHandler` classe nella cartella *authorization* .</span><span class="sxs-lookup"><span data-stu-id="f4b4c-206">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="f4b4c-207">Il `ContactAdministratorsAuthorizationHandler` Verifica che l'utente che agisce sulla risorsa sia un amministratore.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-207">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="f4b4c-208">L'amministratore può eseguire tutte le operazioni.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-208">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="f4b4c-209">Registrare i gestori di autorizzazioni</span><span class="sxs-lookup"><span data-stu-id="f4b4c-209">Register the authorization handlers</span></span>

<span data-ttu-id="f4b4c-210">I servizi che usano Entity Framework Core devono essere registrati per l' [inserimento di dipendenze](xref:fundamentals/dependency-injection) usando [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="f4b4c-210">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="f4b4c-211">`ContactIsOwnerAuthorizationHandler`Usa ASP.NET Core [Identity](xref:security/authentication/identity) , che è basato su Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-211">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="f4b4c-212">Registrare i gestori con la raccolta di servizi in modo che siano disponibili per `ContactsController` tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f4b4c-212">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f4b4c-213">Aggiungere il codice seguente alla fine di `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f4b4c-213">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="f4b4c-214">`ContactAdministratorsAuthorizationHandler`e `ContactManagerAuthorizationHandler` vengono aggiunti come singleton.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-214">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="f4b4c-215">Si tratta di singleton perché non usano EF e tutte le informazioni necessarie si trovano nel `Context` parametro del `HandleRequirementAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-215">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="f4b4c-216">Autorizzazione supporto</span><span class="sxs-lookup"><span data-stu-id="f4b4c-216">Support authorization</span></span>

<span data-ttu-id="f4b4c-217">In questa sezione si aggiornano le Razor pagine e si aggiunge una classe dei requisiti delle operazioni.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-217">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="f4b4c-218">Esaminare la classe dei requisiti delle operazioni di contatto</span><span class="sxs-lookup"><span data-stu-id="f4b4c-218">Review the contact operations requirements class</span></span>

<span data-ttu-id="f4b4c-219">Esaminare la `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-219">Review the `ContactOperations` class.</span></span> <span data-ttu-id="f4b4c-220">Questa classe contiene i requisiti supportati dall'app:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-220">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="f4b4c-221">Creare una classe di base per le Razor pagine contatti</span><span class="sxs-lookup"><span data-stu-id="f4b4c-221">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="f4b4c-222">Creare una classe di base che contiene i servizi utilizzati nelle Razor pagine contatti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-222">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="f4b4c-223">La classe base inserisce il codice di inizializzazione in un'unica posizione:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-223">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="f4b4c-224">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-224">The preceding code:</span></span>

* <span data-ttu-id="f4b4c-225">Aggiunge il `IAuthorizationService` servizio per accedere ai gestori di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-225">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="f4b4c-226">Aggiunge il Identity `UserManager` servizio.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-226">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="f4b4c-227">Aggiungere l'oggetto `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-227">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="f4b4c-228">Aggiornare CreateModel</span><span class="sxs-lookup"><span data-stu-id="f4b4c-228">Update the CreateModel</span></span>

<span data-ttu-id="f4b4c-229">Aggiornare il costruttore del modello di pagina create per usare la `DI_BasePageModel` classe di base:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-229">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="f4b4c-230">Aggiornare il `CreateModel.OnPostAsync` metodo a:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-230">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="f4b4c-231">Aggiungere l'ID utente al `Contact` modello.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-231">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="f4b4c-232">Chiamare il gestore autorizzazioni per verificare che l'utente disponga dell'autorizzazione per la creazione di contatti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-232">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="f4b4c-233">Aggiornare IndexModel</span><span class="sxs-lookup"><span data-stu-id="f4b4c-233">Update the IndexModel</span></span>

<span data-ttu-id="f4b4c-234">Aggiornare il `OnGetAsync` metodo in modo da visualizzare solo i contatti approvati per gli utenti generali:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-234">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="f4b4c-235">Aggiornare EditModel</span><span class="sxs-lookup"><span data-stu-id="f4b4c-235">Update the EditModel</span></span>

<span data-ttu-id="f4b4c-236">Aggiungere un gestore autorizzazioni per verificare che l'utente sia proprietario del contatto.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-236">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="f4b4c-237">Poiché l'autorizzazione della risorsa viene convalidata, l' `[Authorize]` attributo non è sufficiente.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-237">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="f4b4c-238">L'app non ha accesso alla risorsa quando vengono valutati gli attributi.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-238">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="f4b4c-239">L'autorizzazione basata sulle risorse deve essere imperativa.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-239">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="f4b4c-240">I controlli devono essere eseguiti una volta che l'app può accedere alla risorsa, eseguendo il caricamento nel modello di pagina o caricando l'app all'interno del gestore stesso.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-240">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="f4b4c-241">Si accede spesso alla risorsa passando la chiave della risorsa.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-241">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="f4b4c-242">Aggiornare DeleteModel</span><span class="sxs-lookup"><span data-stu-id="f4b4c-242">Update the DeleteModel</span></span>

<span data-ttu-id="f4b4c-243">Aggiornare il modello di pagina Elimina per utilizzare il gestore autorizzazione per verificare che l'utente disponga dell'autorizzazione DELETE per il contatto.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-243">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="f4b4c-244">Inserire il servizio di autorizzazione nelle viste</span><span class="sxs-lookup"><span data-stu-id="f4b4c-244">Inject the authorization service into the views</span></span>

<span data-ttu-id="f4b4c-245">Attualmente, l'interfaccia utente Mostra i collegamenti modifica ed Elimina per i contatti che non possono essere modificati dall'utente.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-245">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="f4b4c-246">Inserire il servizio di autorizzazione nel file *pages/_ViewImports. cshtml* in modo che sia disponibile per tutte le visualizzazioni:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-246">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="f4b4c-247">Il markup precedente aggiunge diverse `using` istruzioni.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-247">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="f4b4c-248">Aggiornare i collegamenti **Edit** ed **Delete** in *pages/Contacts/index. cshtml* in modo che vengano sottoposti a rendering solo per gli utenti con le autorizzazioni appropriate:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-248">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="f4b4c-249">Nascondere i collegamenti da utenti che non dispongono dell'autorizzazione per modificare i dati non protegge l'app.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-249">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="f4b4c-250">Nascondere i collegamenti rende l'app più intuitiva visualizzando solo i collegamenti validi.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-250">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="f4b4c-251">Gli utenti possono hackerare gli URL generati per richiamare le operazioni di modifica ed eliminazione sui dati di cui non sono proprietari.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-251">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="f4b4c-252">La Razor pagina o il controller deve applicare i controlli di accesso per proteggere i dati.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-252">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="f4b4c-253">Dettagli aggiornamento</span><span class="sxs-lookup"><span data-stu-id="f4b4c-253">Update Details</span></span>

<span data-ttu-id="f4b4c-254">Aggiornare la visualizzazione dettagli in modo che i responsabili possano approvare o rifiutare i contatti:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-254">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="f4b4c-255">Aggiornare il modello della pagina dei dettagli:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-255">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="f4b4c-256">Aggiungere o rimuovere un utente in un ruolo</span><span class="sxs-lookup"><span data-stu-id="f4b4c-256">Add or remove a user to a role</span></span>

<span data-ttu-id="f4b4c-257">Per informazioni su, vedere [questo problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="f4b4c-257">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="f4b4c-258">Rimozione dei privilegi da un utente.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-258">Removing privileges from a user.</span></span> <span data-ttu-id="f4b4c-259">Ad esempio, disattivare un utente in un'app di chat.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-259">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="f4b4c-260">Aggiunta di privilegi a un utente.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-260">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="f4b4c-261">Differenze tra la sfida e la proibizione</span><span class="sxs-lookup"><span data-stu-id="f4b4c-261">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="f4b4c-262">Questa app imposta i criteri predefiniti per [richiedere l'autenticazione degli utenti](#rau).</span><span class="sxs-lookup"><span data-stu-id="f4b4c-262">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="f4b4c-263">Il codice seguente consente agli utenti anonimi.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-263">The following code allows anonymous users.</span></span> <span data-ttu-id="f4b4c-264">Gli utenti anonimi possono mostrare le differenze tra la richiesta di confronto e la proibizione.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-264">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="f4b4c-265">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-265">In the preceding code:</span></span>

* <span data-ttu-id="f4b4c-266">Quando l'utente **non** è autenticato, `ChallengeResult` viene restituito un oggetto.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-266">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="f4b4c-267">Quando `ChallengeResult` viene restituito un oggetto, l'utente viene reindirizzato alla pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-267">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="f4b4c-268">Quando l'utente viene autenticato, ma non autorizzato, `ForbidResult` viene restituito un oggetto.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-268">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="f4b4c-269">Quando `ForbidResult` viene restituito un oggetto, l'utente viene reindirizzato alla pagina di accesso negato.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-269">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="f4b4c-270">Testare l'app completata</span><span class="sxs-lookup"><span data-stu-id="f4b4c-270">Test the completed app</span></span>

<span data-ttu-id="f4b4c-271">Se non è già stata impostata una password per gli account utente di cui è stato eseguito il seeding, usare lo [strumento Gestione segreta](xref:security/app-secrets#secret-manager) per impostare una password:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-271">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="f4b4c-272">Scegliere una password complessa: usare otto o più caratteri e almeno un carattere maiuscolo, un numero e un simbolo.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-272">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="f4b4c-273">Ad esempio, `Passw0rd!` soddisfa i requisiti per le password complesse.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-273">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="f4b4c-274">Eseguire il comando seguente dalla cartella del progetto, dove `<PW>` è la password:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-274">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="f4b4c-275">Se l'app dispone di contatti:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-275">If the app has contacts:</span></span>

* <span data-ttu-id="f4b4c-276">Eliminare tutti i record nella `Contact` tabella.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-276">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="f4b4c-277">Riavviare l'app per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-277">Restart the app to seed the database.</span></span>

<span data-ttu-id="f4b4c-278">Un modo semplice per testare l'app completata consiste nell'avviare tre diversi browser (o sessioni in incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="f4b4c-278">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="f4b4c-279">In un browser registrare un nuovo utente (ad esempio, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="f4b4c-279">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="f4b4c-280">Accedere a ogni browser con un altro utente.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-280">Sign in to each browser with a different user.</span></span> <span data-ttu-id="f4b4c-281">Verificare le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-281">Verify the following operations:</span></span>

* <span data-ttu-id="f4b4c-282">Gli utenti registrati possono visualizzare tutti i dati di contatto approvati.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-282">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="f4b4c-283">Gli utenti registrati possono modificare/eliminare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-283">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="f4b4c-284">I responsabili possono approvare/rifiutare i dati di contatto.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-284">Managers can approve/reject contact data.</span></span> <span data-ttu-id="f4b4c-285">La `Details` visualizzazione Mostra i pulsanti **approva** e **rifiuta** .</span><span class="sxs-lookup"><span data-stu-id="f4b4c-285">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="f4b4c-286">Gli amministratori possono approvare/rifiutare e modificare/eliminare tutti i dati.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-286">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="f4b4c-287">Utente</span><span class="sxs-lookup"><span data-stu-id="f4b4c-287">User</span></span>                | <span data-ttu-id="f4b4c-288">Seeding dall'app</span><span class="sxs-lookup"><span data-stu-id="f4b4c-288">Seeded by the app</span></span> | <span data-ttu-id="f4b4c-289">Opzioni</span><span class="sxs-lookup"><span data-stu-id="f4b4c-289">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="f4b4c-290">No</span><span class="sxs-lookup"><span data-stu-id="f4b4c-290">No</span></span>                | <span data-ttu-id="f4b4c-291">Modificare/eliminare i dati personali.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-291">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="f4b4c-292">Sì</span><span class="sxs-lookup"><span data-stu-id="f4b4c-292">Yes</span></span>               | <span data-ttu-id="f4b4c-293">Approva/rifiuta e modifica/elimina i dati personali.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-293">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="f4b4c-294">Sì</span><span class="sxs-lookup"><span data-stu-id="f4b4c-294">Yes</span></span>               | <span data-ttu-id="f4b4c-295">Approva/rifiuta e modifica/elimina tutti i dati.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-295">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="f4b4c-296">Creare un contatto nel browser dell'amministratore.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-296">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="f4b4c-297">Copiare l'URL da eliminare e modificare dal contatto amministratore.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-297">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="f4b4c-298">Incollare questi collegamenti nel browser dell'utente test per verificare che l'utente test non possa eseguire queste operazioni.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-298">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="f4b4c-299">Creare l'app Starter</span><span class="sxs-lookup"><span data-stu-id="f4b4c-299">Create the starter app</span></span>

* <span data-ttu-id="f4b4c-300">Creare un' Razor app per le pagine denominata "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="f4b4c-300">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="f4b4c-301">Creare l'app con **singoli account utente**.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-301">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="f4b4c-302">Denominarlo "ContactManager" in modo che lo spazio dei nomi corrisponda allo spazio dei nomi usato nell'esempio.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-302">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="f4b4c-303">`-uld`Specifica il database locale anziché SQLite</span><span class="sxs-lookup"><span data-stu-id="f4b4c-303">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="f4b4c-304">Aggiungi *modelli/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-304">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="f4b4c-305">Impalcatura del `Contact` modello.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-305">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="f4b4c-306">Creare la migrazione iniziale e aggiornare il database:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-306">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="f4b4c-307">Se si verifica un bug con il `dotnet aspnet-codegenerator razorpage` comando, vedere [questo problema di GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="f4b4c-307">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="f4b4c-308">Aggiornare l'ancoraggio **ContactManager** nel file *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f4b4c-308">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="f4b4c-309">Testare l'app creando, modificando ed eliminando un contatto</span><span class="sxs-lookup"><span data-stu-id="f4b4c-309">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="f4b4c-310">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="f4b4c-310">Seed the database</span></span>

<span data-ttu-id="f4b4c-311">Aggiungere la classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) alla cartella *Data* :</span><span class="sxs-lookup"><span data-stu-id="f4b4c-311">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="f4b4c-312">Chiama `SeedData.Initialize` da `Main` :</span><span class="sxs-lookup"><span data-stu-id="f4b4c-312">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="f4b4c-313">Verificare che l'app abbia sottoposta a seeding il database.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-313">Test that the app seeded the database.</span></span> <span data-ttu-id="f4b4c-314">Se sono presenti righe nel database Contact, il metodo seed non viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-314">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="f4b4c-315">Questa esercitazione illustra come creare un'app Web di ASP.NET Core con i dati utente protetti dall'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-315">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="f4b4c-316">Viene visualizzato un elenco di contatti creati dagli utenti autenticati (registrati).</span><span class="sxs-lookup"><span data-stu-id="f4b4c-316">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="f4b4c-317">Sono disponibili tre gruppi di sicurezza:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-317">There are three security groups:</span></span>

* <span data-ttu-id="f4b4c-318">**Gli utenti registrati** possono visualizzare tutti i dati approvati e possono modificare/eliminare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-318">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="f4b4c-319">I **responsabili** possono approvare o rifiutare i dati di contatto.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-319">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="f4b4c-320">Solo i contatti approvati sono visibili agli utenti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-320">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="f4b4c-321">Gli **amministratori** possono approvare/rifiutare e modificare/eliminare i dati.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-321">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="f4b4c-322">Nell'immagine seguente, l'utente Rick ( `rick@example.com` ) ha eseguito l'accesso.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-322">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="f4b4c-323">Rick può visualizzare solo i contatti approvati e **modificare** / **Delete** / **Crea nuovi** collegamenti per i contatti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-323">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="f4b4c-324">Solo l'ultimo record, creato da Rick, Visualizza i collegamenti **modifica** ed **Elimina** .</span><span class="sxs-lookup"><span data-stu-id="f4b4c-324">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="f4b4c-325">Gli altri utenti non vedranno l'ultimo record fino a quando un responsabile o un amministratore non modifica lo stato in "approvato".</span><span class="sxs-lookup"><span data-stu-id="f4b4c-325">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot che illustra l'accesso a Rick](secure-data/_static/rick.png)

<span data-ttu-id="f4b4c-327">Nell'immagine seguente, `manager@contoso.com` è stato eseguito l'accesso e nel ruolo del Manager:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-327">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot che mostra l' manager@contoso.com accesso](secure-data/_static/manager1.png)

<span data-ttu-id="f4b4c-329">Nella figura seguente viene illustrata la visualizzazione dettagli Manager di un contatto:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-329">The following image shows the managers details view of a contact:</span></span>

![Visualizzazione del contatto da un responsabile](secure-data/_static/manager.png)

<span data-ttu-id="f4b4c-331">I pulsanti **approva** e **rifiuta** vengono visualizzati solo per Manager e amministratori.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-331">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="f4b4c-332">Nell'immagine seguente, `admin@contoso.com` è stato eseguito l'accesso e il ruolo dell'amministratore:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-332">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot che mostra l' admin@contoso.com accesso](secure-data/_static/admin.png)

<span data-ttu-id="f4b4c-334">L'amministratore dispone di tutti i privilegi.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-334">The administrator has all privileges.</span></span> <span data-ttu-id="f4b4c-335">Può leggere, modificare ed eliminare qualsiasi contatto e modificare lo stato dei contatti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-335">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="f4b4c-336">L'app è stata creata mediante l' [impalcatura](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) del `Contact` modello seguente:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-336">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="f4b4c-337">L'esempio contiene i gestori di autorizzazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-337">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="f4b4c-338">`ContactIsOwnerAuthorizationHandler`: Assicura che un utente possa modificare solo i propri dati.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-338">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="f4b4c-339">`ContactManagerAuthorizationHandler`: Consente ai responsabili di approvare o rifiutare i contatti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-339">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="f4b4c-340">`ContactAdministratorsAuthorizationHandler`: Consente agli amministratori di approvare o rifiutare contatti e di modificare/eliminare contatti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-340">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f4b4c-341">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="f4b4c-341">Prerequisites</span></span>

<span data-ttu-id="f4b4c-342">Questa esercitazione è avanzata.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-342">This tutorial is advanced.</span></span> <span data-ttu-id="f4b4c-343">È necessario avere familiarità con:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-343">You should be familiar with:</span></span>

* [<span data-ttu-id="f4b4c-344">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f4b4c-344">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="f4b4c-345">autenticazione</span><span class="sxs-lookup"><span data-stu-id="f4b4c-345">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="f4b4c-346">Conferma account e recupero password</span><span class="sxs-lookup"><span data-stu-id="f4b4c-346">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="f4b4c-347">Autorizzazione</span><span class="sxs-lookup"><span data-stu-id="f4b4c-347">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="f4b4c-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="f4b4c-348">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="f4b4c-349">App iniziale e completata</span><span class="sxs-lookup"><span data-stu-id="f4b4c-349">The starter and completed app</span></span>

<span data-ttu-id="f4b4c-350">[Scaricare](xref:index#how-to-download-a-sample) l'app [completata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="f4b4c-350">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="f4b4c-351">[Testare](#test-the-completed-app) l'app completata in modo da acquisire familiarità con le funzionalità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-351">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="f4b4c-352">App iniziale</span><span class="sxs-lookup"><span data-stu-id="f4b4c-352">The starter app</span></span>

<span data-ttu-id="f4b4c-353">[Scaricare](xref:index#how-to-download-a-sample) l'app [Starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="f4b4c-353">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="f4b4c-354">Eseguire l'app, toccare il collegamento **ContactManager** e verificare che sia possibile creare, modificare ed eliminare un contatto.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-354">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="f4b4c-355">Proteggere i dati utente</span><span class="sxs-lookup"><span data-stu-id="f4b4c-355">Secure user data</span></span>

<span data-ttu-id="f4b4c-356">Le sezioni seguenti includono tutti i passaggi principali per creare l'app Secure User Data.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-356">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="f4b4c-357">Può risultare utile fare riferimento al progetto completato.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-357">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="f4b4c-358">Associare i dati di contatto all'utente</span><span class="sxs-lookup"><span data-stu-id="f4b4c-358">Tie the contact data to the user</span></span>

<span data-ttu-id="f4b4c-359">Usare l' [Identity](xref:security/authentication/identity) ID utente ASP.NET per assicurarsi che gli utenti possano modificare i dati, ma non i dati di altri utenti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-359">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="f4b4c-360">Aggiungere `OwnerID` e `ContactStatus` al `Contact` modello:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-360">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="f4b4c-361">`OwnerID`ID dell'utente della `AspNetUser` tabella nel [Identity](xref:security/authentication/identity) database.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-361">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="f4b4c-362">Il `Status` campo determina se un contatto è visualizzabile dagli utenti generali.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-362">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="f4b4c-363">Creare una nuova migrazione e aggiornare il database:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-363">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="f4b4c-364">Aggiungere servizi ruolo aIdentity</span><span class="sxs-lookup"><span data-stu-id="f4b4c-364">Add Role services to Identity</span></span>

<span data-ttu-id="f4b4c-365">Aggiungere [Aggiungi ruoli](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) per aggiungere servizi ruolo:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-365">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="f4b4c-366">Richiedi utenti autenticati</span><span class="sxs-lookup"><span data-stu-id="f4b4c-366">Require authenticated users</span></span>

<span data-ttu-id="f4b4c-367">Impostare i criteri di autenticazione predefiniti per richiedere l'autenticazione degli utenti:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-367">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="f4b4c-368">È possibile rifiutare esplicitamente l'autenticazione a Razor livello di pagina, controller o metodo di azione con l' `[AllowAnonymous]` attributo.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-368">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="f4b4c-369">L'impostazione dei criteri di autenticazione predefiniti per richiedere l'autenticazione degli utenti consente di proteggere le Razor pagine e i controller appena aggiunti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-369">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="f4b4c-370">La necessità di eseguire l'autenticazione per impostazione predefinita è più sicura rispetto a quella di fare affidamento su nuovi controller e Razor pagine per includere l' `[Authorize]` attributo.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-370">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="f4b4c-371">Aggiungere [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) alle pagine di indice, informazioni e contatti in modo che gli utenti anonimi possano ottenere informazioni sul sito prima della registrazione.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-371">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="f4b4c-372">Configurare l'account di test</span><span class="sxs-lookup"><span data-stu-id="f4b4c-372">Configure the test account</span></span>

<span data-ttu-id="f4b4c-373">La `SeedData` classe crea due account: Administrator e Manager.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-373">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="f4b4c-374">Utilizzare lo [strumento Gestione segreta](xref:security/app-secrets) per impostare una password per questi account.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-374">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="f4b4c-375">Impostare la password dalla directory del progetto (la directory contenente *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="f4b4c-375">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="f4b4c-376">Se non si specifica una password complessa, viene generata un'eccezione quando `SeedData.Initialize` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-376">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="f4b4c-377">Aggiornare `Main` per usare la password di test:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-377">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="f4b4c-378">Creare gli account di test e aggiornare i contatti</span><span class="sxs-lookup"><span data-stu-id="f4b4c-378">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="f4b4c-379">Aggiornare il `Initialize` metodo nella `SeedData` classe per creare gli account di test:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-379">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="f4b4c-380">Aggiungere l'ID utente amministratore e `ContactStatus` ai contatti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-380">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="f4b4c-381">Creare uno dei contatti "inviato" e uno "rifiutato".</span><span class="sxs-lookup"><span data-stu-id="f4b4c-381">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="f4b4c-382">Aggiungere l'ID utente e lo stato a tutti i contatti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-382">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="f4b4c-383">Viene visualizzato un solo contatto:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-383">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="f4b4c-384">Creazione di gestori di autorizzazioni proprietario, Manager e amministratore</span><span class="sxs-lookup"><span data-stu-id="f4b4c-384">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="f4b4c-385">Creare una cartella di *autorizzazione* e crearvi una `ContactIsOwnerAuthorizationHandler` classe.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-385">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="f4b4c-386">`ContactIsOwnerAuthorizationHandler`Verifica che l'utente che agisce su una risorsa appartenga alla risorsa.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-386">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="f4b4c-387">`ContactIsOwnerAuthorizationHandler`Contesto delle chiamate [. Ha esito positivo](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se l'utente autenticato corrente è il proprietario del contatto.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-387">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="f4b4c-388">Gestori autorizzazioni in genere:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-388">Authorization handlers generally:</span></span>

* <span data-ttu-id="f4b4c-389">Restituisce `context.Succeed` quando vengono soddisfatti i requisiti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-389">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="f4b4c-390">Restituisce `Task.CompletedTask` quando i requisiti non vengono soddisfatti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-390">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="f4b4c-391">`Task.CompletedTask`non ha esito positivo o negativo &mdash; consente l'esecuzione di altri gestori di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-391">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="f4b4c-392">Se è necessario eseguire in modo esplicito l'errore, restituire [context. Esito negativo](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="f4b4c-392">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="f4b4c-393">L'app consente ai proprietari dei contatti di modificare/eliminare/creare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-393">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="f4b4c-394">`ContactIsOwnerAuthorizationHandler`non è necessario controllare l'operazione passata nel parametro requirement.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-394">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="f4b4c-395">Creazione di un gestore autorizzazioni di gestione</span><span class="sxs-lookup"><span data-stu-id="f4b4c-395">Create a manager authorization handler</span></span>

<span data-ttu-id="f4b4c-396">Creare una `ContactManagerAuthorizationHandler` classe nella cartella *authorization* .</span><span class="sxs-lookup"><span data-stu-id="f4b4c-396">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="f4b4c-397">`ContactManagerAuthorizationHandler`Verifica che l'utente che agisce sulla risorsa sia un responsabile.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-397">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="f4b4c-398">Solo i responsabili possono approvare o rifiutare le modifiche al contenuto (nuove o modificate).</span><span class="sxs-lookup"><span data-stu-id="f4b4c-398">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="f4b4c-399">Creazione di un gestore autorizzazioni Amministratore</span><span class="sxs-lookup"><span data-stu-id="f4b4c-399">Create an administrator authorization handler</span></span>

<span data-ttu-id="f4b4c-400">Creare una `ContactAdministratorsAuthorizationHandler` classe nella cartella *authorization* .</span><span class="sxs-lookup"><span data-stu-id="f4b4c-400">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="f4b4c-401">Il `ContactAdministratorsAuthorizationHandler` Verifica che l'utente che agisce sulla risorsa sia un amministratore.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-401">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="f4b4c-402">L'amministratore può eseguire tutte le operazioni.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-402">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="f4b4c-403">Registrare i gestori di autorizzazioni</span><span class="sxs-lookup"><span data-stu-id="f4b4c-403">Register the authorization handlers</span></span>

<span data-ttu-id="f4b4c-404">I servizi che usano Entity Framework Core devono essere registrati per l' [inserimento di dipendenze](xref:fundamentals/dependency-injection) usando [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="f4b4c-404">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="f4b4c-405">`ContactIsOwnerAuthorizationHandler`Usa ASP.NET Core [Identity](xref:security/authentication/identity) , che è basato su Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-405">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="f4b4c-406">Registrare i gestori con la raccolta di servizi in modo che siano disponibili per `ContactsController` tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f4b4c-406">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f4b4c-407">Aggiungere il codice seguente alla fine di `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f4b4c-407">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="f4b4c-408">`ContactAdministratorsAuthorizationHandler`e `ContactManagerAuthorizationHandler` vengono aggiunti come singleton.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-408">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="f4b4c-409">Si tratta di singleton perché non usano EF e tutte le informazioni necessarie si trovano nel `Context` parametro del `HandleRequirementAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-409">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="f4b4c-410">Autorizzazione supporto</span><span class="sxs-lookup"><span data-stu-id="f4b4c-410">Support authorization</span></span>

<span data-ttu-id="f4b4c-411">In questa sezione si aggiornano le Razor pagine e si aggiunge una classe dei requisiti delle operazioni.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-411">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="f4b4c-412">Esaminare la classe dei requisiti delle operazioni di contatto</span><span class="sxs-lookup"><span data-stu-id="f4b4c-412">Review the contact operations requirements class</span></span>

<span data-ttu-id="f4b4c-413">Esaminare la `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-413">Review the `ContactOperations` class.</span></span> <span data-ttu-id="f4b4c-414">Questa classe contiene i requisiti supportati dall'app:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-414">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="f4b4c-415">Creare una classe di base per le Razor pagine contatti</span><span class="sxs-lookup"><span data-stu-id="f4b4c-415">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="f4b4c-416">Creare una classe di base che contiene i servizi utilizzati nelle Razor pagine contatti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-416">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="f4b4c-417">La classe base inserisce il codice di inizializzazione in un'unica posizione:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-417">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="f4b4c-418">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-418">The preceding code:</span></span>

* <span data-ttu-id="f4b4c-419">Aggiunge il `IAuthorizationService` servizio per accedere ai gestori di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-419">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="f4b4c-420">Aggiunge il Identity `UserManager` servizio.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-420">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="f4b4c-421">Aggiungere l'oggetto `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-421">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="f4b4c-422">Aggiornare CreateModel</span><span class="sxs-lookup"><span data-stu-id="f4b4c-422">Update the CreateModel</span></span>

<span data-ttu-id="f4b4c-423">Aggiornare il costruttore del modello di pagina create per usare la `DI_BasePageModel` classe di base:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-423">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="f4b4c-424">Aggiornare il `CreateModel.OnPostAsync` metodo a:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-424">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="f4b4c-425">Aggiungere l'ID utente al `Contact` modello.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-425">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="f4b4c-426">Chiamare il gestore autorizzazioni per verificare che l'utente disponga dell'autorizzazione per la creazione di contatti.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-426">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="f4b4c-427">Aggiornare IndexModel</span><span class="sxs-lookup"><span data-stu-id="f4b4c-427">Update the IndexModel</span></span>

<span data-ttu-id="f4b4c-428">Aggiornare il `OnGetAsync` metodo in modo da visualizzare solo i contatti approvati per gli utenti generali:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-428">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="f4b4c-429">Aggiornare EditModel</span><span class="sxs-lookup"><span data-stu-id="f4b4c-429">Update the EditModel</span></span>

<span data-ttu-id="f4b4c-430">Aggiungere un gestore autorizzazioni per verificare che l'utente sia proprietario del contatto.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-430">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="f4b4c-431">Poiché l'autorizzazione della risorsa viene convalidata, l' `[Authorize]` attributo non è sufficiente.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-431">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="f4b4c-432">L'app non ha accesso alla risorsa quando vengono valutati gli attributi.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-432">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="f4b4c-433">L'autorizzazione basata sulle risorse deve essere imperativa.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-433">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="f4b4c-434">I controlli devono essere eseguiti una volta che l'app può accedere alla risorsa, eseguendo il caricamento nel modello di pagina o caricando l'app all'interno del gestore stesso.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-434">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="f4b4c-435">Si accede spesso alla risorsa passando la chiave della risorsa.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-435">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="f4b4c-436">Aggiornare DeleteModel</span><span class="sxs-lookup"><span data-stu-id="f4b4c-436">Update the DeleteModel</span></span>

<span data-ttu-id="f4b4c-437">Aggiornare il modello di pagina Elimina per utilizzare il gestore autorizzazione per verificare che l'utente disponga dell'autorizzazione DELETE per il contatto.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-437">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="f4b4c-438">Inserire il servizio di autorizzazione nelle viste</span><span class="sxs-lookup"><span data-stu-id="f4b4c-438">Inject the authorization service into the views</span></span>

<span data-ttu-id="f4b4c-439">Attualmente, l'interfaccia utente Mostra i collegamenti modifica ed Elimina per i contatti che non possono essere modificati dall'utente.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-439">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="f4b4c-440">Inserire il servizio di autorizzazione nel file *views/_ViewImports. cshtml* in modo che sia disponibile per tutte le visualizzazioni:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-440">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="f4b4c-441">Il markup precedente aggiunge diverse `using` istruzioni.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-441">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="f4b4c-442">Aggiornare i collegamenti **Edit** ed **Delete** in *pages/Contacts/index. cshtml* in modo che vengano sottoposti a rendering solo per gli utenti con le autorizzazioni appropriate:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-442">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="f4b4c-443">Nascondere i collegamenti da utenti che non dispongono dell'autorizzazione per modificare i dati non protegge l'app.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-443">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="f4b4c-444">Nascondere i collegamenti rende l'app più intuitiva visualizzando solo i collegamenti validi.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-444">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="f4b4c-445">Gli utenti possono hackerare gli URL generati per richiamare le operazioni di modifica ed eliminazione sui dati di cui non sono proprietari.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-445">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="f4b4c-446">La Razor pagina o il controller deve applicare i controlli di accesso per proteggere i dati.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-446">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="f4b4c-447">Dettagli aggiornamento</span><span class="sxs-lookup"><span data-stu-id="f4b4c-447">Update Details</span></span>

<span data-ttu-id="f4b4c-448">Aggiornare la visualizzazione dettagli in modo che i responsabili possano approvare o rifiutare i contatti:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-448">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="f4b4c-449">Aggiornare il modello della pagina dei dettagli:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-449">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="f4b4c-450">Aggiungere o rimuovere un utente in un ruolo</span><span class="sxs-lookup"><span data-stu-id="f4b4c-450">Add or remove a user to a role</span></span>

<span data-ttu-id="f4b4c-451">Per informazioni su, vedere [questo problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="f4b4c-451">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="f4b4c-452">Rimozione dei privilegi da un utente.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-452">Removing privileges from a user.</span></span> <span data-ttu-id="f4b4c-453">Ad esempio, disattivare un utente in un'app di chat.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-453">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="f4b4c-454">Aggiunta di privilegi a un utente.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-454">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="f4b4c-455">Testare l'app completata</span><span class="sxs-lookup"><span data-stu-id="f4b4c-455">Test the completed app</span></span>

<span data-ttu-id="f4b4c-456">Se non è già stata impostata una password per gli account utente di cui è stato eseguito il seeding, usare lo [strumento Gestione segreta](xref:security/app-secrets#secret-manager) per impostare una password:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-456">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="f4b4c-457">Scegliere una password complessa: usare otto o più caratteri e almeno un carattere maiuscolo, un numero e un simbolo.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-457">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="f4b4c-458">Ad esempio, `Passw0rd!` soddisfa i requisiti per le password complesse.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-458">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="f4b4c-459">Eseguire il comando seguente dalla cartella del progetto, dove `<PW>` è la password:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-459">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="f4b4c-460">Eliminare e aggiornare il database</span><span class="sxs-lookup"><span data-stu-id="f4b4c-460">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="f4b4c-461">Riavviare l'app per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-461">Restart the app to seed the database.</span></span>

<span data-ttu-id="f4b4c-462">Un modo semplice per testare l'app completata consiste nell'avviare tre diversi browser (o sessioni in incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="f4b4c-462">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="f4b4c-463">In un browser registrare un nuovo utente (ad esempio, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="f4b4c-463">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="f4b4c-464">Accedere a ogni browser con un altro utente.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-464">Sign in to each browser with a different user.</span></span> <span data-ttu-id="f4b4c-465">Verificare le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-465">Verify the following operations:</span></span>

* <span data-ttu-id="f4b4c-466">Gli utenti registrati possono visualizzare tutti i dati di contatto approvati.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-466">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="f4b4c-467">Gli utenti registrati possono modificare/eliminare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-467">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="f4b4c-468">I responsabili possono approvare/rifiutare i dati di contatto.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-468">Managers can approve/reject contact data.</span></span> <span data-ttu-id="f4b4c-469">La `Details` visualizzazione Mostra i pulsanti **approva** e **rifiuta** .</span><span class="sxs-lookup"><span data-stu-id="f4b4c-469">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="f4b4c-470">Gli amministratori possono approvare/rifiutare e modificare/eliminare tutti i dati.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-470">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="f4b4c-471">Utente</span><span class="sxs-lookup"><span data-stu-id="f4b4c-471">User</span></span>                | <span data-ttu-id="f4b4c-472">Seeding dall'app</span><span class="sxs-lookup"><span data-stu-id="f4b4c-472">Seeded by the app</span></span> | <span data-ttu-id="f4b4c-473">Opzioni</span><span class="sxs-lookup"><span data-stu-id="f4b4c-473">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="f4b4c-474">No</span><span class="sxs-lookup"><span data-stu-id="f4b4c-474">No</span></span>                | <span data-ttu-id="f4b4c-475">Modificare/eliminare i dati personali.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-475">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="f4b4c-476">Sì</span><span class="sxs-lookup"><span data-stu-id="f4b4c-476">Yes</span></span>               | <span data-ttu-id="f4b4c-477">Approva/rifiuta e modifica/elimina i dati personali.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-477">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="f4b4c-478">Sì</span><span class="sxs-lookup"><span data-stu-id="f4b4c-478">Yes</span></span>               | <span data-ttu-id="f4b4c-479">Approva/rifiuta e modifica/elimina tutti i dati.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-479">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="f4b4c-480">Creare un contatto nel browser dell'amministratore.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-480">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="f4b4c-481">Copiare l'URL da eliminare e modificare dal contatto amministratore.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-481">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="f4b4c-482">Incollare questi collegamenti nel browser dell'utente test per verificare che l'utente test non possa eseguire queste operazioni.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-482">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="f4b4c-483">Creare l'app Starter</span><span class="sxs-lookup"><span data-stu-id="f4b4c-483">Create the starter app</span></span>

* <span data-ttu-id="f4b4c-484">Creare un' Razor app per le pagine denominata "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="f4b4c-484">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="f4b4c-485">Creare l'app con **singoli account utente**.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-485">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="f4b4c-486">Denominarlo "ContactManager" in modo che lo spazio dei nomi corrisponda allo spazio dei nomi usato nell'esempio.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-486">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="f4b4c-487">`-uld`Specifica il database locale anziché SQLite</span><span class="sxs-lookup"><span data-stu-id="f4b4c-487">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="f4b4c-488">Aggiungi *modelli/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-488">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="f4b4c-489">Impalcatura del `Contact` modello.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-489">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="f4b4c-490">Creare la migrazione iniziale e aggiornare il database:</span><span class="sxs-lookup"><span data-stu-id="f4b4c-490">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="f4b4c-491">Aggiornare l'ancoraggio **ContactManager** nel file *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f4b4c-491">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="f4b4c-492">Testare l'app creando, modificando ed eliminando un contatto</span><span class="sxs-lookup"><span data-stu-id="f4b4c-492">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="f4b4c-493">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="f4b4c-493">Seed the database</span></span>

<span data-ttu-id="f4b4c-494">Aggiungere la classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) alla cartella *Data* .</span><span class="sxs-lookup"><span data-stu-id="f4b4c-494">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="f4b4c-495">Chiama `SeedData.Initialize` da `Main` :</span><span class="sxs-lookup"><span data-stu-id="f4b4c-495">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="f4b4c-496">Verificare che l'app abbia sottoposta a seeding il database.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-496">Test that the app seeded the database.</span></span> <span data-ttu-id="f4b4c-497">Se sono presenti righe nel database Contact, il metodo seed non viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-497">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="f4b4c-498">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="f4b4c-498">Additional resources</span></span>

* [<span data-ttu-id="f4b4c-499">Creare un'app Web .NET Core e database SQL nel Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="f4b4c-499">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="f4b4c-500">[ASP.NET Core Lab di autorizzazione](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="f4b4c-500">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="f4b4c-501">Questo Lab illustra in modo più dettagliato le funzionalità di sicurezza introdotte in questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="f4b4c-501">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="f4b4c-502">Autorizzazione personalizzata basata su criteri</span><span class="sxs-lookup"><span data-stu-id="f4b4c-502">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
