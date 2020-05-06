---
title: Introduzione a Identity on ASP.NET Core
author: rick-anderson
description: Usare Identity con un'app ASP.NET Core. Informazioni su come impostare i requisiti per le password (RequireDigit, RequiredLength, RequiredUniqueChars e altro).
ms.author: riande
ms.date: 01/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: d596a8357c5c812b94950809eedf35718328747c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777007"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="d1f95-104">Introduzione all'identità in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d1f95-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d1f95-105">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d1f95-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d1f95-106">Identità ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d1f95-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="d1f95-107">È un'API che supporta la funzionalità di accesso dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="d1f95-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="d1f95-108">Gestisce utenti, password, dati di profilo, ruoli, attestazioni, token, conferma tramite posta elettronica e altro ancora.</span><span class="sxs-lookup"><span data-stu-id="d1f95-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="d1f95-109">Gli utenti possono creare un account con le informazioni di accesso archiviate in Identity oppure possono usare un provider di accesso esterno.</span><span class="sxs-lookup"><span data-stu-id="d1f95-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="d1f95-110">I provider di accesso esterni supportati includono [Facebook, Google, account Microsoft e Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="d1f95-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="d1f95-111">Il [codice sorgente di identità](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) è disponibile in GitHub.</span><span class="sxs-lookup"><span data-stu-id="d1f95-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="d1f95-112">[Identità del patibolo](xref:security/authentication/scaffold-identity) e visualizzare i file generati per esaminare l'interazione del modello con l'identità.</span><span class="sxs-lookup"><span data-stu-id="d1f95-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="d1f95-113">L'identità viene in genere configurata utilizzando un database SQL Server per archiviare i nomi utente, le password e i dati di profilo.</span><span class="sxs-lookup"><span data-stu-id="d1f95-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="d1f95-114">In alternativa, è possibile usare un altro archivio permanente, ad esempio archiviazione tabelle di Azure.</span><span class="sxs-lookup"><span data-stu-id="d1f95-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="d1f95-115">In questo argomento si apprenderà come usare l'identità per la registrazione, l'accesso e la disconnessione di un utente.</span><span class="sxs-lookup"><span data-stu-id="d1f95-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="d1f95-116">Nota: i modelli considerano il nome utente e il messaggio di posta elettronica come lo stesso per gli utenti.</span><span class="sxs-lookup"><span data-stu-id="d1f95-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="d1f95-117">Per istruzioni più dettagliate sulla creazione di app che usano l'identità, vedere la sezione passaggi successivi alla fine di questo articolo.</span><span class="sxs-lookup"><span data-stu-id="d1f95-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="d1f95-118">[Piattaforma di identità Microsoft](/azure/active-directory/develop/) :</span><span class="sxs-lookup"><span data-stu-id="d1f95-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="d1f95-119">Evoluzione della piattaforma per sviluppatori Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="d1f95-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="d1f95-120">Non correlato all'identità del ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d1f95-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="d1f95-121">Consente di [visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([come scaricare)](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="d1f95-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="d1f95-122">Creare un'app Web con l'autenticazione</span><span class="sxs-lookup"><span data-stu-id="d1f95-122">Create a Web app with authentication</span></span>

<span data-ttu-id="d1f95-123">Creare un progetto di applicazione Web di ASP.NET Core con singoli account utente.</span><span class="sxs-lookup"><span data-stu-id="d1f95-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d1f95-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1f95-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d1f95-125">Selezionare **file** > **nuovo** > **progetto**.</span><span class="sxs-lookup"><span data-stu-id="d1f95-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="d1f95-126">Selezionare **Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="d1f95-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="d1f95-127">Denominare il progetto **app Web 1** in modo che abbia lo stesso spazio dei nomi del download del progetto.</span><span class="sxs-lookup"><span data-stu-id="d1f95-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="d1f95-128">Fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="d1f95-128">Click **OK**.</span></span>
* <span data-ttu-id="d1f95-129">Selezionare un' **applicazione Web**ASP.NET Core, quindi selezionare **Modifica autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="d1f95-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="d1f95-130">Selezionare **singoli account utente** e fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="d1f95-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d1f95-131">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="d1f95-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="d1f95-132">Il comando precedente crea un'app Web Razor con SQLite.</span><span class="sxs-lookup"><span data-stu-id="d1f95-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="d1f95-133">Per creare l'app Web con il database locale, eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="d1f95-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="d1f95-134">Il progetto generato fornisce [ASP.NET Core identità](xref:security/authentication/identity) come [libreria di classi Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="d1f95-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="d1f95-135">La libreria della classe Razor Identity espone gli endpoint con `Identity` l'area.</span><span class="sxs-lookup"><span data-stu-id="d1f95-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="d1f95-136">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="d1f95-136">For example:</span></span>

* <span data-ttu-id="d1f95-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="d1f95-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="d1f95-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="d1f95-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="d1f95-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="d1f95-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="d1f95-140">Applicare le migrazioni</span><span class="sxs-lookup"><span data-stu-id="d1f95-140">Apply migrations</span></span>

<span data-ttu-id="d1f95-141">Applicare le migrazioni per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="d1f95-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d1f95-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1f95-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d1f95-143">Eseguire il comando seguente nella console di gestione pacchetti (PMC):</span><span class="sxs-lookup"><span data-stu-id="d1f95-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="d1f95-144">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="d1f95-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d1f95-145">Le migrazioni non sono necessarie in questo passaggio quando si usa SQLite.</span><span class="sxs-lookup"><span data-stu-id="d1f95-145">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="d1f95-146">Per il database locale, eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="d1f95-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="d1f95-147">Registro di test e account di accesso</span><span class="sxs-lookup"><span data-stu-id="d1f95-147">Test Register and Login</span></span>

<span data-ttu-id="d1f95-148">Eseguire l'app e registrare un utente.</span><span class="sxs-lookup"><span data-stu-id="d1f95-148">Run the app and register a user.</span></span> <span data-ttu-id="d1f95-149">A seconda delle dimensioni dello schermo, potrebbe essere necessario selezionare l'interruttore di spostamento per visualizzare i collegamenti di **Registro** e di **accesso** .</span><span class="sxs-lookup"><span data-stu-id="d1f95-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="d1f95-150">Configurare i servizi di identità</span><span class="sxs-lookup"><span data-stu-id="d1f95-150">Configure Identity services</span></span>

<span data-ttu-id="d1f95-151">I servizi vengono aggiunti `ConfigureServices`in.</span><span class="sxs-lookup"><span data-stu-id="d1f95-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="d1f95-152">Il modello tipico consiste nel chiamare tutti i metodi `Add{Service}` e quindi chiamare tutti i metodi `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="d1f95-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="d1f95-153">Il codice evidenziato precedente configura l'identità con i valori delle opzioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="d1f95-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="d1f95-154">I servizi vengono resi disponibili per l'applicazione tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d1f95-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="d1f95-155">L'identità viene abilitata <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>chiamando.</span><span class="sxs-lookup"><span data-stu-id="d1f95-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="d1f95-156">`UseAuthentication`aggiunge il [middleware](xref:fundamentals/middleware/index) di autenticazione alla pipeline della richiesta.</span><span class="sxs-lookup"><span data-stu-id="d1f95-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="d1f95-157">L'app generata dal modello non usa l' [autorizzazione](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="d1f95-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="d1f95-158">`app.UseAuthorization`è incluso per assicurarsi che venga aggiunto nell'ordine corretto se l'app aggiunge l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="d1f95-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="d1f95-159">`UseRouting``UseAuthorization` `UseEndpoints` , `UseAuthentication`, e devono essere chiamati nell'ordine indicato nel codice precedente.</span><span class="sxs-lookup"><span data-stu-id="d1f95-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="d1f95-160">Per ulteriori informazioni su `IdentityOptions` e `Startup`, vedere <xref:Microsoft.AspNetCore.Identity.IdentityOptions> e [avvio dell'applicazione](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="d1f95-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="d1f95-161">Registrazione, accesso e disconnessione del patibolo</span><span class="sxs-lookup"><span data-stu-id="d1f95-161">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d1f95-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1f95-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d1f95-163">Aggiungere i file di registro, di accesso e di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="d1f95-163">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="d1f95-164">Seguire l' [identità del patibolo in un progetto Razor con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) le istruzioni di autorizzazione per generare il codice illustrato in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="d1f95-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d1f95-165">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="d1f95-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d1f95-166">Se il progetto è stato creato con il nome **app Web 1**, eseguire i comandi seguenti.</span><span class="sxs-lookup"><span data-stu-id="d1f95-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="d1f95-167">In caso contrario, utilizzare lo spazio dei `ApplicationDbContext`nomi corretto per:</span><span class="sxs-lookup"><span data-stu-id="d1f95-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="d1f95-168">PowerShell usa il punto e virgola come separatore di comandi.</span><span class="sxs-lookup"><span data-stu-id="d1f95-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="d1f95-169">Quando si usa PowerShell, usare il carattere di escape per il punto e virgola nell'elenco dei file o inserire l'elenco di file tra virgolette doppie, come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="d1f95-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="d1f95-170">Per altre informazioni sull'identità di impalcatura, vedere la pagina relativa all' [identità di impalcatura in un progetto Razor con autorizzazione](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="d1f95-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="d1f95-171">Esaminare il registro</span><span class="sxs-lookup"><span data-stu-id="d1f95-171">Examine Register</span></span>

<span data-ttu-id="d1f95-172">Quando un utente fa clic sul collegamento **Register** , `RegisterModel.OnPostAsync` viene richiamata l'azione.</span><span class="sxs-lookup"><span data-stu-id="d1f95-172">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="d1f95-173">L'utente viene creato da [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) sull' `_userManager` oggetto.</span><span class="sxs-lookup"><span data-stu-id="d1f95-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="d1f95-174">`_userManager`viene fornito dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="d1f95-174">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="d1f95-175">Se l'utente è stato creato correttamente, l'utente è connesso dalla chiamata a `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="d1f95-175">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="d1f95-176">Vedere la [conferma dell'account](xref:security/authentication/accconfirm#prevent-login-at-registration) per i passaggi per impedire l'accesso immediato alla registrazione.</span><span class="sxs-lookup"><span data-stu-id="d1f95-176">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="d1f95-177">Accesso</span><span class="sxs-lookup"><span data-stu-id="d1f95-177">Log in</span></span>

<span data-ttu-id="d1f95-178">Il modulo di accesso viene visualizzato quando:</span><span class="sxs-lookup"><span data-stu-id="d1f95-178">The Login form is displayed when:</span></span>

* <span data-ttu-id="d1f95-179">Il collegamento **Accedi** è selezionato.</span><span class="sxs-lookup"><span data-stu-id="d1f95-179">The **Log in** link is selected.</span></span>
* <span data-ttu-id="d1f95-180">Un utente tenta di accedere a una pagina con restrizioni che non è autorizzata ad accedere **o** quando non è stata autenticata dal sistema.</span><span class="sxs-lookup"><span data-stu-id="d1f95-180">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="d1f95-181">Quando viene inviato il modulo nella pagina di accesso, viene `OnPostAsync` chiamata l'azione.</span><span class="sxs-lookup"><span data-stu-id="d1f95-181">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="d1f95-182">`PasswordSignInAsync`viene chiamato sull' `_signInManager` oggetto, fornito dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="d1f95-182">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="d1f95-183">La classe `Controller` base espone una `User` proprietà a cui è possibile accedere dai metodi del controller.</span><span class="sxs-lookup"><span data-stu-id="d1f95-183">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="d1f95-184">È possibile, ad esempio, `User.Claims` enumerare e prendere decisioni di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="d1f95-184">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="d1f95-185">Per altre informazioni, vedere <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="d1f95-185">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="d1f95-186">Effettuare la disconnessione</span><span class="sxs-lookup"><span data-stu-id="d1f95-186">Log out</span></span>

<span data-ttu-id="d1f95-187">Il collegamento **Disconnetti** richiama l' `LogoutModel.OnPost` azione.</span><span class="sxs-lookup"><span data-stu-id="d1f95-187">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="d1f95-188">Nel codice precedente, il codice `return RedirectToPage();` deve essere un reindirizzamento in modo che il browser esegua una nuova richiesta e venga aggiornata l'identità dell'utente.</span><span class="sxs-lookup"><span data-stu-id="d1f95-188">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="d1f95-189">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) Cancella le attestazioni dell'utente archiviate in un cookie.</span><span class="sxs-lookup"><span data-stu-id="d1f95-189">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="d1f95-190">Post viene specificato nelle *pagine/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d1f95-190">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="d1f95-191">Identità del test</span><span class="sxs-lookup"><span data-stu-id="d1f95-191">Test Identity</span></span>

<span data-ttu-id="d1f95-192">I modelli di progetto Web predefiniti consentono l'accesso anonimo alle Home page.</span><span class="sxs-lookup"><span data-stu-id="d1f95-192">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="d1f95-193">Per verificare l'identità, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)aggiungere:</span><span class="sxs-lookup"><span data-stu-id="d1f95-193">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="d1f95-194">Se è stato eseguito l'accesso, disconnettersi. Eseguire l'app e selezionare il collegamento per la **privacy** .</span><span class="sxs-lookup"><span data-stu-id="d1f95-194">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="d1f95-195">Si verrà reindirizzati alla pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="d1f95-195">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="d1f95-196">Esplora identità</span><span class="sxs-lookup"><span data-stu-id="d1f95-196">Explore Identity</span></span>

<span data-ttu-id="d1f95-197">Per esplorare l'identità in modo più dettagliato:</span><span class="sxs-lookup"><span data-stu-id="d1f95-197">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="d1f95-198">Crea origine interfaccia utente con identità completa</span><span class="sxs-lookup"><span data-stu-id="d1f95-198">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="d1f95-199">Esaminare l'origine di ogni pagina ed eseguire un'istruzione alla volta nel debugger.</span><span class="sxs-lookup"><span data-stu-id="d1f95-199">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="d1f95-200">Componenti Identity</span><span class="sxs-lookup"><span data-stu-id="d1f95-200">Identity Components</span></span>

<span data-ttu-id="d1f95-201">Tutti i pacchetti NuGet dipendenti dall'identità sono inclusi nel [Framework condiviso ASP.NET Core](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="d1f95-201">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="d1f95-202">Il pacchetto primario per Identity è [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="d1f95-202">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="d1f95-203">Questo pacchetto contiene il set principale di interfacce per ASP.NET Core identità ed è incluso in `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="d1f95-203">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="d1f95-204">Migrazione a identità ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d1f95-204">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="d1f95-205">Per altre informazioni e indicazioni sulla migrazione dell'archivio identità esistente, vedere [eseguire la migrazione dell'autenticazione e dell'identità](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="d1f95-205">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="d1f95-206">Impostazione della complessità della password</span><span class="sxs-lookup"><span data-stu-id="d1f95-206">Setting password strength</span></span>

<span data-ttu-id="d1f95-207">Vedere [configurazione](#pw) per un esempio che consente di impostare i requisiti minimi per le password.</span><span class="sxs-lookup"><span data-stu-id="d1f95-207">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="d1f95-208">AddDefaultIdentity e AddIdentity</span><span class="sxs-lookup"><span data-stu-id="d1f95-208">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="d1f95-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>è stato introdotto in ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="d1f95-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="d1f95-210">La `AddDefaultIdentity` chiamata a è simile alla chiamata a quanto segue:</span><span class="sxs-lookup"><span data-stu-id="d1f95-210">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="d1f95-211">Per altre informazioni, vedere [origine AddDefaultIdentity](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="d1f95-211">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="d1f95-212">Impedisci la pubblicazione di asset di identità statici</span><span class="sxs-lookup"><span data-stu-id="d1f95-212">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="d1f95-213">Per evitare la pubblicazione di risorse di identità statiche (fogli di stile e file JavaScript per l'interfaccia utente dell'identità) nella `ResolveStaticWebAssetsInputsDependsOn` radice Web `RemoveIdentityAssets` , aggiungere la proprietà e la destinazione seguenti al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="d1f95-213">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="d1f95-214">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="d1f95-214">Next Steps</span></span>

* <span data-ttu-id="d1f95-215">Per informazioni sulla configurazione dell'identità con SQLite, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) .</span><span class="sxs-lookup"><span data-stu-id="d1f95-215">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="d1f95-216">Configurare Identity</span><span class="sxs-lookup"><span data-stu-id="d1f95-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d1f95-217">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d1f95-217">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d1f95-218">ASP.NET Core identità è un sistema di appartenenza che aggiunge la funzionalità di accesso alle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d1f95-218">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="d1f95-219">Gli utenti possono creare un account con le informazioni di accesso archiviate in Identity oppure possono usare un provider di accesso esterno.</span><span class="sxs-lookup"><span data-stu-id="d1f95-219">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="d1f95-220">I provider di accesso esterni supportati includono [Facebook, Google, account Microsoft e Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="d1f95-220">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="d1f95-221">L'identità può essere configurata utilizzando un database SQL Server per archiviare i nomi utente, le password e i dati di profilo.</span><span class="sxs-lookup"><span data-stu-id="d1f95-221">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="d1f95-222">In alternativa, è possibile usare un altro archivio permanente, ad esempio archiviazione tabelle di Azure.</span><span class="sxs-lookup"><span data-stu-id="d1f95-222">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="d1f95-223">Consente di [visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([come scaricare)](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="d1f95-223">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="d1f95-224">In questo argomento si apprenderà come usare l'identità per la registrazione, l'accesso e la disconnessione di un utente.</span><span class="sxs-lookup"><span data-stu-id="d1f95-224">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="d1f95-225">Per istruzioni più dettagliate sulla creazione di app che usano l'identità, vedere la sezione passaggi successivi alla fine di questo articolo.</span><span class="sxs-lookup"><span data-stu-id="d1f95-225">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="d1f95-226">AddDefaultIdentity e AddIdentity</span><span class="sxs-lookup"><span data-stu-id="d1f95-226">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="d1f95-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>è stato introdotto in ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="d1f95-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="d1f95-228">La `AddDefaultIdentity` chiamata a è simile alla chiamata a quanto segue:</span><span class="sxs-lookup"><span data-stu-id="d1f95-228">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="d1f95-229">Per altre informazioni, vedere [origine AddDefaultIdentity](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="d1f95-229">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="d1f95-230">Creare un'app Web con l'autenticazione</span><span class="sxs-lookup"><span data-stu-id="d1f95-230">Create a Web app with authentication</span></span>

<span data-ttu-id="d1f95-231">Creare un progetto di applicazione Web di ASP.NET Core con singoli account utente.</span><span class="sxs-lookup"><span data-stu-id="d1f95-231">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d1f95-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1f95-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d1f95-233">Selezionare **file** > **nuovo** > **progetto**.</span><span class="sxs-lookup"><span data-stu-id="d1f95-233">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="d1f95-234">Selezionare **Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="d1f95-234">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="d1f95-235">Denominare il progetto **app Web 1** in modo che abbia lo stesso spazio dei nomi del download del progetto.</span><span class="sxs-lookup"><span data-stu-id="d1f95-235">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="d1f95-236">Fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="d1f95-236">Click **OK**.</span></span>
* <span data-ttu-id="d1f95-237">Selezionare un' **applicazione Web**ASP.NET Core, quindi selezionare **Modifica autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="d1f95-237">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="d1f95-238">Selezionare **singoli account utente** e fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="d1f95-238">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d1f95-239">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="d1f95-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="d1f95-240">Il progetto generato fornisce [ASP.NET Core identità](xref:security/authentication/identity) come [libreria di classi Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="d1f95-240">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="d1f95-241">La libreria della classe Razor Identity espone gli endpoint con `Identity` l'area.</span><span class="sxs-lookup"><span data-stu-id="d1f95-241">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="d1f95-242">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="d1f95-242">For example:</span></span>

* <span data-ttu-id="d1f95-243">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="d1f95-243">/Identity/Account/Login</span></span>
* <span data-ttu-id="d1f95-244">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="d1f95-244">/Identity/Account/Logout</span></span>
* <span data-ttu-id="d1f95-245">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="d1f95-245">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="d1f95-246">Applicare le migrazioni</span><span class="sxs-lookup"><span data-stu-id="d1f95-246">Apply migrations</span></span>

<span data-ttu-id="d1f95-247">Applicare le migrazioni per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="d1f95-247">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d1f95-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1f95-248">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d1f95-249">Eseguire il comando seguente nella console di gestione pacchetti (PMC):</span><span class="sxs-lookup"><span data-stu-id="d1f95-249">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="d1f95-250">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="d1f95-250">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="d1f95-251">Registro di test e account di accesso</span><span class="sxs-lookup"><span data-stu-id="d1f95-251">Test Register and Login</span></span>

<span data-ttu-id="d1f95-252">Eseguire l'app e registrare un utente.</span><span class="sxs-lookup"><span data-stu-id="d1f95-252">Run the app and register a user.</span></span> <span data-ttu-id="d1f95-253">A seconda delle dimensioni dello schermo, potrebbe essere necessario selezionare l'interruttore di spostamento per visualizzare i collegamenti di **Registro** e di **accesso** .</span><span class="sxs-lookup"><span data-stu-id="d1f95-253">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="d1f95-254">Configurare i servizi di identità</span><span class="sxs-lookup"><span data-stu-id="d1f95-254">Configure Identity services</span></span>

<span data-ttu-id="d1f95-255">I servizi vengono aggiunti `ConfigureServices`in.</span><span class="sxs-lookup"><span data-stu-id="d1f95-255">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="d1f95-256">Il modello tipico consiste nel chiamare tutti i metodi `Add{Service}` e quindi chiamare tutti i metodi `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="d1f95-256">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="d1f95-257">Il codice precedente configura l'identità con i valori delle opzioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="d1f95-257">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="d1f95-258">I servizi vengono resi disponibili per l'applicazione tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d1f95-258">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="d1f95-259">L'identità viene abilitata chiamando [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="d1f95-259">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="d1f95-260">`UseAuthentication`aggiunge il [middleware](xref:fundamentals/middleware/index) di autenticazione alla pipeline della richiesta.</span><span class="sxs-lookup"><span data-stu-id="d1f95-260">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="d1f95-261">Per ulteriori informazioni, vedere la [Classe IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) e l' [avvio dell'applicazione](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="d1f95-261">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="d1f95-262">Registrazione, accesso e disconnessione del patibolo</span><span class="sxs-lookup"><span data-stu-id="d1f95-262">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="d1f95-263">Seguire l' [identità del patibolo in un progetto Razor con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) le istruzioni di autorizzazione per generare il codice illustrato in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="d1f95-263">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d1f95-264">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1f95-264">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d1f95-265">Aggiungere i file di registro, di accesso e di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="d1f95-265">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d1f95-266">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="d1f95-266">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d1f95-267">Se il progetto è stato creato con il nome **app Web 1**, eseguire i comandi seguenti.</span><span class="sxs-lookup"><span data-stu-id="d1f95-267">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="d1f95-268">In caso contrario, utilizzare lo spazio dei `ApplicationDbContext`nomi corretto per:</span><span class="sxs-lookup"><span data-stu-id="d1f95-268">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="d1f95-269">PowerShell usa il punto e virgola come separatore di comandi.</span><span class="sxs-lookup"><span data-stu-id="d1f95-269">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="d1f95-270">Quando si usa PowerShell, usare il carattere di escape per il punto e virgola nell'elenco dei file o inserire l'elenco di file tra virgolette doppie, come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="d1f95-270">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="d1f95-271">Esaminare il registro</span><span class="sxs-lookup"><span data-stu-id="d1f95-271">Examine Register</span></span>

<span data-ttu-id="d1f95-272">Quando un utente fa clic sul collegamento **Register** , `RegisterModel.OnPostAsync` viene richiamata l'azione.</span><span class="sxs-lookup"><span data-stu-id="d1f95-272">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="d1f95-273">L'utente viene creato da [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) sull' `_userManager` oggetto.</span><span class="sxs-lookup"><span data-stu-id="d1f95-273">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="d1f95-274">`_userManager`viene fornito dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="d1f95-274">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="d1f95-275">Se l'utente è stato creato correttamente, l'utente è connesso dalla chiamata a `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="d1f95-275">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="d1f95-276">**Nota:** Vedere la [conferma dell'account](xref:security/authentication/accconfirm#prevent-login-at-registration) per i passaggi per impedire l'accesso immediato alla registrazione.</span><span class="sxs-lookup"><span data-stu-id="d1f95-276">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="d1f95-277">Accesso</span><span class="sxs-lookup"><span data-stu-id="d1f95-277">Log in</span></span>

<span data-ttu-id="d1f95-278">Il modulo di accesso viene visualizzato quando:</span><span class="sxs-lookup"><span data-stu-id="d1f95-278">The Login form is displayed when:</span></span>

* <span data-ttu-id="d1f95-279">Il collegamento **Accedi** è selezionato.</span><span class="sxs-lookup"><span data-stu-id="d1f95-279">The **Log in** link is selected.</span></span>
* <span data-ttu-id="d1f95-280">Un utente tenta di accedere a una pagina con restrizioni che non è autorizzata ad accedere **o** quando non è stata autenticata dal sistema.</span><span class="sxs-lookup"><span data-stu-id="d1f95-280">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="d1f95-281">Quando viene inviato il modulo nella pagina di accesso, viene `OnPostAsync` chiamata l'azione.</span><span class="sxs-lookup"><span data-stu-id="d1f95-281">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="d1f95-282">`PasswordSignInAsync`viene chiamato sull' `_signInManager` oggetto, fornito dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="d1f95-282">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="d1f95-283">La classe `Controller` base espone una `User` proprietà a cui è possibile accedere dai metodi del controller.</span><span class="sxs-lookup"><span data-stu-id="d1f95-283">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="d1f95-284">È possibile, ad esempio, `User.Claims` enumerare e prendere decisioni di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="d1f95-284">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="d1f95-285">Per altre informazioni, vedere <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="d1f95-285">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="d1f95-286">Effettuare la disconnessione</span><span class="sxs-lookup"><span data-stu-id="d1f95-286">Log out</span></span>

<span data-ttu-id="d1f95-287">Il collegamento **Disconnetti** richiama l' `LogoutModel.OnPost` azione.</span><span class="sxs-lookup"><span data-stu-id="d1f95-287">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="d1f95-288">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) Cancella le attestazioni dell'utente archiviate in un cookie.</span><span class="sxs-lookup"><span data-stu-id="d1f95-288">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="d1f95-289">Post viene specificato nelle *pagine/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d1f95-289">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="d1f95-290">TestIdentity</span><span class="sxs-lookup"><span data-stu-id="d1f95-290">Test Identity</span></span>

<span data-ttu-id="d1f95-291">I modelli di progetto Web predefiniti consentono l'accesso anonimo alle Home page.</span><span class="sxs-lookup"><span data-stu-id="d1f95-291">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="d1f95-292">Per eseguire Identityil test [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) , aggiungere alla pagina privacy.</span><span class="sxs-lookup"><span data-stu-id="d1f95-292">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="d1f95-293">Se è stato eseguito l'accesso, disconnettersi. Eseguire l'app e selezionare il collegamento per la **privacy** .</span><span class="sxs-lookup"><span data-stu-id="d1f95-293">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="d1f95-294">Si verrà reindirizzati alla pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="d1f95-294">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="d1f95-295">EsplorareIdentity</span><span class="sxs-lookup"><span data-stu-id="d1f95-295">Explore Identity</span></span>

<span data-ttu-id="d1f95-296">Per esplorare Identity in modo più dettagliato:</span><span class="sxs-lookup"><span data-stu-id="d1f95-296">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="d1f95-297">Crea origine interfaccia utente con identità completa</span><span class="sxs-lookup"><span data-stu-id="d1f95-297">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="d1f95-298">Esaminare l'origine di ogni pagina ed eseguire un'istruzione alla volta nel debugger.</span><span class="sxs-lookup"><span data-stu-id="d1f95-298">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="d1f95-299">Componenti</span><span class="sxs-lookup"><span data-stu-id="d1f95-299"> Components</span></span>

<span data-ttu-id="d1f95-300">Tutti i Identity pacchetti NuGet dipendenti sono inclusi nel [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="d1f95-300">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="d1f95-301">Il pacchetto primario per Identity è [Microsoft. AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="d1f95-301">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="d1f95-302">Questo pacchetto contiene il set principale di interfacce per ASP.NET Core Identityed è incluso in `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="d1f95-302">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="d1f95-303">Migrazione a ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="d1f95-303">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="d1f95-304">Per ulteriori informazioni e istruzioni sulla migrazione dell'archivio Identity esistente, vedere [eseguire la migrazione Identitydell'autenticazione e ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="d1f95-304">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="d1f95-305">Impostazione della complessità della password</span><span class="sxs-lookup"><span data-stu-id="d1f95-305">Setting password strength</span></span>

<span data-ttu-id="d1f95-306">Vedere [configurazione](#pw) per un esempio che consente di impostare i requisiti minimi per le password.</span><span class="sxs-lookup"><span data-stu-id="d1f95-306">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d1f95-307">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="d1f95-307">Next Steps</span></span>

* <span data-ttu-id="d1f95-308">Per informazioni sulla configurazione Identity di using SQLite, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) .</span><span class="sxs-lookup"><span data-stu-id="d1f95-308">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="d1f95-309">[ConfigurareIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="d1f95-309">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
