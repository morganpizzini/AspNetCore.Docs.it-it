---
title: Creare un'app ASP.NET Core con i dati utente protetti dall'autorizzazione
author: rick-anderson
description: Informazioni su come creare un' Razor app pagine con dati utente protetti tramite autorizzazione. Include HTTPS, autenticazione, sicurezza ASP.NET Core Identity.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/secure-data
ms.openlocfilehash: f52b08786dde54e7dcbd2e00f43badb58879cf79
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775752"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="31d5f-104">Creare un'app ASP.NET Core con i dati utente protetti dall'autorizzazione</span><span class="sxs-lookup"><span data-stu-id="31d5f-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="31d5f-105">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="31d5f-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="31d5f-106">Vedere [questo file PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) per la versione ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="31d5f-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="31d5f-107">La versione ASP.NET Core 1,1 di questa esercitazione si trova in [questa](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) cartella.</span><span class="sxs-lookup"><span data-stu-id="31d5f-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="31d5f-108">L'esempio 1,1 ASP.NET Core è presente negli [esempi](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span><span class="sxs-lookup"><span data-stu-id="31d5f-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="31d5f-109">Vedi [questo PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="31d5f-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="31d5f-110">Questa esercitazione illustra come creare un'app Web di ASP.NET Core con i dati utente protetti dall'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="31d5f-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="31d5f-111">Viene visualizzato un elenco di contatti creati dagli utenti autenticati (registrati).</span><span class="sxs-lookup"><span data-stu-id="31d5f-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="31d5f-112">Sono disponibili tre gruppi di sicurezza:</span><span class="sxs-lookup"><span data-stu-id="31d5f-112">There are three security groups:</span></span>

* <span data-ttu-id="31d5f-113">**Gli utenti registrati** possono visualizzare tutti i dati approvati e possono modificare/eliminare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="31d5f-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="31d5f-114">I **responsabili** possono approvare o rifiutare i dati di contatto.</span><span class="sxs-lookup"><span data-stu-id="31d5f-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="31d5f-115">Solo i contatti approvati sono visibili agli utenti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="31d5f-116">Gli **amministratori** possono approvare/rifiutare e modificare/eliminare i dati.</span><span class="sxs-lookup"><span data-stu-id="31d5f-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="31d5f-117">Le immagini in questo documento non corrispondono esattamente ai modelli più recenti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="31d5f-118">Nell'immagine seguente, l'utente Rick (`rick@example.com`) ha eseguito l'accesso.</span><span class="sxs-lookup"><span data-stu-id="31d5f-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="31d5f-119">Rick può visualizzare solo i contatti approvati e **modificare**/**Delete**/**Crea nuovi** collegamenti per i contatti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="31d5f-120">Solo l'ultimo record, creato da Rick, Visualizza i collegamenti **modifica** ed **Elimina** .</span><span class="sxs-lookup"><span data-stu-id="31d5f-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="31d5f-121">Gli altri utenti non vedranno l'ultimo record fino a quando un responsabile o un amministratore non modifica lo stato in "approvato".</span><span class="sxs-lookup"><span data-stu-id="31d5f-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot che illustra l'accesso a Rick](secure-data/_static/rick.png)

<span data-ttu-id="31d5f-123">Nell'immagine seguente, `manager@contoso.com` è stato eseguito l'accesso e nel ruolo del Manager:</span><span class="sxs-lookup"><span data-stu-id="31d5f-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot che manager@contoso.com Mostra l'accesso](secure-data/_static/manager1.png)

<span data-ttu-id="31d5f-125">Nella figura seguente viene illustrata la visualizzazione dettagli Manager di un contatto:</span><span class="sxs-lookup"><span data-stu-id="31d5f-125">The following image shows the managers details view of a contact:</span></span>

![Visualizzazione del contatto da un responsabile](secure-data/_static/manager.png)

<span data-ttu-id="31d5f-127">I pulsanti **approva** e **rifiuta** vengono visualizzati solo per Manager e amministratori.</span><span class="sxs-lookup"><span data-stu-id="31d5f-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="31d5f-128">Nell'immagine seguente, `admin@contoso.com` è stato eseguito l'accesso e il ruolo dell'amministratore:</span><span class="sxs-lookup"><span data-stu-id="31d5f-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot che admin@contoso.com Mostra l'accesso](secure-data/_static/admin.png)

<span data-ttu-id="31d5f-130">L'amministratore dispone di tutti i privilegi.</span><span class="sxs-lookup"><span data-stu-id="31d5f-130">The administrator has all privileges.</span></span> <span data-ttu-id="31d5f-131">Può leggere, modificare ed eliminare qualsiasi contatto e modificare lo stato dei contatti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="31d5f-132">L'app è stata creata mediante l' [impalcatura](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) del modello seguente `Contact` :</span><span class="sxs-lookup"><span data-stu-id="31d5f-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="31d5f-133">L'esempio contiene i gestori di autorizzazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="31d5f-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="31d5f-134">`ContactIsOwnerAuthorizationHandler`: Assicura che un utente possa modificare solo i propri dati.</span><span class="sxs-lookup"><span data-stu-id="31d5f-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="31d5f-135">`ContactManagerAuthorizationHandler`: Consente ai responsabili di approvare o rifiutare i contatti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="31d5f-136">`ContactAdministratorsAuthorizationHandler`: Consente agli amministratori di approvare o rifiutare contatti e di modificare/eliminare contatti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="31d5f-137">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="31d5f-137">Prerequisites</span></span>

<span data-ttu-id="31d5f-138">Questa esercitazione è avanzata.</span><span class="sxs-lookup"><span data-stu-id="31d5f-138">This tutorial is advanced.</span></span> <span data-ttu-id="31d5f-139">È necessario avere familiarità con:</span><span class="sxs-lookup"><span data-stu-id="31d5f-139">You should be familiar with:</span></span>

* [<span data-ttu-id="31d5f-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="31d5f-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="31d5f-141">autenticazione</span><span class="sxs-lookup"><span data-stu-id="31d5f-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="31d5f-142">Conferma account e recupero password</span><span class="sxs-lookup"><span data-stu-id="31d5f-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="31d5f-143">Autorizzazione</span><span class="sxs-lookup"><span data-stu-id="31d5f-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="31d5f-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="31d5f-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="31d5f-145">App iniziale e completata</span><span class="sxs-lookup"><span data-stu-id="31d5f-145">The starter and completed app</span></span>

<span data-ttu-id="31d5f-146">[Scaricare](xref:index#how-to-download-a-sample) l'app [completata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="31d5f-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="31d5f-147">[Testare](#test-the-completed-app) l'app completata in modo da acquisire familiarità con le funzionalità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="31d5f-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="31d5f-148">App iniziale</span><span class="sxs-lookup"><span data-stu-id="31d5f-148">The starter app</span></span>

<span data-ttu-id="31d5f-149">[Scaricare](xref:index#how-to-download-a-sample) l'app [Starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="31d5f-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="31d5f-150">Eseguire l'app, toccare il collegamento **ContactManager** e verificare che sia possibile creare, modificare ed eliminare un contatto.</span><span class="sxs-lookup"><span data-stu-id="31d5f-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="31d5f-151">Proteggere i dati utente</span><span class="sxs-lookup"><span data-stu-id="31d5f-151">Secure user data</span></span>

<span data-ttu-id="31d5f-152">Le sezioni seguenti includono tutti i passaggi principali per creare l'app Secure User Data.</span><span class="sxs-lookup"><span data-stu-id="31d5f-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="31d5f-153">Può risultare utile fare riferimento al progetto completato.</span><span class="sxs-lookup"><span data-stu-id="31d5f-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="31d5f-154">Associare i dati di contatto all'utente</span><span class="sxs-lookup"><span data-stu-id="31d5f-154">Tie the contact data to the user</span></span>

<span data-ttu-id="31d5f-155">Usare l'ID utente dell' [identità](xref:security/authentication/identity) ASP.NET per assicurarsi che gli utenti possano modificare i dati, ma non i dati di altri utenti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="31d5f-156">Aggiungere `OwnerID` e `ContactStatus` al `Contact` modello:</span><span class="sxs-lookup"><span data-stu-id="31d5f-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="31d5f-157">`OwnerID`ID dell'utente della `AspNetUser` tabella nel database di [identità](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="31d5f-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="31d5f-158">Il `Status` campo determina se un contatto è visualizzabile dagli utenti generali.</span><span class="sxs-lookup"><span data-stu-id="31d5f-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="31d5f-159">Creare una nuova migrazione e aggiornare il database:</span><span class="sxs-lookup"><span data-stu-id="31d5f-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="31d5f-160">Aggiungere servizi ruolo all'identità</span><span class="sxs-lookup"><span data-stu-id="31d5f-160">Add Role services to Identity</span></span>

<span data-ttu-id="31d5f-161">Aggiungere [Aggiungi ruoli](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) per aggiungere servizi ruolo:</span><span class="sxs-lookup"><span data-stu-id="31d5f-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="31d5f-162">Richiedi utenti autenticati</span><span class="sxs-lookup"><span data-stu-id="31d5f-162">Require authenticated users</span></span>

<span data-ttu-id="31d5f-163">Impostare i criteri di autenticazione predefiniti per richiedere l'autenticazione degli utenti:</span><span class="sxs-lookup"><span data-stu-id="31d5f-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="31d5f-164">È possibile rifiutare esplicitamente l'autenticazione a livello di pagina Razor, controller o metodo di azione con `[AllowAnonymous]` l'attributo.</span><span class="sxs-lookup"><span data-stu-id="31d5f-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="31d5f-165">L'impostazione dei criteri di autenticazione predefiniti in modo da richiedere l'autenticazione degli utenti consente di proteggere Razor Pages e controller appena aggiunti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="31d5f-166">La necessità di eseguire l'autenticazione per impostazione predefinita è più sicura rispetto all'uso di nuovi controller e `[Authorize]` Razor Pages per includere l'attributo.</span><span class="sxs-lookup"><span data-stu-id="31d5f-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="31d5f-167">Aggiungere [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) alle pagine indici e privacy in modo che gli utenti anonimi possano ottenere informazioni sul sito prima della registrazione.</span><span class="sxs-lookup"><span data-stu-id="31d5f-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="31d5f-168">Configurare l'account di test</span><span class="sxs-lookup"><span data-stu-id="31d5f-168">Configure the test account</span></span>

<span data-ttu-id="31d5f-169">La `SeedData` classe crea due account: Administrator e Manager.</span><span class="sxs-lookup"><span data-stu-id="31d5f-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="31d5f-170">Utilizzare lo [strumento Gestione segreta](xref:security/app-secrets) per impostare una password per questi account.</span><span class="sxs-lookup"><span data-stu-id="31d5f-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="31d5f-171">Impostare la password dalla directory del progetto (la directory contenente *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="31d5f-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="31d5f-172">Se non si specifica una password complessa, viene generata un'eccezione quando `SeedData.Initialize` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="31d5f-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="31d5f-173">Aggiornare `Main` per usare la password di test:</span><span class="sxs-lookup"><span data-stu-id="31d5f-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="31d5f-174">Creare gli account di test e aggiornare i contatti</span><span class="sxs-lookup"><span data-stu-id="31d5f-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="31d5f-175">Aggiornare il `Initialize` metodo nella `SeedData` classe per creare gli account di test:</span><span class="sxs-lookup"><span data-stu-id="31d5f-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="31d5f-176">Aggiungere l'ID utente amministratore e `ContactStatus` ai contatti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="31d5f-177">Creare uno dei contatti "inviato" e uno "rifiutato".</span><span class="sxs-lookup"><span data-stu-id="31d5f-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="31d5f-178">Aggiungere l'ID utente e lo stato a tutti i contatti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="31d5f-179">Viene visualizzato un solo contatto:</span><span class="sxs-lookup"><span data-stu-id="31d5f-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="31d5f-180">Creazione di gestori di autorizzazioni proprietario, Manager e amministratore</span><span class="sxs-lookup"><span data-stu-id="31d5f-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="31d5f-181">Creare una `ContactIsOwnerAuthorizationHandler` classe nella cartella *authorization* .</span><span class="sxs-lookup"><span data-stu-id="31d5f-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="31d5f-182">`ContactIsOwnerAuthorizationHandler` Verifica che l'utente che agisce su una risorsa appartenga alla risorsa.</span><span class="sxs-lookup"><span data-stu-id="31d5f-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="31d5f-183">Contesto `ContactIsOwnerAuthorizationHandler` delle chiamate [. Ha esito positivo](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se l'utente autenticato corrente è il proprietario del contatto.</span><span class="sxs-lookup"><span data-stu-id="31d5f-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="31d5f-184">Gestori autorizzazioni in genere:</span><span class="sxs-lookup"><span data-stu-id="31d5f-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="31d5f-185">Restituisce `context.Succeed` quando vengono soddisfatti i requisiti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="31d5f-186">Restituisce `Task.CompletedTask` quando i requisiti non vengono soddisfatti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="31d5f-187">`Task.CompletedTask`non ha esito positivo&mdash;o negativo consente l'esecuzione di altri gestori di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="31d5f-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="31d5f-188">Se è necessario eseguire in modo esplicito l'errore, restituire [context. Esito negativo](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="31d5f-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="31d5f-189">L'app consente ai proprietari dei contatti di modificare/eliminare/creare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="31d5f-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="31d5f-190">`ContactIsOwnerAuthorizationHandler`non è necessario controllare l'operazione passata nel parametro requirement.</span><span class="sxs-lookup"><span data-stu-id="31d5f-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="31d5f-191">Creazione di un gestore autorizzazioni di gestione</span><span class="sxs-lookup"><span data-stu-id="31d5f-191">Create a manager authorization handler</span></span>

<span data-ttu-id="31d5f-192">Creare una `ContactManagerAuthorizationHandler` classe nella cartella *authorization* .</span><span class="sxs-lookup"><span data-stu-id="31d5f-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="31d5f-193">`ContactManagerAuthorizationHandler` Verifica che l'utente che agisce sulla risorsa sia un responsabile.</span><span class="sxs-lookup"><span data-stu-id="31d5f-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="31d5f-194">Solo i responsabili possono approvare o rifiutare le modifiche al contenuto (nuove o modificate).</span><span class="sxs-lookup"><span data-stu-id="31d5f-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="31d5f-195">Creazione di un gestore autorizzazioni Amministratore</span><span class="sxs-lookup"><span data-stu-id="31d5f-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="31d5f-196">Creare una `ContactAdministratorsAuthorizationHandler` classe nella cartella *authorization* .</span><span class="sxs-lookup"><span data-stu-id="31d5f-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="31d5f-197">Il `ContactAdministratorsAuthorizationHandler` verifica che l'utente che agisce sulla risorsa sia un amministratore.</span><span class="sxs-lookup"><span data-stu-id="31d5f-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="31d5f-198">L'amministratore può eseguire tutte le operazioni.</span><span class="sxs-lookup"><span data-stu-id="31d5f-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="31d5f-199">Registrare i gestori di autorizzazioni</span><span class="sxs-lookup"><span data-stu-id="31d5f-199">Register the authorization handlers</span></span>

<span data-ttu-id="31d5f-200">I servizi che usano Entity Framework Core devono essere registrati per l' [inserimento di dipendenze](xref:fundamentals/dependency-injection) usando [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="31d5f-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="31d5f-201">`ContactIsOwnerAuthorizationHandler` USA ASP.NET Core [identità](xref:security/authentication/identity), che si basa su Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="31d5f-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="31d5f-202">Registrare i gestori con la raccolta di servizi in modo che siano disponibili per `ContactsController` tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="31d5f-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="31d5f-203">Aggiungere il codice seguente alla fine di `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="31d5f-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="31d5f-204">`ContactAdministratorsAuthorizationHandler`e `ContactManagerAuthorizationHandler` vengono aggiunti come singleton.</span><span class="sxs-lookup"><span data-stu-id="31d5f-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="31d5f-205">Si tratta di singleton perché non usano EF e tutte le informazioni necessarie si trovano nel `Context` parametro del `HandleRequirementAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="31d5f-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="31d5f-206">Autorizzazione supporto</span><span class="sxs-lookup"><span data-stu-id="31d5f-206">Support authorization</span></span>

<span data-ttu-id="31d5f-207">In questa sezione si aggiorna il Razor Pages e si aggiunge una classe dei requisiti delle operazioni.</span><span class="sxs-lookup"><span data-stu-id="31d5f-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="31d5f-208">Esaminare la classe dei requisiti delle operazioni di contatto</span><span class="sxs-lookup"><span data-stu-id="31d5f-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="31d5f-209">Esaminare la `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="31d5f-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="31d5f-210">Questa classe contiene i requisiti supportati dall'app:</span><span class="sxs-lookup"><span data-stu-id="31d5f-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="31d5f-211">Creare una classe di base per i contatti Razor Pages</span><span class="sxs-lookup"><span data-stu-id="31d5f-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="31d5f-212">Creare una classe di base che contiene i servizi utilizzati nell'Razor Pages contatti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="31d5f-213">La classe base inserisce il codice di inizializzazione in un'unica posizione:</span><span class="sxs-lookup"><span data-stu-id="31d5f-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="31d5f-214">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="31d5f-214">The preceding code:</span></span>

* <span data-ttu-id="31d5f-215">Aggiunge il `IAuthorizationService` servizio per accedere ai gestori di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="31d5f-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="31d5f-216">Aggiunge il servizio `UserManager` di identità.</span><span class="sxs-lookup"><span data-stu-id="31d5f-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="31d5f-217">Aggiungere l'oggetto `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="31d5f-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="31d5f-218">Aggiornare CreateModel</span><span class="sxs-lookup"><span data-stu-id="31d5f-218">Update the CreateModel</span></span>

<span data-ttu-id="31d5f-219">Aggiornare il costruttore del modello di pagina create per `DI_BasePageModel` usare la classe di base:</span><span class="sxs-lookup"><span data-stu-id="31d5f-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="31d5f-220">Aggiornare il `CreateModel.OnPostAsync` metodo a:</span><span class="sxs-lookup"><span data-stu-id="31d5f-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="31d5f-221">Aggiungere l'ID utente al `Contact` modello.</span><span class="sxs-lookup"><span data-stu-id="31d5f-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="31d5f-222">Chiamare il gestore autorizzazioni per verificare che l'utente disponga dell'autorizzazione per la creazione di contatti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="31d5f-223">Aggiornare IndexModel</span><span class="sxs-lookup"><span data-stu-id="31d5f-223">Update the IndexModel</span></span>

<span data-ttu-id="31d5f-224">Aggiornare il `OnGetAsync` metodo in modo da visualizzare solo i contatti approvati per gli utenti generali:</span><span class="sxs-lookup"><span data-stu-id="31d5f-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="31d5f-225">Aggiornare EditModel</span><span class="sxs-lookup"><span data-stu-id="31d5f-225">Update the EditModel</span></span>

<span data-ttu-id="31d5f-226">Aggiungere un gestore autorizzazioni per verificare che l'utente sia proprietario del contatto.</span><span class="sxs-lookup"><span data-stu-id="31d5f-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="31d5f-227">Poiché l'autorizzazione della risorsa viene convalidata `[Authorize]` , l'attributo non è sufficiente.</span><span class="sxs-lookup"><span data-stu-id="31d5f-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="31d5f-228">L'app non ha accesso alla risorsa quando vengono valutati gli attributi.</span><span class="sxs-lookup"><span data-stu-id="31d5f-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="31d5f-229">L'autorizzazione basata sulle risorse deve essere imperativa.</span><span class="sxs-lookup"><span data-stu-id="31d5f-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="31d5f-230">I controlli devono essere eseguiti una volta che l'app può accedere alla risorsa, eseguendo il caricamento nel modello di pagina o caricando l'app all'interno del gestore stesso.</span><span class="sxs-lookup"><span data-stu-id="31d5f-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="31d5f-231">Si accede spesso alla risorsa passando la chiave della risorsa.</span><span class="sxs-lookup"><span data-stu-id="31d5f-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="31d5f-232">Aggiornare DeleteModel</span><span class="sxs-lookup"><span data-stu-id="31d5f-232">Update the DeleteModel</span></span>

<span data-ttu-id="31d5f-233">Aggiornare il modello di pagina Elimina per utilizzare il gestore autorizzazione per verificare che l'utente disponga dell'autorizzazione DELETE per il contatto.</span><span class="sxs-lookup"><span data-stu-id="31d5f-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="31d5f-234">Inserire il servizio di autorizzazione nelle viste</span><span class="sxs-lookup"><span data-stu-id="31d5f-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="31d5f-235">Attualmente, l'interfaccia utente Mostra i collegamenti modifica ed Elimina per i contatti che non possono essere modificati dall'utente.</span><span class="sxs-lookup"><span data-stu-id="31d5f-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="31d5f-236">Inserire il servizio di autorizzazione nel file *pages/_ViewImports. cshtml* in modo che sia disponibile per tutte le visualizzazioni:</span><span class="sxs-lookup"><span data-stu-id="31d5f-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="31d5f-237">Il markup precedente aggiunge diverse `using` istruzioni.</span><span class="sxs-lookup"><span data-stu-id="31d5f-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="31d5f-238">Aggiornare i collegamenti **Edit** ed **Delete** in *pages/Contacts/index. cshtml* in modo che vengano sottoposti a rendering solo per gli utenti con le autorizzazioni appropriate:</span><span class="sxs-lookup"><span data-stu-id="31d5f-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="31d5f-239">Nascondere i collegamenti da utenti che non dispongono dell'autorizzazione per modificare i dati non protegge l'app.</span><span class="sxs-lookup"><span data-stu-id="31d5f-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="31d5f-240">Nascondere i collegamenti rende l'app più intuitiva visualizzando solo i collegamenti validi.</span><span class="sxs-lookup"><span data-stu-id="31d5f-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="31d5f-241">Gli utenti possono hackerare gli URL generati per richiamare le operazioni di modifica ed eliminazione sui dati di cui non sono proprietari.</span><span class="sxs-lookup"><span data-stu-id="31d5f-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="31d5f-242">La pagina o il controller Razor deve applicare i controlli di accesso per proteggere i dati.</span><span class="sxs-lookup"><span data-stu-id="31d5f-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="31d5f-243">Dettagli aggiornamento</span><span class="sxs-lookup"><span data-stu-id="31d5f-243">Update Details</span></span>

<span data-ttu-id="31d5f-244">Aggiornare la visualizzazione dettagli in modo che i responsabili possano approvare o rifiutare i contatti:</span><span class="sxs-lookup"><span data-stu-id="31d5f-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="31d5f-245">Aggiornare il modello della pagina dei dettagli:</span><span class="sxs-lookup"><span data-stu-id="31d5f-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="31d5f-246">Aggiungere o rimuovere un utente in un ruolo</span><span class="sxs-lookup"><span data-stu-id="31d5f-246">Add or remove a user to a role</span></span>

<span data-ttu-id="31d5f-247">Per informazioni su, vedere [questo problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="31d5f-247">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="31d5f-248">Rimozione dei privilegi da un utente.</span><span class="sxs-lookup"><span data-stu-id="31d5f-248">Removing privileges from a user.</span></span> <span data-ttu-id="31d5f-249">Ad esempio, disattivare un utente in un'app di chat.</span><span class="sxs-lookup"><span data-stu-id="31d5f-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="31d5f-250">Aggiunta di privilegi a un utente.</span><span class="sxs-lookup"><span data-stu-id="31d5f-250">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="31d5f-251">Differenze tra la sfida e la proibizione</span><span class="sxs-lookup"><span data-stu-id="31d5f-251">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="31d5f-252">Questa app imposta i criteri predefiniti per [richiedere l'autenticazione degli utenti](#require-authenticated-users).</span><span class="sxs-lookup"><span data-stu-id="31d5f-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span></span> <span data-ttu-id="31d5f-253">Il codice seguente consente agli utenti anonimi.</span><span class="sxs-lookup"><span data-stu-id="31d5f-253">The following code allows anonymous users.</span></span> <span data-ttu-id="31d5f-254">Gli utenti anonimi possono mostrare le differenze tra la richiesta di confronto e la proibizione.</span><span class="sxs-lookup"><span data-stu-id="31d5f-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="31d5f-255">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="31d5f-255">In the preceding code:</span></span>

* <span data-ttu-id="31d5f-256">Quando l'utente **non** è autenticato, viene `ChallengeResult` restituito un oggetto.</span><span class="sxs-lookup"><span data-stu-id="31d5f-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="31d5f-257">Quando viene `ChallengeResult` restituito un oggetto, l'utente viene reindirizzato alla pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="31d5f-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="31d5f-258">Quando l'utente viene autenticato, ma non autorizzato, viene `ForbidResult` restituito un oggetto.</span><span class="sxs-lookup"><span data-stu-id="31d5f-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="31d5f-259">Quando viene `ForbidResult` restituito un oggetto, l'utente viene reindirizzato alla pagina di accesso negato.</span><span class="sxs-lookup"><span data-stu-id="31d5f-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="31d5f-260">Testare l'app completata</span><span class="sxs-lookup"><span data-stu-id="31d5f-260">Test the completed app</span></span>

<span data-ttu-id="31d5f-261">Se non è già stata impostata una password per gli account utente di cui è stato eseguito il seeding, usare lo [strumento Gestione segreta](xref:security/app-secrets#secret-manager) per impostare una password:</span><span class="sxs-lookup"><span data-stu-id="31d5f-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="31d5f-262">Scegliere una password complessa: usare otto o più caratteri e almeno un carattere maiuscolo, un numero e un simbolo.</span><span class="sxs-lookup"><span data-stu-id="31d5f-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="31d5f-263">Ad esempio, `Passw0rd!` soddisfa i requisiti per le password complesse.</span><span class="sxs-lookup"><span data-stu-id="31d5f-263">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="31d5f-264">Eseguire il comando seguente dalla cartella del progetto, dove `<PW>` è la password:</span><span class="sxs-lookup"><span data-stu-id="31d5f-264">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="31d5f-265">Se l'app dispone di contatti:</span><span class="sxs-lookup"><span data-stu-id="31d5f-265">If the app has contacts:</span></span>

* <span data-ttu-id="31d5f-266">Eliminare tutti i record nella `Contact` tabella.</span><span class="sxs-lookup"><span data-stu-id="31d5f-266">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="31d5f-267">Riavviare l'app per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="31d5f-267">Restart the app to seed the database.</span></span>

<span data-ttu-id="31d5f-268">Un modo semplice per testare l'app completata consiste nell'avviare tre diversi browser (o sessioni in incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="31d5f-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="31d5f-269">In un browser registrare un nuovo utente (ad esempio, `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="31d5f-269">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="31d5f-270">Accedere a ogni browser con un altro utente.</span><span class="sxs-lookup"><span data-stu-id="31d5f-270">Sign in to each browser with a different user.</span></span> <span data-ttu-id="31d5f-271">Verificare le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="31d5f-271">Verify the following operations:</span></span>

* <span data-ttu-id="31d5f-272">Gli utenti registrati possono visualizzare tutti i dati di contatto approvati.</span><span class="sxs-lookup"><span data-stu-id="31d5f-272">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="31d5f-273">Gli utenti registrati possono modificare/eliminare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="31d5f-273">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="31d5f-274">I responsabili possono approvare/rifiutare i dati di contatto.</span><span class="sxs-lookup"><span data-stu-id="31d5f-274">Managers can approve/reject contact data.</span></span> <span data-ttu-id="31d5f-275">La `Details` visualizzazione Mostra i pulsanti **approva** e **rifiuta** .</span><span class="sxs-lookup"><span data-stu-id="31d5f-275">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="31d5f-276">Gli amministratori possono approvare/rifiutare e modificare/eliminare tutti i dati.</span><span class="sxs-lookup"><span data-stu-id="31d5f-276">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="31d5f-277">Utente</span><span class="sxs-lookup"><span data-stu-id="31d5f-277">User</span></span>                | <span data-ttu-id="31d5f-278">Seeding dall'app</span><span class="sxs-lookup"><span data-stu-id="31d5f-278">Seeded by the app</span></span> | <span data-ttu-id="31d5f-279">Opzioni</span><span class="sxs-lookup"><span data-stu-id="31d5f-279">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="31d5f-280">No</span><span class="sxs-lookup"><span data-stu-id="31d5f-280">No</span></span>                | <span data-ttu-id="31d5f-281">Modificare/eliminare i dati personali.</span><span class="sxs-lookup"><span data-stu-id="31d5f-281">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="31d5f-282">Sì</span><span class="sxs-lookup"><span data-stu-id="31d5f-282">Yes</span></span>               | <span data-ttu-id="31d5f-283">Approva/rifiuta e modifica/elimina i dati personali.</span><span class="sxs-lookup"><span data-stu-id="31d5f-283">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="31d5f-284">Sì</span><span class="sxs-lookup"><span data-stu-id="31d5f-284">Yes</span></span>               | <span data-ttu-id="31d5f-285">Approva/rifiuta e modifica/elimina tutti i dati.</span><span class="sxs-lookup"><span data-stu-id="31d5f-285">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="31d5f-286">Creare un contatto nel browser dell'amministratore.</span><span class="sxs-lookup"><span data-stu-id="31d5f-286">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="31d5f-287">Copiare l'URL da eliminare e modificare dal contatto amministratore.</span><span class="sxs-lookup"><span data-stu-id="31d5f-287">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="31d5f-288">Incollare questi collegamenti nel browser dell'utente test per verificare che l'utente test non possa eseguire queste operazioni.</span><span class="sxs-lookup"><span data-stu-id="31d5f-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="31d5f-289">Creare l'app Starter</span><span class="sxs-lookup"><span data-stu-id="31d5f-289">Create the starter app</span></span>

* <span data-ttu-id="31d5f-290">Creare un'app Razor Pages denominata "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="31d5f-290">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="31d5f-291">Creare l'app con **singoli account utente**.</span><span class="sxs-lookup"><span data-stu-id="31d5f-291">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="31d5f-292">Denominarlo "ContactManager" in modo che lo spazio dei nomi corrisponda allo spazio dei nomi usato nell'esempio.</span><span class="sxs-lookup"><span data-stu-id="31d5f-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="31d5f-293">`-uld`Specifica il database locale anziché SQLite</span><span class="sxs-lookup"><span data-stu-id="31d5f-293">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="31d5f-294">Aggiungi *modelli/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="31d5f-294">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="31d5f-295">Impalcatura `Contact` del modello.</span><span class="sxs-lookup"><span data-stu-id="31d5f-295">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="31d5f-296">Creare la migrazione iniziale e aggiornare il database:</span><span class="sxs-lookup"><span data-stu-id="31d5f-296">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="31d5f-297">Se si verifica un bug con il `dotnet aspnet-codegenerator razorpage` comando, vedere [questo problema di GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="31d5f-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="31d5f-298">Aggiornare l'ancoraggio **ContactManager** nel file *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="31d5f-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="31d5f-299">Testare l'app creando, modificando ed eliminando un contatto</span><span class="sxs-lookup"><span data-stu-id="31d5f-299">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="31d5f-300">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="31d5f-300">Seed the database</span></span>

<span data-ttu-id="31d5f-301">Aggiungere la classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) alla cartella *Data* :</span><span class="sxs-lookup"><span data-stu-id="31d5f-301">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="31d5f-302">Chiama `SeedData.Initialize` da `Main`:</span><span class="sxs-lookup"><span data-stu-id="31d5f-302">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="31d5f-303">Verificare che l'app abbia sottoposta a seeding il database.</span><span class="sxs-lookup"><span data-stu-id="31d5f-303">Test that the app seeded the database.</span></span> <span data-ttu-id="31d5f-304">Se sono presenti righe nel database Contact, il metodo seed non viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="31d5f-304">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="31d5f-305">Questa esercitazione illustra come creare un'app Web di ASP.NET Core con i dati utente protetti dall'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="31d5f-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="31d5f-306">Viene visualizzato un elenco di contatti creati dagli utenti autenticati (registrati).</span><span class="sxs-lookup"><span data-stu-id="31d5f-306">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="31d5f-307">Sono disponibili tre gruppi di sicurezza:</span><span class="sxs-lookup"><span data-stu-id="31d5f-307">There are three security groups:</span></span>

* <span data-ttu-id="31d5f-308">**Gli utenti registrati** possono visualizzare tutti i dati approvati e possono modificare/eliminare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="31d5f-308">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="31d5f-309">I **responsabili** possono approvare o rifiutare i dati di contatto.</span><span class="sxs-lookup"><span data-stu-id="31d5f-309">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="31d5f-310">Solo i contatti approvati sono visibili agli utenti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-310">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="31d5f-311">Gli **amministratori** possono approvare/rifiutare e modificare/eliminare i dati.</span><span class="sxs-lookup"><span data-stu-id="31d5f-311">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="31d5f-312">Nell'immagine seguente, l'utente Rick (`rick@example.com`) ha eseguito l'accesso.</span><span class="sxs-lookup"><span data-stu-id="31d5f-312">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="31d5f-313">Rick può visualizzare solo i contatti approvati e **modificare**/**Delete**/**Crea nuovi** collegamenti per i contatti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="31d5f-314">Solo l'ultimo record, creato da Rick, Visualizza i collegamenti **modifica** ed **Elimina** .</span><span class="sxs-lookup"><span data-stu-id="31d5f-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="31d5f-315">Gli altri utenti non vedranno l'ultimo record fino a quando un responsabile o un amministratore non modifica lo stato in "approvato".</span><span class="sxs-lookup"><span data-stu-id="31d5f-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot che illustra l'accesso a Rick](secure-data/_static/rick.png)

<span data-ttu-id="31d5f-317">Nell'immagine seguente, `manager@contoso.com` è stato eseguito l'accesso e nel ruolo del Manager:</span><span class="sxs-lookup"><span data-stu-id="31d5f-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot che manager@contoso.com Mostra l'accesso](secure-data/_static/manager1.png)

<span data-ttu-id="31d5f-319">Nella figura seguente viene illustrata la visualizzazione dettagli Manager di un contatto:</span><span class="sxs-lookup"><span data-stu-id="31d5f-319">The following image shows the managers details view of a contact:</span></span>

![Visualizzazione del contatto da un responsabile](secure-data/_static/manager.png)

<span data-ttu-id="31d5f-321">I pulsanti **approva** e **rifiuta** vengono visualizzati solo per Manager e amministratori.</span><span class="sxs-lookup"><span data-stu-id="31d5f-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="31d5f-322">Nell'immagine seguente, `admin@contoso.com` è stato eseguito l'accesso e il ruolo dell'amministratore:</span><span class="sxs-lookup"><span data-stu-id="31d5f-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot che admin@contoso.com Mostra l'accesso](secure-data/_static/admin.png)

<span data-ttu-id="31d5f-324">L'amministratore dispone di tutti i privilegi.</span><span class="sxs-lookup"><span data-stu-id="31d5f-324">The administrator has all privileges.</span></span> <span data-ttu-id="31d5f-325">Può leggere, modificare ed eliminare qualsiasi contatto e modificare lo stato dei contatti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-325">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="31d5f-326">L'app è stata creata mediante l' [impalcatura](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) del modello seguente `Contact` :</span><span class="sxs-lookup"><span data-stu-id="31d5f-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="31d5f-327">L'esempio contiene i gestori di autorizzazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="31d5f-327">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="31d5f-328">`ContactIsOwnerAuthorizationHandler`: Assicura che un utente possa modificare solo i propri dati.</span><span class="sxs-lookup"><span data-stu-id="31d5f-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="31d5f-329">`ContactManagerAuthorizationHandler`: Consente ai responsabili di approvare o rifiutare i contatti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="31d5f-330">`ContactAdministratorsAuthorizationHandler`: Consente agli amministratori di approvare o rifiutare contatti e di modificare/eliminare contatti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="31d5f-331">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="31d5f-331">Prerequisites</span></span>

<span data-ttu-id="31d5f-332">Questa esercitazione è avanzata.</span><span class="sxs-lookup"><span data-stu-id="31d5f-332">This tutorial is advanced.</span></span> <span data-ttu-id="31d5f-333">È necessario avere familiarità con:</span><span class="sxs-lookup"><span data-stu-id="31d5f-333">You should be familiar with:</span></span>

* [<span data-ttu-id="31d5f-334">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="31d5f-334">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="31d5f-335">autenticazione</span><span class="sxs-lookup"><span data-stu-id="31d5f-335">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="31d5f-336">Conferma account e recupero password</span><span class="sxs-lookup"><span data-stu-id="31d5f-336">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="31d5f-337">Autorizzazione</span><span class="sxs-lookup"><span data-stu-id="31d5f-337">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="31d5f-338">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="31d5f-338">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="31d5f-339">App iniziale e completata</span><span class="sxs-lookup"><span data-stu-id="31d5f-339">The starter and completed app</span></span>

<span data-ttu-id="31d5f-340">[Scaricare](xref:index#how-to-download-a-sample) l'app [completata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="31d5f-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="31d5f-341">[Testare](#test-the-completed-app) l'app completata in modo da acquisire familiarità con le funzionalità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="31d5f-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="31d5f-342">App iniziale</span><span class="sxs-lookup"><span data-stu-id="31d5f-342">The starter app</span></span>

<span data-ttu-id="31d5f-343">[Scaricare](xref:index#how-to-download-a-sample) l'app [Starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="31d5f-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="31d5f-344">Eseguire l'app, toccare il collegamento **ContactManager** e verificare che sia possibile creare, modificare ed eliminare un contatto.</span><span class="sxs-lookup"><span data-stu-id="31d5f-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="31d5f-345">Proteggere i dati utente</span><span class="sxs-lookup"><span data-stu-id="31d5f-345">Secure user data</span></span>

<span data-ttu-id="31d5f-346">Le sezioni seguenti includono tutti i passaggi principali per creare l'app Secure User Data.</span><span class="sxs-lookup"><span data-stu-id="31d5f-346">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="31d5f-347">Può risultare utile fare riferimento al progetto completato.</span><span class="sxs-lookup"><span data-stu-id="31d5f-347">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="31d5f-348">Associare i dati di contatto all'utente</span><span class="sxs-lookup"><span data-stu-id="31d5f-348">Tie the contact data to the user</span></span>

<span data-ttu-id="31d5f-349">Usare l'ID utente dell' [identità](xref:security/authentication/identity) ASP.NET per assicurarsi che gli utenti possano modificare i dati, ma non i dati di altri utenti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="31d5f-350">Aggiungere `OwnerID` e `ContactStatus` al `Contact` modello:</span><span class="sxs-lookup"><span data-stu-id="31d5f-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="31d5f-351">`OwnerID`ID dell'utente della `AspNetUser` tabella nel database di [identità](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="31d5f-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="31d5f-352">Il `Status` campo determina se un contatto è visualizzabile dagli utenti generali.</span><span class="sxs-lookup"><span data-stu-id="31d5f-352">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="31d5f-353">Creare una nuova migrazione e aggiornare il database:</span><span class="sxs-lookup"><span data-stu-id="31d5f-353">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="31d5f-354">Aggiungere servizi ruolo all'identità</span><span class="sxs-lookup"><span data-stu-id="31d5f-354">Add Role services to Identity</span></span>

<span data-ttu-id="31d5f-355">Aggiungere [Aggiungi ruoli](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) per aggiungere servizi ruolo:</span><span class="sxs-lookup"><span data-stu-id="31d5f-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="31d5f-356">Richiedi utenti autenticati</span><span class="sxs-lookup"><span data-stu-id="31d5f-356">Require authenticated users</span></span>

<span data-ttu-id="31d5f-357">Impostare i criteri di autenticazione predefiniti per richiedere l'autenticazione degli utenti:</span><span class="sxs-lookup"><span data-stu-id="31d5f-357">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="31d5f-358">È possibile rifiutare esplicitamente l'autenticazione a livello di pagina Razor, controller o metodo di azione con `[AllowAnonymous]` l'attributo.</span><span class="sxs-lookup"><span data-stu-id="31d5f-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="31d5f-359">L'impostazione dei criteri di autenticazione predefiniti in modo da richiedere l'autenticazione degli utenti consente di proteggere Razor Pages e controller appena aggiunti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="31d5f-360">La necessità di eseguire l'autenticazione per impostazione predefinita è più sicura rispetto all'uso di nuovi controller e `[Authorize]` Razor Pages per includere l'attributo.</span><span class="sxs-lookup"><span data-stu-id="31d5f-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="31d5f-361">Aggiungere [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) alle pagine di indice, informazioni e contatti in modo che gli utenti anonimi possano ottenere informazioni sul sito prima della registrazione.</span><span class="sxs-lookup"><span data-stu-id="31d5f-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="31d5f-362">Configurare l'account di test</span><span class="sxs-lookup"><span data-stu-id="31d5f-362">Configure the test account</span></span>

<span data-ttu-id="31d5f-363">La `SeedData` classe crea due account: Administrator e Manager.</span><span class="sxs-lookup"><span data-stu-id="31d5f-363">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="31d5f-364">Utilizzare lo [strumento Gestione segreta](xref:security/app-secrets) per impostare una password per questi account.</span><span class="sxs-lookup"><span data-stu-id="31d5f-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="31d5f-365">Impostare la password dalla directory del progetto (la directory contenente *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="31d5f-365">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="31d5f-366">Se non si specifica una password complessa, viene generata un'eccezione quando `SeedData.Initialize` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="31d5f-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="31d5f-367">Aggiornare `Main` per usare la password di test:</span><span class="sxs-lookup"><span data-stu-id="31d5f-367">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="31d5f-368">Creare gli account di test e aggiornare i contatti</span><span class="sxs-lookup"><span data-stu-id="31d5f-368">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="31d5f-369">Aggiornare il `Initialize` metodo nella `SeedData` classe per creare gli account di test:</span><span class="sxs-lookup"><span data-stu-id="31d5f-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="31d5f-370">Aggiungere l'ID utente amministratore e `ContactStatus` ai contatti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-370">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="31d5f-371">Creare uno dei contatti "inviato" e uno "rifiutato".</span><span class="sxs-lookup"><span data-stu-id="31d5f-371">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="31d5f-372">Aggiungere l'ID utente e lo stato a tutti i contatti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-372">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="31d5f-373">Viene visualizzato un solo contatto:</span><span class="sxs-lookup"><span data-stu-id="31d5f-373">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="31d5f-374">Creazione di gestori di autorizzazioni proprietario, Manager e amministratore</span><span class="sxs-lookup"><span data-stu-id="31d5f-374">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="31d5f-375">Creare una cartella di *autorizzazione* e crearvi una `ContactIsOwnerAuthorizationHandler` classe.</span><span class="sxs-lookup"><span data-stu-id="31d5f-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="31d5f-376">`ContactIsOwnerAuthorizationHandler` Verifica che l'utente che agisce su una risorsa appartenga alla risorsa.</span><span class="sxs-lookup"><span data-stu-id="31d5f-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="31d5f-377">Contesto `ContactIsOwnerAuthorizationHandler` delle chiamate [. Ha esito positivo](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se l'utente autenticato corrente è il proprietario del contatto.</span><span class="sxs-lookup"><span data-stu-id="31d5f-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="31d5f-378">Gestori autorizzazioni in genere:</span><span class="sxs-lookup"><span data-stu-id="31d5f-378">Authorization handlers generally:</span></span>

* <span data-ttu-id="31d5f-379">Restituisce `context.Succeed` quando vengono soddisfatti i requisiti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-379">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="31d5f-380">Restituisce `Task.CompletedTask` quando i requisiti non vengono soddisfatti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-380">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="31d5f-381">`Task.CompletedTask`non ha esito positivo&mdash;o negativo consente l'esecuzione di altri gestori di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="31d5f-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="31d5f-382">Se è necessario eseguire in modo esplicito l'errore, restituire [context. Esito negativo](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="31d5f-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="31d5f-383">L'app consente ai proprietari dei contatti di modificare/eliminare/creare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="31d5f-383">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="31d5f-384">`ContactIsOwnerAuthorizationHandler`non è necessario controllare l'operazione passata nel parametro requirement.</span><span class="sxs-lookup"><span data-stu-id="31d5f-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="31d5f-385">Creazione di un gestore autorizzazioni di gestione</span><span class="sxs-lookup"><span data-stu-id="31d5f-385">Create a manager authorization handler</span></span>

<span data-ttu-id="31d5f-386">Creare una `ContactManagerAuthorizationHandler` classe nella cartella *authorization* .</span><span class="sxs-lookup"><span data-stu-id="31d5f-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="31d5f-387">`ContactManagerAuthorizationHandler` Verifica che l'utente che agisce sulla risorsa sia un responsabile.</span><span class="sxs-lookup"><span data-stu-id="31d5f-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="31d5f-388">Solo i responsabili possono approvare o rifiutare le modifiche al contenuto (nuove o modificate).</span><span class="sxs-lookup"><span data-stu-id="31d5f-388">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="31d5f-389">Creazione di un gestore autorizzazioni Amministratore</span><span class="sxs-lookup"><span data-stu-id="31d5f-389">Create an administrator authorization handler</span></span>

<span data-ttu-id="31d5f-390">Creare una `ContactAdministratorsAuthorizationHandler` classe nella cartella *authorization* .</span><span class="sxs-lookup"><span data-stu-id="31d5f-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="31d5f-391">Il `ContactAdministratorsAuthorizationHandler` verifica che l'utente che agisce sulla risorsa sia un amministratore.</span><span class="sxs-lookup"><span data-stu-id="31d5f-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="31d5f-392">L'amministratore può eseguire tutte le operazioni.</span><span class="sxs-lookup"><span data-stu-id="31d5f-392">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="31d5f-393">Registrare i gestori di autorizzazioni</span><span class="sxs-lookup"><span data-stu-id="31d5f-393">Register the authorization handlers</span></span>

<span data-ttu-id="31d5f-394">I servizi che usano Entity Framework Core devono essere registrati per l' [inserimento di dipendenze](xref:fundamentals/dependency-injection) usando [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="31d5f-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="31d5f-395">`ContactIsOwnerAuthorizationHandler` USA ASP.NET Core [identità](xref:security/authentication/identity), che si basa su Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="31d5f-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="31d5f-396">Registrare i gestori con la raccolta di servizi in modo che siano disponibili per `ContactsController` tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="31d5f-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="31d5f-397">Aggiungere il codice seguente alla fine di `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="31d5f-397">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="31d5f-398">`ContactAdministratorsAuthorizationHandler`e `ContactManagerAuthorizationHandler` vengono aggiunti come singleton.</span><span class="sxs-lookup"><span data-stu-id="31d5f-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="31d5f-399">Si tratta di singleton perché non usano EF e tutte le informazioni necessarie si trovano nel `Context` parametro del `HandleRequirementAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="31d5f-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="31d5f-400">Autorizzazione supporto</span><span class="sxs-lookup"><span data-stu-id="31d5f-400">Support authorization</span></span>

<span data-ttu-id="31d5f-401">In questa sezione si aggiorna il Razor Pages e si aggiunge una classe dei requisiti delle operazioni.</span><span class="sxs-lookup"><span data-stu-id="31d5f-401">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="31d5f-402">Esaminare la classe dei requisiti delle operazioni di contatto</span><span class="sxs-lookup"><span data-stu-id="31d5f-402">Review the contact operations requirements class</span></span>

<span data-ttu-id="31d5f-403">Esaminare la `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="31d5f-403">Review the `ContactOperations` class.</span></span> <span data-ttu-id="31d5f-404">Questa classe contiene i requisiti supportati dall'app:</span><span class="sxs-lookup"><span data-stu-id="31d5f-404">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="31d5f-405">Creare una classe di base per i contatti Razor Pages</span><span class="sxs-lookup"><span data-stu-id="31d5f-405">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="31d5f-406">Creare una classe di base che contiene i servizi utilizzati nell'Razor Pages contatti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-406">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="31d5f-407">La classe base inserisce il codice di inizializzazione in un'unica posizione:</span><span class="sxs-lookup"><span data-stu-id="31d5f-407">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="31d5f-408">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="31d5f-408">The preceding code:</span></span>

* <span data-ttu-id="31d5f-409">Aggiunge il `IAuthorizationService` servizio per accedere ai gestori di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="31d5f-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="31d5f-410">Aggiunge il servizio `UserManager` di identità.</span><span class="sxs-lookup"><span data-stu-id="31d5f-410">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="31d5f-411">Aggiungere l'oggetto `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="31d5f-411">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="31d5f-412">Aggiornare CreateModel</span><span class="sxs-lookup"><span data-stu-id="31d5f-412">Update the CreateModel</span></span>

<span data-ttu-id="31d5f-413">Aggiornare il costruttore del modello di pagina create per `DI_BasePageModel` usare la classe di base:</span><span class="sxs-lookup"><span data-stu-id="31d5f-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="31d5f-414">Aggiornare il `CreateModel.OnPostAsync` metodo a:</span><span class="sxs-lookup"><span data-stu-id="31d5f-414">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="31d5f-415">Aggiungere l'ID utente al `Contact` modello.</span><span class="sxs-lookup"><span data-stu-id="31d5f-415">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="31d5f-416">Chiamare il gestore autorizzazioni per verificare che l'utente disponga dell'autorizzazione per la creazione di contatti.</span><span class="sxs-lookup"><span data-stu-id="31d5f-416">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="31d5f-417">Aggiornare IndexModel</span><span class="sxs-lookup"><span data-stu-id="31d5f-417">Update the IndexModel</span></span>

<span data-ttu-id="31d5f-418">Aggiornare il `OnGetAsync` metodo in modo da visualizzare solo i contatti approvati per gli utenti generali:</span><span class="sxs-lookup"><span data-stu-id="31d5f-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="31d5f-419">Aggiornare EditModel</span><span class="sxs-lookup"><span data-stu-id="31d5f-419">Update the EditModel</span></span>

<span data-ttu-id="31d5f-420">Aggiungere un gestore autorizzazioni per verificare che l'utente sia proprietario del contatto.</span><span class="sxs-lookup"><span data-stu-id="31d5f-420">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="31d5f-421">Poiché l'autorizzazione della risorsa viene convalidata `[Authorize]` , l'attributo non è sufficiente.</span><span class="sxs-lookup"><span data-stu-id="31d5f-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="31d5f-422">L'app non ha accesso alla risorsa quando vengono valutati gli attributi.</span><span class="sxs-lookup"><span data-stu-id="31d5f-422">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="31d5f-423">L'autorizzazione basata sulle risorse deve essere imperativa.</span><span class="sxs-lookup"><span data-stu-id="31d5f-423">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="31d5f-424">I controlli devono essere eseguiti una volta che l'app può accedere alla risorsa, eseguendo il caricamento nel modello di pagina o caricando l'app all'interno del gestore stesso.</span><span class="sxs-lookup"><span data-stu-id="31d5f-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="31d5f-425">Si accede spesso alla risorsa passando la chiave della risorsa.</span><span class="sxs-lookup"><span data-stu-id="31d5f-425">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="31d5f-426">Aggiornare DeleteModel</span><span class="sxs-lookup"><span data-stu-id="31d5f-426">Update the DeleteModel</span></span>

<span data-ttu-id="31d5f-427">Aggiornare il modello di pagina Elimina per utilizzare il gestore autorizzazione per verificare che l'utente disponga dell'autorizzazione DELETE per il contatto.</span><span class="sxs-lookup"><span data-stu-id="31d5f-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="31d5f-428">Inserire il servizio di autorizzazione nelle viste</span><span class="sxs-lookup"><span data-stu-id="31d5f-428">Inject the authorization service into the views</span></span>

<span data-ttu-id="31d5f-429">Attualmente, l'interfaccia utente Mostra i collegamenti modifica ed Elimina per i contatti che non possono essere modificati dall'utente.</span><span class="sxs-lookup"><span data-stu-id="31d5f-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="31d5f-430">Inserire il servizio di autorizzazione nel file *views/_ViewImports. cshtml* in modo che sia disponibile per tutte le visualizzazioni:</span><span class="sxs-lookup"><span data-stu-id="31d5f-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="31d5f-431">Il markup precedente aggiunge diverse `using` istruzioni.</span><span class="sxs-lookup"><span data-stu-id="31d5f-431">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="31d5f-432">Aggiornare i collegamenti **Edit** ed **Delete** in *pages/Contacts/index. cshtml* in modo che vengano sottoposti a rendering solo per gli utenti con le autorizzazioni appropriate:</span><span class="sxs-lookup"><span data-stu-id="31d5f-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="31d5f-433">Nascondere i collegamenti da utenti che non dispongono dell'autorizzazione per modificare i dati non protegge l'app.</span><span class="sxs-lookup"><span data-stu-id="31d5f-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="31d5f-434">Nascondere i collegamenti rende l'app più intuitiva visualizzando solo i collegamenti validi.</span><span class="sxs-lookup"><span data-stu-id="31d5f-434">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="31d5f-435">Gli utenti possono hackerare gli URL generati per richiamare le operazioni di modifica ed eliminazione sui dati di cui non sono proprietari.</span><span class="sxs-lookup"><span data-stu-id="31d5f-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="31d5f-436">La pagina o il controller Razor deve applicare i controlli di accesso per proteggere i dati.</span><span class="sxs-lookup"><span data-stu-id="31d5f-436">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="31d5f-437">Dettagli aggiornamento</span><span class="sxs-lookup"><span data-stu-id="31d5f-437">Update Details</span></span>

<span data-ttu-id="31d5f-438">Aggiornare la visualizzazione dettagli in modo che i responsabili possano approvare o rifiutare i contatti:</span><span class="sxs-lookup"><span data-stu-id="31d5f-438">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="31d5f-439">Aggiornare il modello della pagina dei dettagli:</span><span class="sxs-lookup"><span data-stu-id="31d5f-439">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="31d5f-440">Aggiungere o rimuovere un utente in un ruolo</span><span class="sxs-lookup"><span data-stu-id="31d5f-440">Add or remove a user to a role</span></span>

<span data-ttu-id="31d5f-441">Per informazioni su, vedere [questo problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="31d5f-441">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="31d5f-442">Rimozione dei privilegi da un utente.</span><span class="sxs-lookup"><span data-stu-id="31d5f-442">Removing privileges from a user.</span></span> <span data-ttu-id="31d5f-443">Ad esempio, disattivare un utente in un'app di chat.</span><span class="sxs-lookup"><span data-stu-id="31d5f-443">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="31d5f-444">Aggiunta di privilegi a un utente.</span><span class="sxs-lookup"><span data-stu-id="31d5f-444">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="31d5f-445">Testare l'app completata</span><span class="sxs-lookup"><span data-stu-id="31d5f-445">Test the completed app</span></span>

<span data-ttu-id="31d5f-446">Se non è già stata impostata una password per gli account utente di cui è stato eseguito il seeding, usare lo [strumento Gestione segreta](xref:security/app-secrets#secret-manager) per impostare una password:</span><span class="sxs-lookup"><span data-stu-id="31d5f-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="31d5f-447">Scegliere una password complessa: usare otto o più caratteri e almeno un carattere maiuscolo, un numero e un simbolo.</span><span class="sxs-lookup"><span data-stu-id="31d5f-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="31d5f-448">Ad esempio, `Passw0rd!` soddisfa i requisiti per le password complesse.</span><span class="sxs-lookup"><span data-stu-id="31d5f-448">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="31d5f-449">Eseguire il comando seguente dalla cartella del progetto, dove `<PW>` è la password:</span><span class="sxs-lookup"><span data-stu-id="31d5f-449">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="31d5f-450">Eliminare e aggiornare il database</span><span class="sxs-lookup"><span data-stu-id="31d5f-450">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="31d5f-451">Riavviare l'app per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="31d5f-451">Restart the app to seed the database.</span></span>

<span data-ttu-id="31d5f-452">Un modo semplice per testare l'app completata consiste nell'avviare tre diversi browser (o sessioni in incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="31d5f-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="31d5f-453">In un browser registrare un nuovo utente (ad esempio, `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="31d5f-453">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="31d5f-454">Accedere a ogni browser con un altro utente.</span><span class="sxs-lookup"><span data-stu-id="31d5f-454">Sign in to each browser with a different user.</span></span> <span data-ttu-id="31d5f-455">Verificare le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="31d5f-455">Verify the following operations:</span></span>

* <span data-ttu-id="31d5f-456">Gli utenti registrati possono visualizzare tutti i dati di contatto approvati.</span><span class="sxs-lookup"><span data-stu-id="31d5f-456">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="31d5f-457">Gli utenti registrati possono modificare/eliminare i propri dati.</span><span class="sxs-lookup"><span data-stu-id="31d5f-457">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="31d5f-458">I responsabili possono approvare/rifiutare i dati di contatto.</span><span class="sxs-lookup"><span data-stu-id="31d5f-458">Managers can approve/reject contact data.</span></span> <span data-ttu-id="31d5f-459">La `Details` visualizzazione Mostra i pulsanti **approva** e **rifiuta** .</span><span class="sxs-lookup"><span data-stu-id="31d5f-459">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="31d5f-460">Gli amministratori possono approvare/rifiutare e modificare/eliminare tutti i dati.</span><span class="sxs-lookup"><span data-stu-id="31d5f-460">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="31d5f-461">Utente</span><span class="sxs-lookup"><span data-stu-id="31d5f-461">User</span></span>                | <span data-ttu-id="31d5f-462">Seeding dall'app</span><span class="sxs-lookup"><span data-stu-id="31d5f-462">Seeded by the app</span></span> | <span data-ttu-id="31d5f-463">Opzioni</span><span class="sxs-lookup"><span data-stu-id="31d5f-463">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="31d5f-464">No</span><span class="sxs-lookup"><span data-stu-id="31d5f-464">No</span></span>                | <span data-ttu-id="31d5f-465">Modificare/eliminare i dati personali.</span><span class="sxs-lookup"><span data-stu-id="31d5f-465">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="31d5f-466">Sì</span><span class="sxs-lookup"><span data-stu-id="31d5f-466">Yes</span></span>               | <span data-ttu-id="31d5f-467">Approva/rifiuta e modifica/elimina i dati personali.</span><span class="sxs-lookup"><span data-stu-id="31d5f-467">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="31d5f-468">Sì</span><span class="sxs-lookup"><span data-stu-id="31d5f-468">Yes</span></span>               | <span data-ttu-id="31d5f-469">Approva/rifiuta e modifica/elimina tutti i dati.</span><span class="sxs-lookup"><span data-stu-id="31d5f-469">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="31d5f-470">Creare un contatto nel browser dell'amministratore.</span><span class="sxs-lookup"><span data-stu-id="31d5f-470">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="31d5f-471">Copiare l'URL da eliminare e modificare dal contatto amministratore.</span><span class="sxs-lookup"><span data-stu-id="31d5f-471">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="31d5f-472">Incollare questi collegamenti nel browser dell'utente test per verificare che l'utente test non possa eseguire queste operazioni.</span><span class="sxs-lookup"><span data-stu-id="31d5f-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="31d5f-473">Creare l'app Starter</span><span class="sxs-lookup"><span data-stu-id="31d5f-473">Create the starter app</span></span>

* <span data-ttu-id="31d5f-474">Creare un' Razor app per le pagine denominata "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="31d5f-474">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="31d5f-475">Creare l'app con **singoli account utente**.</span><span class="sxs-lookup"><span data-stu-id="31d5f-475">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="31d5f-476">Denominarlo "ContactManager" in modo che lo spazio dei nomi corrisponda allo spazio dei nomi usato nell'esempio.</span><span class="sxs-lookup"><span data-stu-id="31d5f-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="31d5f-477">`-uld`Specifica il database locale anziché SQLite</span><span class="sxs-lookup"><span data-stu-id="31d5f-477">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="31d5f-478">Aggiungi *modelli/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="31d5f-478">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="31d5f-479">Impalcatura `Contact` del modello.</span><span class="sxs-lookup"><span data-stu-id="31d5f-479">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="31d5f-480">Creare la migrazione iniziale e aggiornare il database:</span><span class="sxs-lookup"><span data-stu-id="31d5f-480">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="31d5f-481">Aggiornare l'ancoraggio **ContactManager** nel file *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="31d5f-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="31d5f-482">Testare l'app creando, modificando ed eliminando un contatto</span><span class="sxs-lookup"><span data-stu-id="31d5f-482">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="31d5f-483">Specificare il valore di inizializzazione del database</span><span class="sxs-lookup"><span data-stu-id="31d5f-483">Seed the database</span></span>

<span data-ttu-id="31d5f-484">Aggiungere la classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) alla cartella *Data* .</span><span class="sxs-lookup"><span data-stu-id="31d5f-484">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="31d5f-485">Chiama `SeedData.Initialize` da `Main`:</span><span class="sxs-lookup"><span data-stu-id="31d5f-485">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="31d5f-486">Verificare che l'app abbia sottoposta a seeding il database.</span><span class="sxs-lookup"><span data-stu-id="31d5f-486">Test that the app seeded the database.</span></span> <span data-ttu-id="31d5f-487">Se sono presenti righe nel database Contact, il metodo seed non viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="31d5f-487">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="31d5f-488">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="31d5f-488">Additional resources</span></span>

* [<span data-ttu-id="31d5f-489">Creare un'app Web .NET Core e database SQL nel Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="31d5f-489">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="31d5f-490">[ASP.NET Core Lab di autorizzazione](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="31d5f-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="31d5f-491">Questo Lab illustra in modo più dettagliato le funzionalità di sicurezza introdotte in questa esercitazione.</span><span class="sxs-lookup"><span data-stu-id="31d5f-491">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="31d5f-492">Autorizzazione personalizzata basata su criteri</span><span class="sxs-lookup"><span data-stu-id="31d5f-492">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
