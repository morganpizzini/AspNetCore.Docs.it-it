---
title: Introduzione a Identity on ASP.NET Core
author: rick-anderson
description: Usare Identity con un'app ASP.NET Core. Informazioni su come impostare i requisiti per le password (RequireDigit, RequiredLength, RequiredUniqueChars e altro).
ms.author: riande
ms.date: 7/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: dd3296db568700a363c427398f02239846a46ada
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445431"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="c550c-104">Introduzione a Identity on ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c550c-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c550c-105">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c550c-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c550c-106">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="c550c-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="c550c-107">È un'API che supporta la funzionalità di accesso dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="c550c-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="c550c-108">Gestisce utenti, password, dati di profilo, ruoli, attestazioni, token, conferma tramite posta elettronica e altro ancora.</span><span class="sxs-lookup"><span data-stu-id="c550c-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="c550c-109">Gli utenti possono creare un account con le informazioni di accesso archiviate in Identity o possono usare un provider di accesso esterno.</span><span class="sxs-lookup"><span data-stu-id="c550c-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="c550c-110">I provider di accesso esterni supportati includono [Facebook, Google, account Microsoft e Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="c550c-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="c550c-111">Il [ Identity codice sorgente](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) è disponibile in GitHub.</span><span class="sxs-lookup"><span data-stu-id="c550c-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="c550c-112">[Impalcatura Identity ](xref:security/authentication/scaffold-identity) e visualizzare i file generati per esaminare l'interazione del modello con Identity .</span><span class="sxs-lookup"><span data-stu-id="c550c-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

Identity<span data-ttu-id="c550c-113">viene in genere configurato utilizzando un database di SQL Server per archiviare i nomi utente, le password e i dati di profilo.</span><span class="sxs-lookup"><span data-stu-id="c550c-113"> is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="c550c-114">In alternativa, è possibile usare un altro archivio permanente, ad esempio archiviazione tabelle di Azure.</span><span class="sxs-lookup"><span data-stu-id="c550c-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="c550c-115">In questo argomento si apprenderà come usare Identity per registrare, accedere e disconnettere un utente.</span><span class="sxs-lookup"><span data-stu-id="c550c-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="c550c-116">Nota: i modelli considerano il nome utente e il messaggio di posta elettronica come lo stesso per gli utenti.</span><span class="sxs-lookup"><span data-stu-id="c550c-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="c550c-117">Per istruzioni più dettagliate sulla creazione di app che usano Identity , vedere [passaggi successivi](#next).</span><span class="sxs-lookup"><span data-stu-id="c550c-117">For more detailed instructions about creating apps that use Identity, see [Next Steps](#next).</span></span>

<span data-ttu-id="c550c-118">[Piattaforma di identità Microsoft](/azure/active-directory/develop/) :</span><span class="sxs-lookup"><span data-stu-id="c550c-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="c550c-119">Evoluzione della piattaforma per sviluppatori Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="c550c-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="c550c-120">Non correlato a ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="c550c-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/Identity<span data-ttu-id="c550c-121">Server4.md)]</span><span class="sxs-lookup"><span data-stu-id="c550c-121">Server4.md)]</span></span>

