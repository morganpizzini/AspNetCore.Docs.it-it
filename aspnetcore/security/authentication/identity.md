---
title: Introduzione a Identity on ASP.NET Core
author: rick-anderson
description: Usare Identity con un'app ASP.NET Core. Informazioni su come impostare i requisiti per le password (RequireDigit, RequiredLength, RequiredUniqueChars e altro).
ms.author: riande
ms.date: 01/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: 31970bd2b52ad83c116067d258aa9dca2d9b3b66
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793575"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="1108b-104">Introduzione a Identity on ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1108b-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1108b-105">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1108b-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1108b-106">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="1108b-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="1108b-107">È un'API che supporta la funzionalità di accesso dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="1108b-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="1108b-108">Gestisce utenti, password, dati di profilo, ruoli, attestazioni, token, conferma tramite posta elettronica e altro ancora.</span><span class="sxs-lookup"><span data-stu-id="1108b-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="1108b-109">Gli utenti possono creare un account con le informazioni di accesso archiviate in Identity o possono usare un provider di accesso esterno.</span><span class="sxs-lookup"><span data-stu-id="1108b-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="1108b-110">I provider di accesso esterni supportati includono [Facebook, Google, account Microsoft e Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="1108b-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="1108b-111">Il [ Identity codice sorgente](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) è disponibile in GitHub.</span><span class="sxs-lookup"><span data-stu-id="1108b-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="1108b-112">[Impalcatura Identity ](xref:security/authentication/scaffold-identity) e visualizzare i file generati per esaminare l'interazione del modello con Identity .</span><span class="sxs-lookup"><span data-stu-id="1108b-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

Identity<span data-ttu-id="1108b-113">viene in genere configurato utilizzando un database di SQL Server per archiviare i nomi utente, le password e i dati di profilo.</span><span class="sxs-lookup"><span data-stu-id="1108b-113"> is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="1108b-114">In alternativa, è possibile usare un altro archivio permanente, ad esempio archiviazione tabelle di Azure.</span><span class="sxs-lookup"><span data-stu-id="1108b-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="1108b-115">In questo argomento si apprenderà come usare Identity per registrare, accedere e disconnettere un utente.</span><span class="sxs-lookup"><span data-stu-id="1108b-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="1108b-116">Nota: i modelli considerano il nome utente e il messaggio di posta elettronica come lo stesso per gli utenti.</span><span class="sxs-lookup"><span data-stu-id="1108b-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="1108b-117">Per istruzioni più dettagliate sulla creazione di app che usano Identity , vedere la sezione passaggi successivi alla fine di questo articolo.</span><span class="sxs-lookup"><span data-stu-id="1108b-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="1108b-118">[Piattaforma di identità Microsoft](/azure/active-directory/develop/) :</span><span class="sxs-lookup"><span data-stu-id="1108b-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="1108b-119">Evoluzione della piattaforma per sviluppatori Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="1108b-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="1108b-120">Non correlato a ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="1108b-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="1108b-121">Consente di [visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([come scaricare)](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="1108b-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="1108b-122">Creare un'app Web con l'autenticazione</span><span class="sxs-lookup"><span data-stu-id="1108b-122">Create a Web app with authentication</span></span>

