---
title: 'Introduzione a :::no-loc(Identity)::: on ASP.NET Core'
author: rick-anderson
description: "Usare :::no-loc(Identity)::: con un'app ASP.NET Core. Informazioni su come impostare i requisiti per le password (RequireDigit, RequiredLength, RequiredUniqueChars e altro)."
ms.author: riande
ms.date: 7/15/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/identity
ms.openlocfilehash: bfcef860beb07ab81dda1a10a1648491ae187bef
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052019"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a><span data-ttu-id="18b9e-104">Introduzione a :::no-loc(Identity)::: on ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18b9e-104">Introduction to :::no-loc(Identity)::: on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="18b9e-105">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="18b9e-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="18b9e-106">:::no-loc(ASP.NET Core Identity)::::</span><span class="sxs-lookup"><span data-stu-id="18b9e-106">:::no-loc(ASP.NET Core Identity)::::</span></span>

* <span data-ttu-id="18b9e-107">È un'API che supporta la funzionalità di accesso dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="18b9e-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="18b9e-108">Gestisce utenti, password, dati di profilo, ruoli, attestazioni, token, conferma tramite posta elettronica e altro ancora.</span><span class="sxs-lookup"><span data-stu-id="18b9e-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="18b9e-109">Gli utenti possono creare un account con le informazioni di accesso archiviate in :::no-loc(Identity)::: o possono usare un provider di accesso esterno.</span><span class="sxs-lookup"><span data-stu-id="18b9e-109">Users can create an account with the login information stored in :::no-loc(Identity)::: or they can use an external login provider.</span></span> <span data-ttu-id="18b9e-110">I provider di accesso esterni supportati includono [Facebook, Google, account Microsoft e Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="18b9e-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="18b9e-111">Il [ :::no-loc(Identity)::: codice sorgente](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(Identity):::) è disponibile in GitHub.</span><span class="sxs-lookup"><span data-stu-id="18b9e-111">The [:::no-loc(Identity)::: source code](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(Identity):::) is available on GitHub.</span></span> <span data-ttu-id="18b9e-112">[Impalcatura :::no-loc(Identity)::: ](xref:security/authentication/scaffold-identity) e visualizzare i file generati per esaminare l'interazione del modello con :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="18b9e-112">[Scaffold :::no-loc(Identity):::](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with :::no-loc(Identity):::.</span></span>

<span data-ttu-id="18b9e-113">:::no-loc(Identity)::: viene in genere configurato utilizzando un database di SQL Server per archiviare i nomi utente, le password e i dati di profilo.</span><span class="sxs-lookup"><span data-stu-id="18b9e-113">:::no-loc(Identity)::: is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="18b9e-114">In alternativa, è possibile usare un altro archivio permanente, ad esempio archiviazione tabelle di Azure.</span><span class="sxs-lookup"><span data-stu-id="18b9e-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="18b9e-115">In questo argomento si apprenderà come usare :::no-loc(Identity)::: per registrare, accedere e disconnettere un utente.</span><span class="sxs-lookup"><span data-stu-id="18b9e-115">In this topic, you learn how to use :::no-loc(Identity)::: to register, log in, and log out a user.</span></span> <span data-ttu-id="18b9e-116">Nota: i modelli considerano il nome utente e il messaggio di posta elettronica come lo stesso per gli utenti.</span><span class="sxs-lookup"><span data-stu-id="18b9e-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="18b9e-117">Per istruzioni più dettagliate sulla creazione di app che usano :::no-loc(Identity)::: , vedere [passaggi successivi](#next).</span><span class="sxs-lookup"><span data-stu-id="18b9e-117">For more detailed instructions about creating apps that use :::no-loc(Identity):::, see [Next Steps](#next).</span></span>

<span data-ttu-id="18b9e-118">[Piattaforma di identità Microsoft](/azure/active-directory/develop/) :</span><span class="sxs-lookup"><span data-stu-id="18b9e-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="18b9e-119">Evoluzione della piattaforma per sviluppatori Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="18b9e-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="18b9e-120">Non correlato a :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="18b9e-120">Unrelated to :::no-loc(ASP.NET Core Identity):::.</span></span>

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

<span data-ttu-id="18b9e-121">Consente di [visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([come scaricare](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="18b9e-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="18b9e-122">Creare un'app Web con l'autenticazione</span><span class="sxs-lookup"><span data-stu-id="18b9e-122">Create a Web app with authentication</span></span>

<span data-ttu-id="18b9e-123">Creare un progetto di applicazione Web di ASP.NET Core con singoli account utente.</span><span class="sxs-lookup"><span data-stu-id="18b9e-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="18b9e-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="18b9e-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="18b9e-125">Selezionare **file** > **nuovo** > **progetto** .</span><span class="sxs-lookup"><span data-stu-id="18b9e-125">Select **File** > **New** > **Project** .</span></span>
* <span data-ttu-id="18b9e-126">Selezionare **Applicazione Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="18b9e-126">Select **ASP.NET Core Web Application** .</span></span> <span data-ttu-id="18b9e-127">Denominare il progetto **app Web 1** in modo che abbia lo stesso spazio dei nomi del download del progetto.</span><span class="sxs-lookup"><span data-stu-id="18b9e-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="18b9e-128">Fare clic su **OK** .</span><span class="sxs-lookup"><span data-stu-id="18b9e-128">Click **OK** .</span></span>
* <span data-ttu-id="18b9e-129">Selezionare un' **applicazione Web** ASP.NET Core, quindi selezionare **Modifica autenticazione** .</span><span class="sxs-lookup"><span data-stu-id="18b9e-129">Select an ASP.NET Core **Web Application** , then select **Change Authentication** .</span></span>
* <span data-ttu-id="18b9e-130">Selezionare **singoli account utente** e fare clic su **OK** .</span><span class="sxs-lookup"><span data-stu-id="18b9e-130">Select **Individual User Accounts** and click **OK** .</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="18b9e-131">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="18b9e-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="18b9e-132">Il comando precedente crea un' :::no-loc(Razor)::: app Web con SQLite.</span><span class="sxs-lookup"><span data-stu-id="18b9e-132">The preceding command creates a :::no-loc(Razor)::: web app using SQLite.</span></span> <span data-ttu-id="18b9e-133">Per creare l'app Web con il database locale, eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="18b9e-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="18b9e-134">Il progetto generato fornisce [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) una [ :::no-loc(Razor)::: libreria di classi](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="18b9e-134">The generated project provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="18b9e-135">La :::no-loc(Identity)::: :::no-loc(Razor)::: libreria di classi espone gli endpoint con l' `:::no-loc(Identity):::` area.</span><span class="sxs-lookup"><span data-stu-id="18b9e-135">The :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library exposes endpoints with the `:::no-loc(Identity):::` area.</span></span> <span data-ttu-id="18b9e-136">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="18b9e-136">For example:</span></span>

* <span data-ttu-id="18b9e-137">/:::no-loc(Identity):::/Account/Login</span><span class="sxs-lookup"><span data-stu-id="18b9e-137">/:::no-loc(Identity):::/Account/Login</span></span>
* <span data-ttu-id="18b9e-138">/:::no-loc(Identity):::/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="18b9e-138">/:::no-loc(Identity):::/Account/Logout</span></span>
* <span data-ttu-id="18b9e-139">/:::no-loc(Identity):::/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="18b9e-139">/:::no-loc(Identity):::/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="18b9e-140">Applicare le migrazioni</span><span class="sxs-lookup"><span data-stu-id="18b9e-140">Apply migrations</span></span>

<span data-ttu-id="18b9e-141">Applicare le migrazioni per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="18b9e-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="18b9e-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="18b9e-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="18b9e-143">Eseguire il comando seguente nella console di gestione pacchetti (PMC):</span><span class="sxs-lookup"><span data-stu-id="18b9e-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="18b9e-144">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="18b9e-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="18b9e-145">Le migrazioni non sono necessarie in questo passaggio quando si usa SQLite.</span><span class="sxs-lookup"><span data-stu-id="18b9e-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="18b9e-146">Per il database locale, eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="18b9e-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="18b9e-147">Registro di test e account di accesso</span><span class="sxs-lookup"><span data-stu-id="18b9e-147">Test Register and Login</span></span>

<span data-ttu-id="18b9e-148">Eseguire l'app e registrare un utente.</span><span class="sxs-lookup"><span data-stu-id="18b9e-148">Run the app and register a user.</span></span> <span data-ttu-id="18b9e-149">A seconda delle dimensioni dello schermo, potrebbe essere necessario selezionare l'interruttore di spostamento per visualizzare i collegamenti di **Registro** e di **accesso** .</span><span class="sxs-lookup"><span data-stu-id="18b9e-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="18b9e-150">Configurare i :::no-loc(Identity)::: Servizi</span><span class="sxs-lookup"><span data-stu-id="18b9e-150">Configure :::no-loc(Identity)::: services</span></span>

<span data-ttu-id="18b9e-151">I servizi vengono aggiunti in `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="18b9e-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="18b9e-152">Il modello tipico consiste nel chiamare tutti i metodi `Add{Service}` e quindi chiamare tutti i metodi `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="18b9e-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="18b9e-153">Il codice evidenziato precedente viene configurato :::no-loc(Identity)::: con i valori di opzione predefiniti.</span><span class="sxs-lookup"><span data-stu-id="18b9e-153">The preceding highlighted code configures :::no-loc(Identity)::: with default option values.</span></span> <span data-ttu-id="18b9e-154">I servizi vengono resi disponibili per l'applicazione tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="18b9e-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="18b9e-155">:::no-loc(Identity)::: viene abilitato chiamando <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="18b9e-155">:::no-loc(Identity)::: is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="18b9e-156">`UseAuthentication` aggiunge il [middleware](xref:fundamentals/middleware/index) di autenticazione alla pipeline della richiesta.</span><span class="sxs-lookup"><span data-stu-id="18b9e-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="18b9e-157">L'app generata dal modello non usa l' [autorizzazione](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="18b9e-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="18b9e-158">`app.UseAuthorization` è incluso per assicurarsi che venga aggiunto nell'ordine corretto se l'app aggiunge l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="18b9e-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="18b9e-159">`UseRouting`, `UseAuthentication` , `UseAuthorization` e `UseEndpoints` devono essere chiamati nell'ordine indicato nel codice precedente.</span><span class="sxs-lookup"><span data-stu-id="18b9e-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="18b9e-160">Per ulteriori informazioni su `:::no-loc(Identity):::Options` e `Startup` , vedere <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Options> e [avvio dell'applicazione](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="18b9e-160">For more information on `:::no-loc(Identity):::Options` and `Startup`, see <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Options> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="18b9e-161">Registro di ponteggi, account di accesso, disconnessione e RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="18b9e-161">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="18b9e-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="18b9e-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="18b9e-163">Aggiungere i `Register` `Login` file,, `LogOut` e `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="18b9e-163">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="18b9e-164">Seguire l' [identità del patibolo in un :::no-loc(Razor)::: progetto con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) le istruzioni di autorizzazione per generare il codice illustrato in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="18b9e-164">Follow the [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="18b9e-165">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="18b9e-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="18b9e-166">Se il progetto è stato creato con il nome **app Web 1** , eseguire i comandi seguenti.</span><span class="sxs-lookup"><span data-stu-id="18b9e-166">If you created the project with name **WebApp1** , run the following commands.</span></span> <span data-ttu-id="18b9e-167">In caso contrario, utilizzare lo spazio dei nomi corretto per `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="18b9e-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="18b9e-168">PowerShell usa il punto e virgola come separatore di comandi.</span><span class="sxs-lookup"><span data-stu-id="18b9e-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="18b9e-169">Quando si usa PowerShell, usare il carattere di escape per il punto e virgola nell'elenco dei file o inserire l'elenco di file tra virgolette doppie, come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="18b9e-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="18b9e-170">Per ulteriori informazioni sull'impalcatura :::no-loc(Identity)::: , vedere la pagina relativa all' [identità del patibolo in un :::no-loc(Razor)::: progetto con autorizzazione](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="18b9e-170">For more information on scaffolding :::no-loc(Identity):::, see [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="18b9e-171">Esaminare il registro</span><span class="sxs-lookup"><span data-stu-id="18b9e-171">Examine Register</span></span>

<span data-ttu-id="18b9e-172">Quando un utente fa clic sul pulsante **registra** nella `Register` pagina, `RegisterModel.OnPostAsync` viene richiamata l'azione.</span><span class="sxs-lookup"><span data-stu-id="18b9e-172">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="18b9e-173">L'utente viene creato da [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) nell' `_userManager` oggetto:</span><span class="sxs-lookup"><span data-stu-id="18b9e-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/:::no-loc(Identity):::/UI/src/Areas/:::no-loc(Identity):::/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="18b9e-174">Accesso</span><span class="sxs-lookup"><span data-stu-id="18b9e-174">Log in</span></span>

<span data-ttu-id="18b9e-175">Il modulo di accesso viene visualizzato quando:</span><span class="sxs-lookup"><span data-stu-id="18b9e-175">The Login form is displayed when:</span></span>

* <span data-ttu-id="18b9e-176">Il collegamento **Accedi** è selezionato.</span><span class="sxs-lookup"><span data-stu-id="18b9e-176">The **Log in** link is selected.</span></span>
* <span data-ttu-id="18b9e-177">Un utente tenta di accedere a una pagina con restrizioni che non è autorizzata ad accedere **o** quando non è stata autenticata dal sistema.</span><span class="sxs-lookup"><span data-stu-id="18b9e-177">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="18b9e-178">Quando viene inviato il modulo nella pagina di accesso, `OnPostAsync` viene chiamata l'azione.</span><span class="sxs-lookup"><span data-stu-id="18b9e-178">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="18b9e-179">`PasswordSignInAsync` viene chiamato sull' `_signInManager` oggetto.</span><span class="sxs-lookup"><span data-stu-id="18b9e-179">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="18b9e-180">Per informazioni su come prendere decisioni relative alle autorizzazioni, vedere <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="18b9e-180">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="18b9e-181">Effettuare la disconnessione</span><span class="sxs-lookup"><span data-stu-id="18b9e-181">Log out</span></span>

<span data-ttu-id="18b9e-182">Il collegamento **Disconnetti** richiama l' `LogoutModel.OnPost` azione.</span><span class="sxs-lookup"><span data-stu-id="18b9e-182">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="18b9e-183">Nel codice precedente, il codice `return RedirectToPage();` deve essere un reindirizzamento in modo che il browser esegua una nuova richiesta e venga aggiornata l'identità dell'utente.</span><span class="sxs-lookup"><span data-stu-id="18b9e-183">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="18b9e-184">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) Cancella le attestazioni dell'utente archiviate in un oggetto :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="18b9e-184">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) clears the user's claims stored in a :::no-loc(cookie):::.</span></span>

<span data-ttu-id="18b9e-185">Post viene specificato nelle *pagine/Shared/_LoginPartial. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="18b9e-185">Post is specified in the *Pages/Shared/_LoginPartial.cshtml* :</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a><span data-ttu-id="18b9e-186">Test :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="18b9e-186">Test :::no-loc(Identity):::</span></span>

<span data-ttu-id="18b9e-187">I modelli di progetto Web predefiniti consentono l'accesso anonimo alle Home page.</span><span class="sxs-lookup"><span data-stu-id="18b9e-187">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="18b9e-188">Per eseguire il test :::no-loc(Identity)::: , aggiungere [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="18b9e-188">To test :::no-loc(Identity):::, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="18b9e-189">Se è stato eseguito l'accesso, disconnettersi. Eseguire l'app e selezionare il collegamento per la **privacy** .</span><span class="sxs-lookup"><span data-stu-id="18b9e-189">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="18b9e-190">Si verrà reindirizzati alla pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="18b9e-190">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="18b9e-191">Esplorare :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="18b9e-191">Explore :::no-loc(Identity):::</span></span>

<span data-ttu-id="18b9e-192">Per esplorare :::no-loc(Identity)::: in modo più dettagliato:</span><span class="sxs-lookup"><span data-stu-id="18b9e-192">To explore :::no-loc(Identity)::: in more detail:</span></span>

* [<span data-ttu-id="18b9e-193">Crea origine interfaccia utente con identità completa</span><span class="sxs-lookup"><span data-stu-id="18b9e-193">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="18b9e-194">Esaminare l'origine di ogni pagina ed eseguire un'istruzione alla volta nel debugger.</span><span class="sxs-lookup"><span data-stu-id="18b9e-194">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="18b9e-195">:::no-loc(Identity)::: Componenti</span><span class="sxs-lookup"><span data-stu-id="18b9e-195">:::no-loc(Identity)::: Components</span></span>

<span data-ttu-id="18b9e-196">Tutti i :::no-loc(Identity)::: pacchetti NuGet dipendenti da sono inclusi nel [framework condiviso ASP.NET Core](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="18b9e-196">All the :::no-loc(Identity):::-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="18b9e-197">Il pacchetto primario per :::no-loc(Identity)::: è [Microsoft. AspNetCore. :::no-loc(Identity)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/)</span><span class="sxs-lookup"><span data-stu-id="18b9e-197">The primary package for :::no-loc(Identity)::: is [Microsoft.AspNetCore.:::no-loc(Identity):::](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).</span></span> <span data-ttu-id="18b9e-198">Questo pacchetto contiene il set principale di interfacce per :::no-loc(ASP.NET Core Identity)::: ed è incluso in `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="18b9e-198">This package contains the core set of interfaces for :::no-loc(ASP.NET Core Identity):::, and is included by `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-no-locaspnet-core-identity"></a><span data-ttu-id="18b9e-199">Migrazione a :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="18b9e-199">Migrating to :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="18b9e-200">Per ulteriori informazioni e istruzioni sulla migrazione dell' :::no-loc(Identity)::: archivio esistente, vedere [eseguire la migrazione :::no-loc(Identity)::: dell'autenticazione e ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="18b9e-200">For more information and guidance on migrating your existing :::no-loc(Identity)::: store, see [Migrate Authentication and :::no-loc(Identity):::](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="18b9e-201">Impostazione della complessità della password</span><span class="sxs-lookup"><span data-stu-id="18b9e-201">Setting password strength</span></span>

<span data-ttu-id="18b9e-202">Vedere [configurazione](#pw) per un esempio che consente di impostare i requisiti minimi per le password.</span><span class="sxs-lookup"><span data-stu-id="18b9e-202">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="18b9e-203">AddDefault :::no-loc(Identity)::: e Aggiungi:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="18b9e-203">AddDefault:::no-loc(Identity)::: and Add:::no-loc(Identity):::</span></span>

<span data-ttu-id="18b9e-204"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*> è stato introdotto in ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="18b9e-204"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="18b9e-205">`AddDefault:::no-loc(Identity):::`La chiamata a è simile alla chiamata a quanto segue:</span><span class="sxs-lookup"><span data-stu-id="18b9e-205">Calling `AddDefault:::no-loc(Identity):::` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="18b9e-206">Per altre informazioni, vedere [ :::no-loc(Identity)::: origine AddDefault](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="18b9e-206">See [AddDefault:::no-loc(Identity)::: source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="18b9e-207">Impedisci la pubblicazione di :::no-loc(Identity)::: Asset statici</span><span class="sxs-lookup"><span data-stu-id="18b9e-207">Prevent publish of static :::no-loc(Identity)::: assets</span></span>

<span data-ttu-id="18b9e-208">Per evitare la pubblicazione :::no-loc(Identity)::: di risorse statiche (fogli di stile e file JavaScript per :::no-loc(Identity)::: l'interfaccia utente) nella radice Web, aggiungere la `ResolveStaticWebAssetsInputsDependsOn` proprietà e la `Remove:::no-loc(Identity):::Assets` destinazione seguenti al file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="18b9e-208">To prevent publishing static :::no-loc(Identity)::: assets (stylesheets and JavaScript files for :::no-loc(Identity)::: UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `Remove:::no-loc(Identity):::Assets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>Remove:::no-loc(Identity):::Assets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="Remove:::no-loc(Identity):::Assets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.:::no-loc(Identity):::.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a><span data-ttu-id="18b9e-209">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="18b9e-209">Next Steps</span></span>

* <span data-ttu-id="18b9e-210">[:::no-loc(ASP.NET Core Identity)::: codice sorgente](https://github.com/dotnet/aspnetcore/tree/master/src/:::no-loc(Identity):::)</span><span class="sxs-lookup"><span data-stu-id="18b9e-210">[:::no-loc(ASP.NET Core Identity)::: source code](https://github.com/dotnet/aspnetcore/tree/master/src/:::no-loc(Identity):::)</span></span>
* <span data-ttu-id="18b9e-211">Per informazioni sulla configurazione di using SQLite, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="18b9e-211">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring :::no-loc(Identity)::: using SQLite.</span></span>
* [<span data-ttu-id="18b9e-212">Configurare :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="18b9e-212">Configure :::no-loc(Identity):::</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="18b9e-213">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="18b9e-213">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="18b9e-214">:::no-loc(ASP.NET Core Identity)::: è un sistema di appartenenza che aggiunge la funzionalità di accesso alle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="18b9e-214">:::no-loc(ASP.NET Core Identity)::: is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="18b9e-215">Gli utenti possono creare un account con le informazioni di accesso archiviate in :::no-loc(Identity)::: o possono usare un provider di accesso esterno.</span><span class="sxs-lookup"><span data-stu-id="18b9e-215">Users can create an account with the login information stored in :::no-loc(Identity)::: or they can use an external login provider.</span></span> <span data-ttu-id="18b9e-216">I provider di accesso esterni supportati includono [Facebook, Google, account Microsoft e Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="18b9e-216">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="18b9e-217">:::no-loc(Identity)::: può essere configurato usando un database di SQL Server per archiviare i nomi utente, le password e i dati di profilo.</span><span class="sxs-lookup"><span data-stu-id="18b9e-217">:::no-loc(Identity)::: can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="18b9e-218">In alternativa, è possibile usare un altro archivio permanente, ad esempio archiviazione tabelle di Azure.</span><span class="sxs-lookup"><span data-stu-id="18b9e-218">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="18b9e-219">Consente di [visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-:::no-loc(Identity):::DemoComplete/) ([come scaricare](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="18b9e-219">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-:::no-loc(Identity):::DemoComplete/) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="18b9e-220">In questo argomento si apprenderà come usare :::no-loc(Identity)::: per registrare, accedere e disconnettere un utente.</span><span class="sxs-lookup"><span data-stu-id="18b9e-220">In this topic, you learn how to use :::no-loc(Identity)::: to register, log in, and log out a user.</span></span> <span data-ttu-id="18b9e-221">Per istruzioni più dettagliate sulla creazione di app che usano :::no-loc(Identity)::: , vedere la sezione passaggi successivi alla fine di questo articolo.</span><span class="sxs-lookup"><span data-stu-id="18b9e-221">For more detailed instructions about creating apps that use :::no-loc(Identity):::, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="18b9e-222">AddDefault :::no-loc(Identity)::: e Aggiungi:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="18b9e-222">AddDefault:::no-loc(Identity)::: and Add:::no-loc(Identity):::</span></span>

<span data-ttu-id="18b9e-223"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*> è stato introdotto in ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="18b9e-223"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="18b9e-224">`AddDefault:::no-loc(Identity):::`La chiamata a è simile alla chiamata a quanto segue:</span><span class="sxs-lookup"><span data-stu-id="18b9e-224">Calling `AddDefault:::no-loc(Identity):::` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="18b9e-225">Per altre informazioni, vedere [ :::no-loc(Identity)::: origine AddDefault](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="18b9e-225">See [AddDefault:::no-loc(Identity)::: source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="18b9e-226">Creare un'app Web con l'autenticazione</span><span class="sxs-lookup"><span data-stu-id="18b9e-226">Create a Web app with authentication</span></span>

<span data-ttu-id="18b9e-227">Creare un progetto di applicazione Web di ASP.NET Core con singoli account utente.</span><span class="sxs-lookup"><span data-stu-id="18b9e-227">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="18b9e-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="18b9e-228">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="18b9e-229">Selezionare **file** > **nuovo** > **progetto** .</span><span class="sxs-lookup"><span data-stu-id="18b9e-229">Select **File** > **New** > **Project** .</span></span>
* <span data-ttu-id="18b9e-230">Selezionare **Applicazione Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="18b9e-230">Select **ASP.NET Core Web Application** .</span></span> <span data-ttu-id="18b9e-231">Denominare il progetto **app Web 1** in modo che abbia lo stesso spazio dei nomi del download del progetto.</span><span class="sxs-lookup"><span data-stu-id="18b9e-231">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="18b9e-232">Fare clic su **OK** .</span><span class="sxs-lookup"><span data-stu-id="18b9e-232">Click **OK** .</span></span>
* <span data-ttu-id="18b9e-233">Selezionare un' **applicazione Web** ASP.NET Core, quindi selezionare **Modifica autenticazione** .</span><span class="sxs-lookup"><span data-stu-id="18b9e-233">Select an ASP.NET Core **Web Application** , then select **Change Authentication** .</span></span>
* <span data-ttu-id="18b9e-234">Selezionare **singoli account utente** e fare clic su **OK** .</span><span class="sxs-lookup"><span data-stu-id="18b9e-234">Select **Individual User Accounts** and click **OK** .</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="18b9e-235">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="18b9e-235">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="18b9e-236">Il progetto generato fornisce [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) una [ :::no-loc(Razor)::: libreria di classi](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="18b9e-236">The generated project provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="18b9e-237">La :::no-loc(Identity)::: :::no-loc(Razor)::: libreria di classi espone gli endpoint con l' `:::no-loc(Identity):::` area.</span><span class="sxs-lookup"><span data-stu-id="18b9e-237">The :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library exposes endpoints with the `:::no-loc(Identity):::` area.</span></span> <span data-ttu-id="18b9e-238">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="18b9e-238">For example:</span></span>

* <span data-ttu-id="18b9e-239">/:::no-loc(Identity):::/Account/Login</span><span class="sxs-lookup"><span data-stu-id="18b9e-239">/:::no-loc(Identity):::/Account/Login</span></span>
* <span data-ttu-id="18b9e-240">/:::no-loc(Identity):::/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="18b9e-240">/:::no-loc(Identity):::/Account/Logout</span></span>
* <span data-ttu-id="18b9e-241">/:::no-loc(Identity):::/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="18b9e-241">/:::no-loc(Identity):::/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="18b9e-242">Applicare le migrazioni</span><span class="sxs-lookup"><span data-stu-id="18b9e-242">Apply migrations</span></span>

<span data-ttu-id="18b9e-243">Applicare le migrazioni per inizializzare il database.</span><span class="sxs-lookup"><span data-stu-id="18b9e-243">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="18b9e-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="18b9e-244">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="18b9e-245">Eseguire il comando seguente nella console di gestione pacchetti (PMC):</span><span class="sxs-lookup"><span data-stu-id="18b9e-245">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="18b9e-246">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="18b9e-246">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="18b9e-247">Registro di test e account di accesso</span><span class="sxs-lookup"><span data-stu-id="18b9e-247">Test Register and Login</span></span>

<span data-ttu-id="18b9e-248">Eseguire l'app e registrare un utente.</span><span class="sxs-lookup"><span data-stu-id="18b9e-248">Run the app and register a user.</span></span> <span data-ttu-id="18b9e-249">A seconda delle dimensioni dello schermo, potrebbe essere necessario selezionare l'interruttore di spostamento per visualizzare i collegamenti di **Registro** e di **accesso** .</span><span class="sxs-lookup"><span data-stu-id="18b9e-249">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="18b9e-250">Configurare i :::no-loc(Identity)::: Servizi</span><span class="sxs-lookup"><span data-stu-id="18b9e-250">Configure :::no-loc(Identity)::: services</span></span>

<span data-ttu-id="18b9e-251">I servizi vengono aggiunti in `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="18b9e-251">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="18b9e-252">Il modello tipico consiste nel chiamare tutti i metodi `Add{Service}` e quindi chiamare tutti i metodi `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="18b9e-252">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="18b9e-253">Il codice precedente Configura :::no-loc(Identity)::: con i valori di opzione predefiniti.</span><span class="sxs-lookup"><span data-stu-id="18b9e-253">The preceding code configures :::no-loc(Identity)::: with default option values.</span></span> <span data-ttu-id="18b9e-254">I servizi vengono resi disponibili per l'applicazione tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="18b9e-254">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="18b9e-255">:::no-loc(Identity)::: viene abilitato chiamando [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="18b9e-255">:::no-loc(Identity)::: is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="18b9e-256">`UseAuthentication` aggiunge il [middleware](xref:fundamentals/middleware/index) di autenticazione alla pipeline della richiesta.</span><span class="sxs-lookup"><span data-stu-id="18b9e-256">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="18b9e-257">Per ulteriori informazioni, vedere la [ :::no-loc(Identity)::: classe Options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) e l' [avvio dell'applicazione](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="18b9e-257">For more information, see the [:::no-loc(Identity):::Options Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="18b9e-258">Registrazione, accesso e disconnessione del patibolo</span><span class="sxs-lookup"><span data-stu-id="18b9e-258">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="18b9e-259">Seguire l' [identità del patibolo in un :::no-loc(Razor)::: progetto con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) le istruzioni di autorizzazione per generare il codice illustrato in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="18b9e-259">Follow the [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="18b9e-260">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="18b9e-260">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="18b9e-261">Aggiungere i file di registro, di accesso e di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="18b9e-261">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="18b9e-262">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="18b9e-262">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="18b9e-263">Se il progetto è stato creato con il nome **app Web 1** , eseguire i comandi seguenti.</span><span class="sxs-lookup"><span data-stu-id="18b9e-263">If you created the project with name **WebApp1** , run the following commands.</span></span> <span data-ttu-id="18b9e-264">In caso contrario, utilizzare lo spazio dei nomi corretto per `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="18b9e-264">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="18b9e-265">PowerShell usa il punto e virgola come separatore di comandi.</span><span class="sxs-lookup"><span data-stu-id="18b9e-265">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="18b9e-266">Quando si usa PowerShell, usare il carattere di escape per il punto e virgola nell'elenco dei file o inserire l'elenco di file tra virgolette doppie, come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="18b9e-266">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="18b9e-267">Esaminare il registro</span><span class="sxs-lookup"><span data-stu-id="18b9e-267">Examine Register</span></span>

<span data-ttu-id="18b9e-268">Quando un utente fa clic sul collegamento **Register** , `RegisterModel.OnPostAsync` viene richiamata l'azione.</span><span class="sxs-lookup"><span data-stu-id="18b9e-268">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="18b9e-269">L'utente viene creato da [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) nell' `_userManager` oggetto:</span><span class="sxs-lookup"><span data-stu-id="18b9e-269">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="18b9e-270">Se l'utente è stato creato correttamente, l'utente è connesso dalla chiamata a `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="18b9e-270">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="18b9e-271">**Nota:** Vedere la [conferma dell'account](xref:security/authentication/accconfirm#prevent-login-at-registration) per i passaggi per impedire l'accesso immediato alla registrazione.</span><span class="sxs-lookup"><span data-stu-id="18b9e-271">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="18b9e-272">Accesso</span><span class="sxs-lookup"><span data-stu-id="18b9e-272">Log in</span></span>

<span data-ttu-id="18b9e-273">Il modulo di accesso viene visualizzato quando:</span><span class="sxs-lookup"><span data-stu-id="18b9e-273">The Login form is displayed when:</span></span>

* <span data-ttu-id="18b9e-274">Il collegamento **Accedi** è selezionato.</span><span class="sxs-lookup"><span data-stu-id="18b9e-274">The **Log in** link is selected.</span></span>
* <span data-ttu-id="18b9e-275">Un utente tenta di accedere a una pagina con restrizioni che non è autorizzata ad accedere **o** quando non è stata autenticata dal sistema.</span><span class="sxs-lookup"><span data-stu-id="18b9e-275">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="18b9e-276">Quando viene inviato il modulo nella pagina di accesso, `OnPostAsync` viene chiamata l'azione.</span><span class="sxs-lookup"><span data-stu-id="18b9e-276">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="18b9e-277">`PasswordSignInAsync` viene chiamato sull' `_signInManager` oggetto.</span><span class="sxs-lookup"><span data-stu-id="18b9e-277">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="18b9e-278">Per informazioni su come prendere decisioni relative alle autorizzazioni, vedere <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="18b9e-278">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="18b9e-279">Effettuare la disconnessione</span><span class="sxs-lookup"><span data-stu-id="18b9e-279">Log out</span></span>

<span data-ttu-id="18b9e-280">Il collegamento **Disconnetti** richiama l' `LogoutModel.OnPost` azione.</span><span class="sxs-lookup"><span data-stu-id="18b9e-280">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="18b9e-281">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) Cancella le attestazioni dell'utente archiviate in un oggetto :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="18b9e-281">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) clears the user's claims stored in a :::no-loc(cookie):::.</span></span>

<span data-ttu-id="18b9e-282">Post viene specificato nelle *pagine/Shared/_LoginPartial. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="18b9e-282">Post is specified in the *Pages/Shared/_LoginPartial.cshtml* :</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a><span data-ttu-id="18b9e-283">Test :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="18b9e-283">Test :::no-loc(Identity):::</span></span>

<span data-ttu-id="18b9e-284">I modelli di progetto Web predefiniti consentono l'accesso anonimo alle Home page.</span><span class="sxs-lookup"><span data-stu-id="18b9e-284">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="18b9e-285">Per eseguire :::no-loc(Identity)::: il test, aggiungere [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) alla pagina privacy.</span><span class="sxs-lookup"><span data-stu-id="18b9e-285">To test :::no-loc(Identity):::, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="18b9e-286">Se è stato eseguito l'accesso, disconnettersi. Eseguire l'app e selezionare il collegamento per la **privacy** .</span><span class="sxs-lookup"><span data-stu-id="18b9e-286">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="18b9e-287">Si verrà reindirizzati alla pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="18b9e-287">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="18b9e-288">Esplorare :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="18b9e-288">Explore :::no-loc(Identity):::</span></span>

<span data-ttu-id="18b9e-289">Per esplorare :::no-loc(Identity)::: in modo più dettagliato:</span><span class="sxs-lookup"><span data-stu-id="18b9e-289">To explore :::no-loc(Identity)::: in more detail:</span></span>

* [<span data-ttu-id="18b9e-290">Crea origine interfaccia utente con identità completa</span><span class="sxs-lookup"><span data-stu-id="18b9e-290">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="18b9e-291">Esaminare l'origine di ogni pagina ed eseguire un'istruzione alla volta nel debugger.</span><span class="sxs-lookup"><span data-stu-id="18b9e-291">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="18b9e-292">:::no-loc(Identity)::: Componenti</span><span class="sxs-lookup"><span data-stu-id="18b9e-292">:::no-loc(Identity)::: Components</span></span>

<span data-ttu-id="18b9e-293">Tutti i :::no-loc(Identity)::: pacchetti NuGet dipendenti sono inclusi nel [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="18b9e-293">All the :::no-loc(Identity)::: dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="18b9e-294">Il pacchetto primario per :::no-loc(Identity)::: è [Microsoft. AspNetCore. :::no-loc(Identity)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/)</span><span class="sxs-lookup"><span data-stu-id="18b9e-294">The primary package for :::no-loc(Identity)::: is [Microsoft.AspNetCore.:::no-loc(Identity):::](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).</span></span> <span data-ttu-id="18b9e-295">Questo pacchetto contiene il set principale di interfacce per :::no-loc(ASP.NET Core Identity)::: ed è incluso in `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="18b9e-295">This package contains the core set of interfaces for :::no-loc(ASP.NET Core Identity):::, and is included by `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-no-locaspnet-core-identity"></a><span data-ttu-id="18b9e-296">Migrazione a :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="18b9e-296">Migrating to :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="18b9e-297">Per ulteriori informazioni e istruzioni sulla migrazione dell' :::no-loc(Identity)::: archivio esistente, vedere [eseguire la migrazione :::no-loc(Identity)::: dell'autenticazione e ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="18b9e-297">For more information and guidance on migrating your existing :::no-loc(Identity)::: store, see [Migrate Authentication and :::no-loc(Identity):::](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="18b9e-298">Impostazione della complessità della password</span><span class="sxs-lookup"><span data-stu-id="18b9e-298">Setting password strength</span></span>

<span data-ttu-id="18b9e-299">Vedere [configurazione](#pw) per un esempio che consente di impostare i requisiti minimi per le password.</span><span class="sxs-lookup"><span data-stu-id="18b9e-299">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="18b9e-300">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="18b9e-300">Next Steps</span></span>

* <span data-ttu-id="18b9e-301">Per informazioni sulla configurazione di using SQLite, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="18b9e-301">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring :::no-loc(Identity)::: using SQLite.</span></span>
* [<span data-ttu-id="18b9e-302">Configurare :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="18b9e-302">Configure :::no-loc(Identity):::</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