<span data-ttu-id="c550c-122">Consente di [visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([come scaricare)](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="c550c-122">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="c550c-123">Creare un'app Web con l'autenticazione</span><span class="sxs-lookup"><span data-stu-id="c550c-123">Create a Web app with authentication</span></span>

<span data-ttu-id="c550c-124">Creare un progetto di applicazione Web di ASP.NET Core con singoli account utente.</span><span class="sxs-lookup"><span data-stu-id="c550c-124">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c550c-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c550c-125">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c550c-126">Selezionare **file** > **nuovo** > **progetto**.</span><span class="sxs-lookup"><span data-stu-id="c550c-126">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="c550c-127">Selezionare **Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="c550c-127">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="c550c-128">Denominare il progetto **app Web 1** in modo che abbia lo stesso spazio dei nomi del download del progetto.</span><span class="sxs-lookup"><span data-stu-id="c550c-128">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="c550c-129">Fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="c550c-129">Click **OK**.</span></span>
* <span data-ttu-id="c550c-130">Selezionare un' **applicazione Web**ASP.NET Core, quindi selezionare **Modifica autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="c550c-130">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="c550c-131">Selezionare **singoli account utente** e fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="c550c-131">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c550c-132">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="c550c-132">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="c550c-133">Il comando precedente crea un' Razor app Web con SQLite.</span><span class="sxs-lookup"><span data-stu-id="c550c-133">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="c550c-134">Per creare l'app Web con il database locale, eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="c550c-134">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="c550c-135">Il progetto generato fornisce [ASP.NET Core Identity ](xref:security/authentication/identity) come [ Razor libreria di classi](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="c550c-135">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="c550c-136">La Identity Razor libreria di classi espone gli endpoint con l' `Identity` area.</span><span class="sxs-lookup"><span data-stu-id="c550c-136">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="c550c-137">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c550c-137">For example:</span></span>

* <span data-ttu-id="c550c-138">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="c550c-138">/Identity/Account/Login</span></span>
* <span data-ttu-id="c550c-139">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="c550c-139">/Identity/Account/Logout</span></span>
* <span data-ttu-id="c550c-140">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="c550c-140">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="c550c-141">Applicare le migrazioni</span><span class="sxs-lookup"><span data-stu-id="c550c-141">Apply migrations</span></span>

<span data-ttu-id="c550c-142">Applicare le migrazioni per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="c550c-142">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c550c-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c550c-143">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c550c-144">Eseguire il comando seguente nella console di gestione pacchetti (PMC):</span><span class="sxs-lookup"><span data-stu-id="c550c-144">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="c550c-145">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="c550c-145">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c550c-146">Le migrazioni non sono necessarie in questo passaggio quando si usa SQLite.</span><span class="sxs-lookup"><span data-stu-id="c550c-146">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="c550c-147">Per il database locale, eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="c550c-147">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="c550c-148">Registro di test e account di accesso</span><span class="sxs-lookup"><span data-stu-id="c550c-148">Test Register and Login</span></span>

<span data-ttu-id="c550c-149">Eseguire l'app e registrare un utente.</span><span class="sxs-lookup"><span data-stu-id="c550c-149">Run the app and register a user.</span></span> <span data-ttu-id="c550c-150">A seconda delle dimensioni dello schermo, potrebbe essere necessario selezionare l'interruttore di spostamento per visualizzare i collegamenti di **Registro** e di **accesso** .</span><span class="sxs-lookup"><span data-stu-id="c550c-150">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="c550c-151">Configurare i Identity Servizi</span><span class="sxs-lookup"><span data-stu-id="c550c-151">Configure Identity services</span></span>

<span data-ttu-id="c550c-152">I servizi vengono aggiunti in `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c550c-152">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="c550c-153">Il modello tipico consiste nel chiamare tutti i metodi `Add{Service}` e quindi chiamare tutti i metodi `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="c550c-153">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="c550c-154">Il codice evidenziato precedente viene configurato Identity con i valori di opzione predefiniti.</span><span class="sxs-lookup"><span data-stu-id="c550c-154">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="c550c-155">I servizi vengono resi disponibili per l'applicazione tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c550c-155">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="c550c-156">viene abilitato chiamando <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="c550c-156"> is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="c550c-157">`UseAuthentication`aggiunge il [middleware](xref:fundamentals/middleware/index) di autenticazione alla pipeline della richiesta.</span><span class="sxs-lookup"><span data-stu-id="c550c-157">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="c550c-158">L'app generata dal modello non usa l' [autorizzazione](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="c550c-158">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="c550c-159">`app.UseAuthorization`è incluso per assicurarsi che venga aggiunto nell'ordine corretto se l'app aggiunge l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="c550c-159">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="c550c-160">`UseRouting`, `UseAuthentication` , `UseAuthorization` e `UseEndpoints` devono essere chiamati nell'ordine indicato nel codice precedente.</span><span class="sxs-lookup"><span data-stu-id="c550c-160">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="c550c-161">Per ulteriori informazioni su `IdentityOptions` e `Startup` , vedere <xref:Microsoft.AspNetCore.Identity.IdentityOptions> e [avvio dell'applicazione](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="c550c-161">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="c550c-162">Registro di ponteggi, account di accesso, disconnessione e RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="c550c-162">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c550c-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c550c-163">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c550c-164">Aggiungere i `Register` `Login` file,, `LogOut` e `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="c550c-164">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="c550c-165">Seguire l' [identità del patibolo in un Razor progetto con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) le istruzioni di autorizzazione per generare il codice illustrato in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="c550c-165">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c550c-166">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="c550c-166">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c550c-167">Se il progetto è stato creato con il nome **app Web 1**, eseguire i comandi seguenti.</span><span class="sxs-lookup"><span data-stu-id="c550c-167">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="c550c-168">In caso contrario, utilizzare lo spazio dei nomi corretto per `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="c550c-168">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="c550c-169">PowerShell usa il punto e virgola come separatore di comandi.</span><span class="sxs-lookup"><span data-stu-id="c550c-169">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="c550c-170">Quando si usa PowerShell, usare il carattere di escape per il punto e virgola nell'elenco dei file o inserire l'elenco di file tra virgolette doppie, come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="c550c-170">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="c550c-171">Per ulteriori informazioni sull'impalcatura Identity , vedere la pagina relativa all' [identità del patibolo in un Razor progetto con autorizzazione](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="c550c-171">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="c550c-172">Esaminare il registro</span><span class="sxs-lookup"><span data-stu-id="c550c-172">Examine Register</span></span>

<span data-ttu-id="c550c-173">Quando un utente fa clic sul pulsante **registra** nella `Register` pagina, `RegisterModel.OnPostAsync` viene richiamata l'azione.</span><span class="sxs-lookup"><span data-stu-id="c550c-173">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="c550c-174">L'utente viene creato da [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) nell' `_userManager` oggetto:</span><span class="sxs-lookup"><span data-stu-id="c550c-174">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="c550c-175">Accesso</span><span class="sxs-lookup"><span data-stu-id="c550c-175">Log in</span></span>

<span data-ttu-id="c550c-176">Il modulo di accesso viene visualizzato quando:</span><span class="sxs-lookup"><span data-stu-id="c550c-176">The Login form is displayed when:</span></span>

* <span data-ttu-id="c550c-177">Il collegamento **Accedi** è selezionato.</span><span class="sxs-lookup"><span data-stu-id="c550c-177">The **Log in** link is selected.</span></span>
* <span data-ttu-id="c550c-178">Un utente tenta di accedere a una pagina con restrizioni che non è autorizzata ad accedere **o** quando non è stata autenticata dal sistema.</span><span class="sxs-lookup"><span data-stu-id="c550c-178">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="c550c-179">Quando viene inviato il modulo nella pagina di accesso, `OnPostAsync` viene chiamata l'azione.</span><span class="sxs-lookup"><span data-stu-id="c550c-179">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="c550c-180">`PasswordSignInAsync`viene chiamato sull' `_signInManager` oggetto.</span><span class="sxs-lookup"><span data-stu-id="c550c-180">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="c550c-181">Per informazioni su come prendere decisioni relative alle autorizzazioni, vedere <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="c550c-181">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="c550c-182">Effettuare la disconnessione</span><span class="sxs-lookup"><span data-stu-id="c550c-182">Log out</span></span>

<span data-ttu-id="c550c-183">Il collegamento **Disconnetti** richiama l' `LogoutModel.OnPost` azione.</span><span class="sxs-lookup"><span data-stu-id="c550c-183">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="c550c-184">Nel codice precedente, il codice `return RedirectToPage();` deve essere un reindirizzamento in modo che il browser esegua una nuova richiesta e venga aggiornata l'identità dell'utente.</span><span class="sxs-lookup"><span data-stu-id="c550c-184">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="c550c-185">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) Cancella le attestazioni dell'utente archiviate in un cookie.</span><span class="sxs-lookup"><span data-stu-id="c550c-185">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="c550c-186">Post viene specificato nelle *pagine/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c550c-186">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="c550c-187">TestIdentity</span><span class="sxs-lookup"><span data-stu-id="c550c-187">Test Identity</span></span>

<span data-ttu-id="c550c-188">I modelli di progetto Web predefiniti consentono l'accesso anonimo alle Home page.</span><span class="sxs-lookup"><span data-stu-id="c550c-188">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="c550c-189">Per eseguire il test Identity , aggiungere [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="c550c-189">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="c550c-190">Se è stato eseguito l'accesso, disconnettersi. Eseguire l'app e selezionare il collegamento per la **privacy** .</span><span class="sxs-lookup"><span data-stu-id="c550c-190">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="c550c-191">Si verrà reindirizzati alla pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="c550c-191">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="c550c-192">EsplorareIdentity</span><span class="sxs-lookup"><span data-stu-id="c550c-192">Explore Identity</span></span>

<span data-ttu-id="c550c-193">Per esplorare Identity in modo più dettagliato:</span><span class="sxs-lookup"><span data-stu-id="c550c-193">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="c550c-194">Crea origine interfaccia utente con identità completa</span><span class="sxs-lookup"><span data-stu-id="c550c-194">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="c550c-195">Esaminare l'origine di ogni pagina ed eseguire un'istruzione alla volta nel debugger.</span><span class="sxs-lookup"><span data-stu-id="c550c-195">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="c550c-196">Componenti</span><span class="sxs-lookup"><span data-stu-id="c550c-196"> Components</span></span>

<span data-ttu-id="c550c-197">Tutti i Identity pacchetti NuGet dipendenti da sono inclusi nel [framework condiviso ASP.NET Core](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="c550c-197">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="c550c-198">Il pacchetto primario per Identity è [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="c550c-198">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="c550c-199">Questo pacchetto contiene il set principale di interfacce per ASP.NET Core Identity ed è incluso in `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="c550c-199">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="c550c-200">Migrazione a ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="c550c-200">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="c550c-201">Per ulteriori informazioni e istruzioni sulla migrazione dell' Identity archivio esistente, vedere [eseguire la migrazione Identity dell'autenticazione e ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="c550c-201">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="c550c-202">Impostazione della complessità della password</span><span class="sxs-lookup"><span data-stu-id="c550c-202">Setting password strength</span></span>

<span data-ttu-id="c550c-203">Vedere [configurazione](#pw) per un esempio che consente di impostare i requisiti minimi per le password.</span><span class="sxs-lookup"><span data-stu-id="c550c-203">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="c550c-204">AddDefault Identity e AggiungiIdentity</span><span class="sxs-lookup"><span data-stu-id="c550c-204">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="c550c-205"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>è stato introdotto in ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="c550c-205"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="c550c-206">`AddDefaultIdentity`La chiamata a è simile alla chiamata a quanto segue:</span><span class="sxs-lookup"><span data-stu-id="c550c-206">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="c550c-207">Per altre informazioni, vedere [ Identity origine AddDefault](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="c550c-207">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="c550c-208">Impedisci la pubblicazione di Identity Asset statici</span><span class="sxs-lookup"><span data-stu-id="c550c-208">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="c550c-209">Per evitare la pubblicazione Identity di risorse statiche (fogli di stile e file JavaScript per Identity l'interfaccia utente) nella radice Web, aggiungere la `ResolveStaticWebAssetsInputsDependsOn` proprietà e la `RemoveIdentityAssets` destinazione seguenti al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="c550c-209">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

<a name="next"></a>

## <a name="next-steps"></a><span data-ttu-id="c550c-210">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="c550c-210">Next Steps</span></span>

* <span data-ttu-id="c550c-211">[IdentityCodice sorgente ASP.NET Core](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="c550c-211">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="c550c-212">Per informazioni sulla configurazione di using SQLite, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="c550c-212">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="c550c-213">[ConfigurareIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="c550c-213">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c550c-214">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c550c-214">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c550c-215">ASP.NET Core Identity è un sistema di appartenenza che aggiunge la funzionalità di accesso alle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c550c-215">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="c550c-216">Gli utenti possono creare un account con le informazioni di accesso archiviate in Identity o possono usare un provider di accesso esterno.</span><span class="sxs-lookup"><span data-stu-id="c550c-216">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="c550c-217">I provider di accesso esterni supportati includono [Facebook, Google, account Microsoft e Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="c550c-217">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

Identity<span data-ttu-id="c550c-218">può essere configurato usando un database di SQL Server per archiviare i nomi utente, le password e i dati di profilo.</span><span class="sxs-lookup"><span data-stu-id="c550c-218"> can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="c550c-219">In alternativa, è possibile usare un altro archivio permanente, ad esempio archiviazione tabelle di Azure.</span><span class="sxs-lookup"><span data-stu-id="c550c-219">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="c550c-220">Consente di [visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([come scaricare)](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="c550c-220">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="c550c-221">In questo argomento si apprenderà come usare Identity per registrare, accedere e disconnettere un utente.</span><span class="sxs-lookup"><span data-stu-id="c550c-221">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="c550c-222">Per istruzioni più dettagliate sulla creazione di app che usano Identity , vedere la sezione passaggi successivi alla fine di questo articolo.</span><span class="sxs-lookup"><span data-stu-id="c550c-222">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="c550c-223">AddDefault Identity e AggiungiIdentity</span><span class="sxs-lookup"><span data-stu-id="c550c-223">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="c550c-224"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>è stato introdotto in ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="c550c-224"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="c550c-225">`AddDefaultIdentity`La chiamata a è simile alla chiamata a quanto segue:</span><span class="sxs-lookup"><span data-stu-id="c550c-225">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="c550c-226">Per altre informazioni, vedere [ Identity origine AddDefault](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="c550c-226">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="c550c-227">Creare un'app Web con l'autenticazione</span><span class="sxs-lookup"><span data-stu-id="c550c-227">Create a Web app with authentication</span></span>

<span data-ttu-id="c550c-228">Creare un progetto di applicazione Web di ASP.NET Core con singoli account utente.</span><span class="sxs-lookup"><span data-stu-id="c550c-228">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c550c-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c550c-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c550c-230">Selezionare **file** > **nuovo** > **progetto**.</span><span class="sxs-lookup"><span data-stu-id="c550c-230">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="c550c-231">Selezionare **Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="c550c-231">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="c550c-232">Denominare il progetto **app Web 1** in modo che abbia lo stesso spazio dei nomi del download del progetto.</span><span class="sxs-lookup"><span data-stu-id="c550c-232">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="c550c-233">Fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="c550c-233">Click **OK**.</span></span>
* <span data-ttu-id="c550c-234">Selezionare un' **applicazione Web**ASP.NET Core, quindi selezionare **Modifica autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="c550c-234">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="c550c-235">Selezionare **singoli account utente** e fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="c550c-235">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c550c-236">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="c550c-236">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="c550c-237">Il progetto generato fornisce [ASP.NET Core Identity ](xref:security/authentication/identity) come [ Razor libreria di classi](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="c550c-237">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="c550c-238">La Identity Razor libreria di classi espone gli endpoint con l' `Identity` area.</span><span class="sxs-lookup"><span data-stu-id="c550c-238">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="c550c-239">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c550c-239">For example:</span></span>

* <span data-ttu-id="c550c-240">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="c550c-240">/Identity/Account/Login</span></span>
* <span data-ttu-id="c550c-241">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="c550c-241">/Identity/Account/Logout</span></span>
* <span data-ttu-id="c550c-242">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="c550c-242">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="c550c-243">Applicare le migrazioni</span><span class="sxs-lookup"><span data-stu-id="c550c-243">Apply migrations</span></span>

<span data-ttu-id="c550c-244">Applicare le migrazioni per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="c550c-244">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c550c-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c550c-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c550c-246">Eseguire il comando seguente nella console di gestione pacchetti (PMC):</span><span class="sxs-lookup"><span data-stu-id="c550c-246">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="c550c-247">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="c550c-247">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="c550c-248">Registro di test e account di accesso</span><span class="sxs-lookup"><span data-stu-id="c550c-248">Test Register and Login</span></span>

<span data-ttu-id="c550c-249">Eseguire l'app e registrare un utente.</span><span class="sxs-lookup"><span data-stu-id="c550c-249">Run the app and register a user.</span></span> <span data-ttu-id="c550c-250">A seconda delle dimensioni dello schermo, potrebbe essere necessario selezionare l'interruttore di spostamento per visualizzare i collegamenti di **Registro** e di **accesso** .</span><span class="sxs-lookup"><span data-stu-id="c550c-250">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="c550c-251">Configurare i Identity Servizi</span><span class="sxs-lookup"><span data-stu-id="c550c-251">Configure Identity services</span></span>

<span data-ttu-id="c550c-252">I servizi vengono aggiunti in `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c550c-252">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="c550c-253">Il modello tipico consiste nel chiamare tutti i metodi `Add{Service}` e quindi chiamare tutti i metodi `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="c550c-253">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="c550c-254">Il codice precedente Configura Identity con i valori di opzione predefiniti.</span><span class="sxs-lookup"><span data-stu-id="c550c-254">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="c550c-255">I servizi vengono resi disponibili per l'applicazione tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c550c-255">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="c550c-256">viene abilitato chiamando [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="c550c-256"> is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="c550c-257">`UseAuthentication`aggiunge il [middleware](xref:fundamentals/middleware/index) di autenticazione alla pipeline della richiesta.</span><span class="sxs-lookup"><span data-stu-id="c550c-257">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="c550c-258">Per ulteriori informazioni, vedere la [ Identity classe Options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) e l' [avvio dell'applicazione](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="c550c-258">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="c550c-259">Registrazione, accesso e disconnessione del patibolo</span><span class="sxs-lookup"><span data-stu-id="c550c-259">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="c550c-260">Seguire l' [identità del patibolo in un Razor progetto con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) le istruzioni di autorizzazione per generare il codice illustrato in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="c550c-260">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c550c-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c550c-261">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c550c-262">Aggiungere i file di registro, di accesso e di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="c550c-262">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c550c-263">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="c550c-263">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c550c-264">Se il progetto è stato creato con il nome **app Web 1**, eseguire i comandi seguenti.</span><span class="sxs-lookup"><span data-stu-id="c550c-264">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="c550c-265">In caso contrario, utilizzare lo spazio dei nomi corretto per `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="c550c-265">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="c550c-266">PowerShell usa il punto e virgola come separatore di comandi.</span><span class="sxs-lookup"><span data-stu-id="c550c-266">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="c550c-267">Quando si usa PowerShell, usare il carattere di escape per il punto e virgola nell'elenco dei file o inserire l'elenco di file tra virgolette doppie, come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="c550c-267">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="c550c-268">Esaminare il registro</span><span class="sxs-lookup"><span data-stu-id="c550c-268">Examine Register</span></span>

<span data-ttu-id="c550c-269">Quando un utente fa clic sul collegamento **Register** , `RegisterModel.OnPostAsync` viene richiamata l'azione.</span><span class="sxs-lookup"><span data-stu-id="c550c-269">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="c550c-270">L'utente viene creato da [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) nell' `_userManager` oggetto:</span><span class="sxs-lookup"><span data-stu-id="c550c-270">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="c550c-271">Se l'utente è stato creato correttamente, l'utente è connesso dalla chiamata a `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="c550c-271">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="c550c-272">**Nota:** Vedere la [conferma dell'account](xref:security/authentication/accconfirm#prevent-login-at-registration) per i passaggi per impedire l'accesso immediato alla registrazione.</span><span class="sxs-lookup"><span data-stu-id="c550c-272">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="c550c-273">Accesso</span><span class="sxs-lookup"><span data-stu-id="c550c-273">Log in</span></span>

<span data-ttu-id="c550c-274">Il modulo di accesso viene visualizzato quando:</span><span class="sxs-lookup"><span data-stu-id="c550c-274">The Login form is displayed when:</span></span>

* <span data-ttu-id="c550c-275">Il collegamento **Accedi** è selezionato.</span><span class="sxs-lookup"><span data-stu-id="c550c-275">The **Log in** link is selected.</span></span>
* <span data-ttu-id="c550c-276">Un utente tenta di accedere a una pagina con restrizioni che non è autorizzata ad accedere **o** quando non è stata autenticata dal sistema.</span><span class="sxs-lookup"><span data-stu-id="c550c-276">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="c550c-277">Quando viene inviato il modulo nella pagina di accesso, `OnPostAsync` viene chiamata l'azione.</span><span class="sxs-lookup"><span data-stu-id="c550c-277">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="c550c-278">`PasswordSignInAsync`viene chiamato sull' `_signInManager` oggetto.</span><span class="sxs-lookup"><span data-stu-id="c550c-278">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="c550c-279">Per informazioni su come prendere decisioni relative alle autorizzazioni, vedere <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="c550c-279">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="c550c-280">Effettuare la disconnessione</span><span class="sxs-lookup"><span data-stu-id="c550c-280">Log out</span></span>

<span data-ttu-id="c550c-281">Il collegamento **Disconnetti** richiama l' `LogoutModel.OnPost` azione.</span><span class="sxs-lookup"><span data-stu-id="c550c-281">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="c550c-282">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) Cancella le attestazioni dell'utente archiviate in un cookie.</span><span class="sxs-lookup"><span data-stu-id="c550c-282">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="c550c-283">Post viene specificato nelle *pagine/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c550c-283">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="c550c-284">TestIdentity</span><span class="sxs-lookup"><span data-stu-id="c550c-284">Test Identity</span></span>

<span data-ttu-id="c550c-285">I modelli di progetto Web predefiniti consentono l'accesso anonimo alle Home page.</span><span class="sxs-lookup"><span data-stu-id="c550c-285">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="c550c-286">Per eseguire Identity il test, aggiungere [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) alla pagina privacy.</span><span class="sxs-lookup"><span data-stu-id="c550c-286">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="c550c-287">Se è stato eseguito l'accesso, disconnettersi. Eseguire l'app e selezionare il collegamento per la **privacy** .</span><span class="sxs-lookup"><span data-stu-id="c550c-287">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="c550c-288">Si verrà reindirizzati alla pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="c550c-288">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="c550c-289">EsplorareIdentity</span><span class="sxs-lookup"><span data-stu-id="c550c-289">Explore Identity</span></span>

<span data-ttu-id="c550c-290">Per esplorare Identity in modo più dettagliato:</span><span class="sxs-lookup"><span data-stu-id="c550c-290">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="c550c-291">Crea origine interfaccia utente con identità completa</span><span class="sxs-lookup"><span data-stu-id="c550c-291">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="c550c-292">Esaminare l'origine di ogni pagina ed eseguire un'istruzione alla volta nel debugger.</span><span class="sxs-lookup"><span data-stu-id="c550c-292">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="c550c-293">Componenti</span><span class="sxs-lookup"><span data-stu-id="c550c-293"> Components</span></span>

<span data-ttu-id="c550c-294">Tutti i Identity pacchetti NuGet dipendenti sono inclusi nel [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c550c-294">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c550c-295">Il pacchetto primario per Identity è [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="c550c-295">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="c550c-296">Questo pacchetto contiene il set principale di interfacce per ASP.NET Core Identity ed è incluso in `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="c550c-296">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="c550c-297">Migrazione a ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="c550c-297">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="c550c-298">Per ulteriori informazioni e istruzioni sulla migrazione dell' Identity archivio esistente, vedere [eseguire la migrazione Identity dell'autenticazione e ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="c550c-298">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="c550c-299">Impostazione della complessità della password</span><span class="sxs-lookup"><span data-stu-id="c550c-299">Setting password strength</span></span>

<span data-ttu-id="c550c-300">Vedere [configurazione](#pw) per un esempio che consente di impostare i requisiti minimi per le password.</span><span class="sxs-lookup"><span data-stu-id="c550c-300">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c550c-301">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="c550c-301">Next Steps</span></span>

* <span data-ttu-id="c550c-302">Per informazioni sulla configurazione di using SQLite, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="c550c-302">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="c550c-303">[ConfigurareIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="c550c-303">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