<span data-ttu-id="1108b-123">Creare un progetto di applicazione Web di ASP.NET Core con singoli account utente.</span><span class="sxs-lookup"><span data-stu-id="1108b-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1108b-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1108b-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1108b-125">Selezionare **file** > **nuovo** > **progetto**.</span><span class="sxs-lookup"><span data-stu-id="1108b-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="1108b-126">Selezionare **Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="1108b-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="1108b-127">Denominare il progetto **app Web 1** in modo che abbia lo stesso spazio dei nomi del download del progetto.</span><span class="sxs-lookup"><span data-stu-id="1108b-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="1108b-128">Fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="1108b-128">Click **OK**.</span></span>
* <span data-ttu-id="1108b-129">Selezionare un' **applicazione Web**ASP.NET Core, quindi selezionare **Modifica autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="1108b-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="1108b-130">Selezionare **singoli account utente** e fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="1108b-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1108b-131">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="1108b-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="1108b-132">Il comando precedente crea un' Razor app Web con SQLite.</span><span class="sxs-lookup"><span data-stu-id="1108b-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="1108b-133">Per creare l'app Web con il database locale, eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="1108b-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="1108b-134">Il progetto generato fornisce [ASP.NET Core Identity ](xref:security/authentication/identity) come [ Razor libreria di classi](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="1108b-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="1108b-135">La Identity Razor libreria di classi espone gli endpoint con l' `Identity` area.</span><span class="sxs-lookup"><span data-stu-id="1108b-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="1108b-136">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="1108b-136">For example:</span></span>

* <span data-ttu-id="1108b-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="1108b-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="1108b-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="1108b-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="1108b-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="1108b-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="1108b-140">Applicare le migrazioni</span><span class="sxs-lookup"><span data-stu-id="1108b-140">Apply migrations</span></span>

<span data-ttu-id="1108b-141">Applicare le migrazioni per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="1108b-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1108b-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1108b-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1108b-143">Eseguire il comando seguente nella console di gestione pacchetti (PMC):</span><span class="sxs-lookup"><span data-stu-id="1108b-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="1108b-144">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="1108b-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1108b-145">Le migrazioni non sono necessarie in questo passaggio quando si usa SQLite.</span><span class="sxs-lookup"><span data-stu-id="1108b-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="1108b-146">Per il database locale, eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="1108b-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="1108b-147">Registro di test e account di accesso</span><span class="sxs-lookup"><span data-stu-id="1108b-147">Test Register and Login</span></span>

<span data-ttu-id="1108b-148">Eseguire l'app e registrare un utente.</span><span class="sxs-lookup"><span data-stu-id="1108b-148">Run the app and register a user.</span></span> <span data-ttu-id="1108b-149">A seconda delle dimensioni dello schermo, potrebbe essere necessario selezionare l'interruttore di spostamento per visualizzare i collegamenti di **Registro** e di **accesso** .</span><span class="sxs-lookup"><span data-stu-id="1108b-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="1108b-150">Configurare i Identity Servizi</span><span class="sxs-lookup"><span data-stu-id="1108b-150">Configure Identity services</span></span>

<span data-ttu-id="1108b-151">I servizi vengono aggiunti in `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="1108b-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="1108b-152">Il modello tipico consiste nel chiamare tutti i metodi `Add{Service}` e quindi chiamare tutti i metodi `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="1108b-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="1108b-153">Il codice evidenziato precedente viene configurato Identity con i valori di opzione predefiniti.</span><span class="sxs-lookup"><span data-stu-id="1108b-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="1108b-154">I servizi vengono resi disponibili per l'applicazione tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1108b-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="1108b-155">viene abilitato chiamando <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="1108b-155"> is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="1108b-156">`UseAuthentication`aggiunge il [middleware](xref:fundamentals/middleware/index) di autenticazione alla pipeline della richiesta.</span><span class="sxs-lookup"><span data-stu-id="1108b-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="1108b-157">L'app generata dal modello non usa l' [autorizzazione](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="1108b-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="1108b-158">`app.UseAuthorization`è incluso per assicurarsi che venga aggiunto nell'ordine corretto se l'app aggiunge l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="1108b-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="1108b-159">`UseRouting`, `UseAuthentication` , `UseAuthorization` e `UseEndpoints` devono essere chiamati nell'ordine indicato nel codice precedente.</span><span class="sxs-lookup"><span data-stu-id="1108b-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="1108b-160">Per ulteriori informazioni su `IdentityOptions` e `Startup` , vedere <xref:Microsoft.AspNetCore.Identity.IdentityOptions> e [avvio dell'applicazione](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="1108b-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="1108b-161">Registrazione, accesso e disconnessione del patibolo</span><span class="sxs-lookup"><span data-stu-id="1108b-161">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1108b-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1108b-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1108b-163">Aggiungere i file di registro, di accesso e di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="1108b-163">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="1108b-164">Seguire l' [identità del patibolo in un Razor progetto con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) le istruzioni di autorizzazione per generare il codice illustrato in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="1108b-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1108b-165">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="1108b-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1108b-166">Se il progetto è stato creato con il nome **app Web 1**, eseguire i comandi seguenti.</span><span class="sxs-lookup"><span data-stu-id="1108b-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="1108b-167">In caso contrario, utilizzare lo spazio dei nomi corretto per `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="1108b-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="1108b-168">PowerShell usa il punto e virgola come separatore di comandi.</span><span class="sxs-lookup"><span data-stu-id="1108b-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="1108b-169">Quando si usa PowerShell, usare il carattere di escape per il punto e virgola nell'elenco dei file o inserire l'elenco di file tra virgolette doppie, come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="1108b-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="1108b-170">Per ulteriori informazioni sull'impalcatura Identity , vedere la pagina relativa all' [identità del patibolo in un Razor progetto con autorizzazione](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="1108b-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="1108b-171">Esaminare il registro</span><span class="sxs-lookup"><span data-stu-id="1108b-171">Examine Register</span></span>

<span data-ttu-id="1108b-172">Quando un utente fa clic sul collegamento **Register** , `RegisterModel.OnPostAsync` viene richiamata l'azione.</span><span class="sxs-lookup"><span data-stu-id="1108b-172">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="1108b-173">L'utente viene creato da [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) nell' `_userManager` oggetto:</span><span class="sxs-lookup"><span data-stu-id="1108b-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="1108b-174">Se l'utente è stato creato correttamente, l'utente è connesso dalla chiamata a `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="1108b-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="1108b-175">Vedere la [conferma dell'account](xref:security/authentication/accconfirm#prevent-login-at-registration) per i passaggi per impedire l'accesso immediato alla registrazione.</span><span class="sxs-lookup"><span data-stu-id="1108b-175">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="1108b-176">Accesso</span><span class="sxs-lookup"><span data-stu-id="1108b-176">Log in</span></span>

<span data-ttu-id="1108b-177">Il modulo di accesso viene visualizzato quando:</span><span class="sxs-lookup"><span data-stu-id="1108b-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="1108b-178">Il collegamento **Accedi** è selezionato.</span><span class="sxs-lookup"><span data-stu-id="1108b-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="1108b-179">Un utente tenta di accedere a una pagina con restrizioni che non è autorizzata ad accedere **o** quando non è stata autenticata dal sistema.</span><span class="sxs-lookup"><span data-stu-id="1108b-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="1108b-180">Quando viene inviato il modulo nella pagina di accesso, `OnPostAsync` viene chiamata l'azione.</span><span class="sxs-lookup"><span data-stu-id="1108b-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="1108b-181">`PasswordSignInAsync`viene chiamato sull' `_signInManager` oggetto.</span><span class="sxs-lookup"><span data-stu-id="1108b-181">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="1108b-182">Per informazioni su come prendere decisioni relative alle autorizzazioni, vedere <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="1108b-182">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="1108b-183">Effettuare la disconnessione</span><span class="sxs-lookup"><span data-stu-id="1108b-183">Log out</span></span>

<span data-ttu-id="1108b-184">Il collegamento **Disconnetti** richiama l' `LogoutModel.OnPost` azione.</span><span class="sxs-lookup"><span data-stu-id="1108b-184">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="1108b-185">Nel codice precedente, il codice `return RedirectToPage();` deve essere un reindirizzamento in modo che il browser esegua una nuova richiesta e venga aggiornata l'identità dell'utente.</span><span class="sxs-lookup"><span data-stu-id="1108b-185">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="1108b-186">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) Cancella le attestazioni dell'utente archiviate in un cookie.</span><span class="sxs-lookup"><span data-stu-id="1108b-186">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="1108b-187">Post viene specificato nelle *pagine/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1108b-187">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="1108b-188">TestIdentity</span><span class="sxs-lookup"><span data-stu-id="1108b-188">Test Identity</span></span>

<span data-ttu-id="1108b-189">I modelli di progetto Web predefiniti consentono l'accesso anonimo alle Home page.</span><span class="sxs-lookup"><span data-stu-id="1108b-189">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="1108b-190">Per eseguire il test Identity , aggiungere [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="1108b-190">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="1108b-191">Se è stato eseguito l'accesso, disconnettersi. Eseguire l'app e selezionare il collegamento per la **privacy** .</span><span class="sxs-lookup"><span data-stu-id="1108b-191">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="1108b-192">Si verrà reindirizzati alla pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="1108b-192">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="1108b-193">EsplorareIdentity</span><span class="sxs-lookup"><span data-stu-id="1108b-193">Explore Identity</span></span>

<span data-ttu-id="1108b-194">Per esplorare Identity in modo più dettagliato:</span><span class="sxs-lookup"><span data-stu-id="1108b-194">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="1108b-195">Crea origine interfaccia utente con identità completa</span><span class="sxs-lookup"><span data-stu-id="1108b-195">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="1108b-196">Esaminare l'origine di ogni pagina ed eseguire un'istruzione alla volta nel debugger.</span><span class="sxs-lookup"><span data-stu-id="1108b-196">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="1108b-197">Componenti</span><span class="sxs-lookup"><span data-stu-id="1108b-197"> Components</span></span>

<span data-ttu-id="1108b-198">Tutti i Identity pacchetti NuGet dipendenti da sono inclusi nel [framework condiviso ASP.NET Core](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="1108b-198">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="1108b-199">Il pacchetto primario per Identity è [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="1108b-199">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="1108b-200">Questo pacchetto contiene il set principale di interfacce per ASP.NET Core Identity ed è incluso in `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="1108b-200">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="1108b-201">Migrazione a ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="1108b-201">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="1108b-202">Per ulteriori informazioni e istruzioni sulla migrazione dell' Identity archivio esistente, vedere [eseguire la migrazione Identity dell'autenticazione e ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="1108b-202">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="1108b-203">Impostazione della complessità della password</span><span class="sxs-lookup"><span data-stu-id="1108b-203">Setting password strength</span></span>

<span data-ttu-id="1108b-204">Vedere [configurazione](#pw) per un esempio che consente di impostare i requisiti minimi per le password.</span><span class="sxs-lookup"><span data-stu-id="1108b-204">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="1108b-205">AddDefaultIdentity e AddIdentity</span><span class="sxs-lookup"><span data-stu-id="1108b-205">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="1108b-206"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>è stato introdotto in ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="1108b-206"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="1108b-207">`AddDefaultIdentity`La chiamata a è simile alla chiamata a quanto segue:</span><span class="sxs-lookup"><span data-stu-id="1108b-207">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="1108b-208">Per altre informazioni, vedere [origine AddDefaultIdentity](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="1108b-208">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="1108b-209">Impedisci la pubblicazione di Identity Asset statici</span><span class="sxs-lookup"><span data-stu-id="1108b-209">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="1108b-210">Per evitare la pubblicazione Identity di risorse statiche (fogli di stile e file JavaScript per Identity l'interfaccia utente) nella radice Web, aggiungere la `ResolveStaticWebAssetsInputsDependsOn` proprietà e la `RemoveIdentityAssets` destinazione seguenti al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="1108b-210">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

## <a name="next-steps"></a><span data-ttu-id="1108b-211">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="1108b-211">Next Steps</span></span>

* <span data-ttu-id="1108b-212">[IdentityCodice sorgente ASP.NET Core](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="1108b-212">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="1108b-213">Per informazioni sulla configurazione di using SQLite, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="1108b-213">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="1108b-214">[ConfigurareIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="1108b-214">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1108b-215">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1108b-215">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1108b-216">ASP.NET Core Identity è un sistema di appartenenza che aggiunge la funzionalità di accesso alle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1108b-216">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="1108b-217">Gli utenti possono creare un account con le informazioni di accesso archiviate in Identity o possono usare un provider di accesso esterno.</span><span class="sxs-lookup"><span data-stu-id="1108b-217">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="1108b-218">I provider di accesso esterni supportati includono [Facebook, Google, account Microsoft e Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="1108b-218">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

Identity<span data-ttu-id="1108b-219">può essere configurato usando un database di SQL Server per archiviare i nomi utente, le password e i dati di profilo.</span><span class="sxs-lookup"><span data-stu-id="1108b-219"> can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="1108b-220">In alternativa, è possibile usare un altro archivio permanente, ad esempio archiviazione tabelle di Azure.</span><span class="sxs-lookup"><span data-stu-id="1108b-220">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="1108b-221">Consente di [visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([come scaricare)](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="1108b-221">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="1108b-222">In questo argomento si apprenderà come usare Identity per registrare, accedere e disconnettere un utente.</span><span class="sxs-lookup"><span data-stu-id="1108b-222">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="1108b-223">Per istruzioni più dettagliate sulla creazione di app che usano Identity , vedere la sezione passaggi successivi alla fine di questo articolo.</span><span class="sxs-lookup"><span data-stu-id="1108b-223">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="1108b-224">AddDefaultIdentity e AddIdentity</span><span class="sxs-lookup"><span data-stu-id="1108b-224">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="1108b-225"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>è stato introdotto in ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="1108b-225"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="1108b-226">`AddDefaultIdentity`La chiamata a è simile alla chiamata a quanto segue:</span><span class="sxs-lookup"><span data-stu-id="1108b-226">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="1108b-227">Per altre informazioni, vedere [origine AddDefaultIdentity](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="1108b-227">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="1108b-228">Creare un'app Web con l'autenticazione</span><span class="sxs-lookup"><span data-stu-id="1108b-228">Create a Web app with authentication</span></span>

<span data-ttu-id="1108b-229">Creare un progetto di applicazione Web di ASP.NET Core con singoli account utente.</span><span class="sxs-lookup"><span data-stu-id="1108b-229">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1108b-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1108b-230">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1108b-231">Selezionare **file** > **nuovo** > **progetto**.</span><span class="sxs-lookup"><span data-stu-id="1108b-231">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="1108b-232">Selezionare **Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="1108b-232">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="1108b-233">Denominare il progetto **app Web 1** in modo che abbia lo stesso spazio dei nomi del download del progetto.</span><span class="sxs-lookup"><span data-stu-id="1108b-233">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="1108b-234">Fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="1108b-234">Click **OK**.</span></span>
* <span data-ttu-id="1108b-235">Selezionare un' **applicazione Web**ASP.NET Core, quindi selezionare **Modifica autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="1108b-235">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="1108b-236">Selezionare **singoli account utente** e fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="1108b-236">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1108b-237">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="1108b-237">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="1108b-238">Il progetto generato fornisce [ASP.NET Core Identity ](xref:security/authentication/identity) come [ Razor libreria di classi](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="1108b-238">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="1108b-239">La Identity Razor libreria di classi espone gli endpoint con l' `Identity` area.</span><span class="sxs-lookup"><span data-stu-id="1108b-239">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="1108b-240">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="1108b-240">For example:</span></span>

* <span data-ttu-id="1108b-241">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="1108b-241">/Identity/Account/Login</span></span>
* <span data-ttu-id="1108b-242">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="1108b-242">/Identity/Account/Logout</span></span>
* <span data-ttu-id="1108b-243">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="1108b-243">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="1108b-244">Applicare le migrazioni</span><span class="sxs-lookup"><span data-stu-id="1108b-244">Apply migrations</span></span>

<span data-ttu-id="1108b-245">Applicare le migrazioni per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="1108b-245">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1108b-246">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1108b-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1108b-247">Eseguire il comando seguente nella console di gestione pacchetti (PMC):</span><span class="sxs-lookup"><span data-stu-id="1108b-247">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="1108b-248">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="1108b-248">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="1108b-249">Registro di test e account di accesso</span><span class="sxs-lookup"><span data-stu-id="1108b-249">Test Register and Login</span></span>

<span data-ttu-id="1108b-250">Eseguire l'app e registrare un utente.</span><span class="sxs-lookup"><span data-stu-id="1108b-250">Run the app and register a user.</span></span> <span data-ttu-id="1108b-251">A seconda delle dimensioni dello schermo, potrebbe essere necessario selezionare l'interruttore di spostamento per visualizzare i collegamenti di **Registro** e di **accesso** .</span><span class="sxs-lookup"><span data-stu-id="1108b-251">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="1108b-252">Configurare i Identity Servizi</span><span class="sxs-lookup"><span data-stu-id="1108b-252">Configure Identity services</span></span>

<span data-ttu-id="1108b-253">I servizi vengono aggiunti in `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="1108b-253">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="1108b-254">Il modello tipico consiste nel chiamare tutti i metodi `Add{Service}` e quindi chiamare tutti i metodi `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="1108b-254">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="1108b-255">Il codice precedente Configura Identity con i valori di opzione predefiniti.</span><span class="sxs-lookup"><span data-stu-id="1108b-255">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="1108b-256">I servizi vengono resi disponibili per l'applicazione tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1108b-256">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="1108b-257">viene abilitato chiamando [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="1108b-257"> is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="1108b-258">`UseAuthentication`aggiunge il [middleware](xref:fundamentals/middleware/index) di autenticazione alla pipeline della richiesta.</span><span class="sxs-lookup"><span data-stu-id="1108b-258">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="1108b-259">Per ulteriori informazioni, vedere la [Classe IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) e l' [avvio dell'applicazione](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="1108b-259">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="1108b-260">Registrazione, accesso e disconnessione del patibolo</span><span class="sxs-lookup"><span data-stu-id="1108b-260">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="1108b-261">Seguire l' [identità del patibolo in un Razor progetto con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) le istruzioni di autorizzazione per generare il codice illustrato in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="1108b-261">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1108b-262">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1108b-262">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1108b-263">Aggiungere i file di registro, di accesso e di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="1108b-263">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1108b-264">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="1108b-264">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1108b-265">Se il progetto è stato creato con il nome **app Web 1**, eseguire i comandi seguenti.</span><span class="sxs-lookup"><span data-stu-id="1108b-265">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="1108b-266">In caso contrario, utilizzare lo spazio dei nomi corretto per `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="1108b-266">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="1108b-267">PowerShell usa il punto e virgola come separatore di comandi.</span><span class="sxs-lookup"><span data-stu-id="1108b-267">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="1108b-268">Quando si usa PowerShell, usare il carattere di escape per il punto e virgola nell'elenco dei file o inserire l'elenco di file tra virgolette doppie, come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="1108b-268">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="1108b-269">Esaminare il registro</span><span class="sxs-lookup"><span data-stu-id="1108b-269">Examine Register</span></span>

<span data-ttu-id="1108b-270">Quando un utente fa clic sul collegamento **Register** , `RegisterModel.OnPostAsync` viene richiamata l'azione.</span><span class="sxs-lookup"><span data-stu-id="1108b-270">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="1108b-271">L'utente viene creato da [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) nell' `_userManager` oggetto:</span><span class="sxs-lookup"><span data-stu-id="1108b-271">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="1108b-272">Se l'utente è stato creato correttamente, l'utente è connesso dalla chiamata a `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="1108b-272">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="1108b-273">**Nota:** Vedere la [conferma dell'account](xref:security/authentication/accconfirm#prevent-login-at-registration) per i passaggi per impedire l'accesso immediato alla registrazione.</span><span class="sxs-lookup"><span data-stu-id="1108b-273">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="1108b-274">Accesso</span><span class="sxs-lookup"><span data-stu-id="1108b-274">Log in</span></span>

<span data-ttu-id="1108b-275">Il modulo di accesso viene visualizzato quando:</span><span class="sxs-lookup"><span data-stu-id="1108b-275">The Login form is displayed when:</span></span>

* <span data-ttu-id="1108b-276">Il collegamento **Accedi** è selezionato.</span><span class="sxs-lookup"><span data-stu-id="1108b-276">The **Log in** link is selected.</span></span>
* <span data-ttu-id="1108b-277">Un utente tenta di accedere a una pagina con restrizioni che non è autorizzata ad accedere **o** quando non è stata autenticata dal sistema.</span><span class="sxs-lookup"><span data-stu-id="1108b-277">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="1108b-278">Quando viene inviato il modulo nella pagina di accesso, `OnPostAsync` viene chiamata l'azione.</span><span class="sxs-lookup"><span data-stu-id="1108b-278">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="1108b-279">`PasswordSignInAsync`viene chiamato sull' `_signInManager` oggetto.</span><span class="sxs-lookup"><span data-stu-id="1108b-279">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="1108b-280">Per informazioni su come prendere decisioni relative alle autorizzazioni, vedere <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="1108b-280">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="1108b-281">Effettuare la disconnessione</span><span class="sxs-lookup"><span data-stu-id="1108b-281">Log out</span></span>

<span data-ttu-id="1108b-282">Il collegamento **Disconnetti** richiama l' `LogoutModel.OnPost` azione.</span><span class="sxs-lookup"><span data-stu-id="1108b-282">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="1108b-283">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) Cancella le attestazioni dell'utente archiviate in un cookie.</span><span class="sxs-lookup"><span data-stu-id="1108b-283">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="1108b-284">Post viene specificato nelle *pagine/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1108b-284">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="1108b-285">TestIdentity</span><span class="sxs-lookup"><span data-stu-id="1108b-285">Test Identity</span></span>

<span data-ttu-id="1108b-286">I modelli di progetto Web predefiniti consentono l'accesso anonimo alle Home page.</span><span class="sxs-lookup"><span data-stu-id="1108b-286">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="1108b-287">Per eseguire Identity il test, aggiungere [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) alla pagina privacy.</span><span class="sxs-lookup"><span data-stu-id="1108b-287">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="1108b-288">Se è stato eseguito l'accesso, disconnettersi. Eseguire l'app e selezionare il collegamento per la **privacy** .</span><span class="sxs-lookup"><span data-stu-id="1108b-288">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="1108b-289">Si verrà reindirizzati alla pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="1108b-289">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="1108b-290">EsplorareIdentity</span><span class="sxs-lookup"><span data-stu-id="1108b-290">Explore Identity</span></span>

<span data-ttu-id="1108b-291">Per esplorare Identity in modo più dettagliato:</span><span class="sxs-lookup"><span data-stu-id="1108b-291">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="1108b-292">Crea origine interfaccia utente con identità completa</span><span class="sxs-lookup"><span data-stu-id="1108b-292">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="1108b-293">Esaminare l'origine di ogni pagina ed eseguire un'istruzione alla volta nel debugger.</span><span class="sxs-lookup"><span data-stu-id="1108b-293">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="1108b-294">Componenti</span><span class="sxs-lookup"><span data-stu-id="1108b-294"> Components</span></span>

<span data-ttu-id="1108b-295">Tutti i Identity pacchetti NuGet dipendenti sono inclusi nel [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="1108b-295">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="1108b-296">Il pacchetto primario per Identity è [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="1108b-296">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="1108b-297">Questo pacchetto contiene il set principale di interfacce per ASP.NET Core Identity ed è incluso in `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="1108b-297">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="1108b-298">Migrazione a ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="1108b-298">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="1108b-299">Per ulteriori informazioni e istruzioni sulla migrazione dell' Identity archivio esistente, vedere [eseguire la migrazione Identity dell'autenticazione e ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="1108b-299">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="1108b-300">Impostazione della complessità della password</span><span class="sxs-lookup"><span data-stu-id="1108b-300">Setting password strength</span></span>

<span data-ttu-id="1108b-301">Vedere [configurazione](#pw) per un esempio che consente di impostare i requisiti minimi per le password.</span><span class="sxs-lookup"><span data-stu-id="1108b-301">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1108b-302">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="1108b-302">Next Steps</span></span>

* <span data-ttu-id="1108b-303">Per informazioni sulla configurazione di using SQLite, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="1108b-303">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="1108b-304">[ConfigurareIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="1108b-304">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
