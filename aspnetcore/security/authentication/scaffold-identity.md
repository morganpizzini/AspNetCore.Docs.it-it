---
title: Impalcatura Identity nei progetti ASP.NET Core
author: rick-anderson
description: Informazioni su come eseguire Identity l'impalcatura in un progetto ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 6f1ff69863e14c73e90496ea61188387f5267b19
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768390"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="9463d-103">Impalcatura Identity nei progetti ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9463d-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="9463d-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9463d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9463d-105">ASP.NET Core fornisce [ASP.NET Core Identity ](xref:security/authentication/identity) come [ Razor libreria di classi](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="9463d-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="9463d-106">Le applicazioni che Identity includono possono applicare l'impalcatura per aggiungere selettivamente il codice sorgente contenuto nella Identity Razor libreria di classi (RCL).</span><span class="sxs-lookup"><span data-stu-id="9463d-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="9463d-107">Se si vuole generare un codice sorgente, è possibile modificare il codice e modificarne il comportamento.</span><span class="sxs-lookup"><span data-stu-id="9463d-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="9463d-108">Ad esempio, è possibile indicare allo scaffolder di generare il codice usato nella registrazione.</span><span class="sxs-lookup"><span data-stu-id="9463d-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="9463d-109">Il codice generato ha la precedenza sullo stesso codice in Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="9463d-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="9463d-110">Per ottenere il controllo completo dell'interfaccia utente e non usare il RCL predefinito, vedere la sezione [creare l'origine dell'interfaccia utente Full Identity](#full).</span><span class="sxs-lookup"><span data-stu-id="9463d-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="9463d-111">Le applicazioni che **non** includono l'autenticazione possono applicare l'impalcatura per aggiungere il Identity pacchetto RCL.</span><span class="sxs-lookup"><span data-stu-id="9463d-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="9463d-112">È possibile scegliere di selezionare Identity il codice da generare.</span><span class="sxs-lookup"><span data-stu-id="9463d-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="9463d-113">Sebbene l'impalcatura generi la maggior parte del codice necessario, è necessario aggiornare il progetto per completare il processo.</span><span class="sxs-lookup"><span data-stu-id="9463d-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="9463d-114">Questo documento illustra i passaggi necessari per completare un Identity aggiornamento di ponteggi.</span><span class="sxs-lookup"><span data-stu-id="9463d-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="9463d-115">È consigliabile usare un sistema di controllo del codice sorgente che mostra le differenze dei file e consente di eseguire il backup delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="9463d-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="9463d-116">Controllare le modifiche dopo l'esecuzione Identity dell'impalcatura.</span><span class="sxs-lookup"><span data-stu-id="9463d-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="9463d-117">I servizi sono necessari quando si usa [l'autenticazione a due fattori](xref:security/authentication/identity-enable-qrcodes), la [conferma dell'account e il recupero della password](xref:security/authentication/accconfirm)e altre funzionalità di sicurezza con. Identity</span><span class="sxs-lookup"><span data-stu-id="9463d-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="9463d-118">I servizi o gli stub di servizio non vengono Identitygenerati durante l'impalcatura.</span><span class="sxs-lookup"><span data-stu-id="9463d-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="9463d-119">I servizi per abilitare queste funzionalità devono essere aggiunti manualmente.</span><span class="sxs-lookup"><span data-stu-id="9463d-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="9463d-120">Vedere ad esempio [Richiedi conferma tramite posta elettronica](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="9463d-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="9463d-121">Quando si esegue Identity il ponteggi con un nuovo contesto dati in un progetto con account singoli esistenti:</span><span class="sxs-lookup"><span data-stu-id="9463d-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="9463d-122">In `Startup.ConfigureServices`rimuovere le chiamate a:</span><span class="sxs-lookup"><span data-stu-id="9463d-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="9463d-123">Ad esempio, `AddDbContext` e `AddDefaultIdentity` sono impostati come commento nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="9463d-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="9463d-124">Il codice precedente commenta il codice duplicato in *aree/Identity/IdentityHostingStartup.cs*</span><span class="sxs-lookup"><span data-stu-id="9463d-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="9463d-125">In genere, le app create con singoli account ***non*** devono creare un nuovo contesto dati.</span><span class="sxs-lookup"><span data-stu-id="9463d-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="9463d-126">Identità del patibolo in un progetto vuoto</span><span class="sxs-lookup"><span data-stu-id="9463d-126">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="9463d-127">Aggiornare la `Startup` classe con codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="9463d-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="9463d-128">Identità del patibolo Razor in un progetto senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="9463d-128">Scaffold identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="9463d-129">viene configurato in *area/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="9463d-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="9463d-130">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="9463d-130">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="9463d-131">Migrazioni, UseAuthentication e layout</span><span class="sxs-lookup"><span data-stu-id="9463d-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="9463d-132">Abilita autenticazione</span><span class="sxs-lookup"><span data-stu-id="9463d-132">Enable authentication</span></span>

<span data-ttu-id="9463d-133">Aggiornare la `Startup` classe con codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="9463d-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="9463d-134">Modifiche del layout</span><span class="sxs-lookup"><span data-stu-id="9463d-134">Layout changes</span></span>

<span data-ttu-id="9463d-135">Facoltativo: aggiungere l'account di accesso`_LoginPartial`parziale () al file di layout:</span><span class="sxs-lookup"><span data-stu-id="9463d-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="9463d-136">Identità del patibolo Razor in un progetto con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="9463d-136">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
<span data-ttu-id="9463d-137">Alcune Identity opzioni sono configurate in *aree/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="9463d-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="9463d-138">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="9463d-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="9463d-139">Identità del patibolo in un progetto MVC senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="9463d-139">Scaffold identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="9463d-140">Facoltativo: aggiungere l'account di accesso`_LoginPartial`parziale () al file *Views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="9463d-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="9463d-141">Spostare il file *pages/Shared/_LoginPartial. cshtml* in *Views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="9463d-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="9463d-142">viene configurato in *area/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="9463d-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="9463d-143">Per ulteriori informazioni, vedere IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="9463d-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="9463d-144">Aggiornare la `Startup` classe con codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="9463d-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="9463d-145">Identità del patibolo in un progetto MVC con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="9463d-145">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="9463d-146">Crea origine interfaccia utente con identità completa</span><span class="sxs-lookup"><span data-stu-id="9463d-146">Create full identity UI source</span></span>

<span data-ttu-id="9463d-147">Per mantenere il Identity controllo completo dell'interfaccia utente, eseguire Identity l'impalcatura e selezionare **Sostituisci tutti i file**.</span><span class="sxs-lookup"><span data-stu-id="9463d-147">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="9463d-148">Il codice evidenziato seguente mostra le modifiche per sostituire l' Identity interfaccia utente Identity predefinita con in un'app Web ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="9463d-148">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="9463d-149">Questa operazione può essere eseguita per avere il Identity controllo completo dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="9463d-149">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="9463d-150">Il valore Identity predefinito viene sostituito nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="9463d-150">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="9463d-151">Il codice seguente imposta [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="9463d-151">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="9463d-152">Registrare un' `IEmailSender` implementazione, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9463d-152">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="9463d-153">Disabilita pagina Registro</span><span class="sxs-lookup"><span data-stu-id="9463d-153">Disable register page</span></span>

<span data-ttu-id="9463d-154">Per disabilitare la registrazione dell'utente:</span><span class="sxs-lookup"><span data-stu-id="9463d-154">To disable user registration:</span></span>

* <span data-ttu-id="9463d-155">Impalcature Identity.</span><span class="sxs-lookup"><span data-stu-id="9463d-155">Scaffold Identity.</span></span> <span data-ttu-id="9463d-156">Includere account. Register, account. login e account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="9463d-156">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="9463d-157">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9463d-157">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="9463d-158">Aggiornare le *areeIdentity//Pages/account/Register.cshtml.cs* in modo che gli utenti non possano effettuare la registrazione da questo endpoint:</span><span class="sxs-lookup"><span data-stu-id="9463d-158">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="9463d-159">Aggiornare le *areeIdentity//Pages/account/Register.cshtml* in modo che siano coerenti con le modifiche precedenti:</span><span class="sxs-lookup"><span data-stu-id="9463d-159">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="9463d-160">Impostare come commento o rimuovere il collegamento di registrazione da *aree/Identity/Pages/account/login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="9463d-160">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="9463d-161">Aggiornare la pagina *areeIdentity//Pages/account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="9463d-161">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="9463d-162">Rimuovere il codice e i collegamenti dal file cshtml.</span><span class="sxs-lookup"><span data-stu-id="9463d-162">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="9463d-163">Rimuovere il codice di conferma da `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="9463d-163">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="9463d-164">Usare un'altra app per aggiungere utenti</span><span class="sxs-lookup"><span data-stu-id="9463d-164">Use another app to add users</span></span>

<span data-ttu-id="9463d-165">Fornire un meccanismo per aggiungere utenti all'esterno dell'app Web.</span><span class="sxs-lookup"><span data-stu-id="9463d-165">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="9463d-166">Le opzioni per aggiungere utenti includono:</span><span class="sxs-lookup"><span data-stu-id="9463d-166">Options to add users include:</span></span>

* <span data-ttu-id="9463d-167">Un'app Web amministrativa dedicata.</span><span class="sxs-lookup"><span data-stu-id="9463d-167">A dedicated admin web app.</span></span>
* <span data-ttu-id="9463d-168">Un'app console.</span><span class="sxs-lookup"><span data-stu-id="9463d-168">A console app.</span></span>

<span data-ttu-id="9463d-169">Il codice seguente illustra un approccio per l'aggiunta di utenti:</span><span class="sxs-lookup"><span data-stu-id="9463d-169">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="9463d-170">Un elenco di utenti viene letto in memoria.</span><span class="sxs-lookup"><span data-stu-id="9463d-170">A list of users is read into memory.</span></span>
* <span data-ttu-id="9463d-171">Per ogni utente viene generata una password univoca complessa.</span><span class="sxs-lookup"><span data-stu-id="9463d-171">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="9463d-172">L'utente viene aggiunto al Identity database.</span><span class="sxs-lookup"><span data-stu-id="9463d-172">The user is added to the Identity database.</span></span>
* <span data-ttu-id="9463d-173">Viene inviata una notifica all'utente e viene chiesto di modificare la password.</span><span class="sxs-lookup"><span data-stu-id="9463d-173">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="9463d-174">Il codice seguente illustra l'aggiunta di un utente:</span><span class="sxs-lookup"><span data-stu-id="9463d-174">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="9463d-175">Per gli scenari di produzione è possibile seguire un approccio simile.</span><span class="sxs-lookup"><span data-stu-id="9463d-175">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="9463d-176">Impedisci la pubblicazione Identity di asset statici</span><span class="sxs-lookup"><span data-stu-id="9463d-176">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="9463d-177">Per evitare la pubblicazione Identity di asset statici nella radice Web, <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>vedere.</span><span class="sxs-lookup"><span data-stu-id="9463d-177">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9463d-178">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="9463d-178">Additional resources</span></span>

* [<span data-ttu-id="9463d-179">Modifiche al codice di autenticazione in ASP.NET Core 2,1 e versioni successive</span><span class="sxs-lookup"><span data-stu-id="9463d-179">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9463d-180">ASP.NET Core 2,1 e versioni successive [fornisce Identity ASP.NET Core](xref:security/authentication/identity) come [ Razor libreria di classi](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="9463d-180">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="9463d-181">Le applicazioni che Identity includono possono applicare l'impalcatura per aggiungere selettivamente il codice sorgente contenuto nella Identity Razor libreria di classi (RCL).</span><span class="sxs-lookup"><span data-stu-id="9463d-181">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="9463d-182">Se si vuole generare un codice sorgente, è possibile modificare il codice e modificarne il comportamento.</span><span class="sxs-lookup"><span data-stu-id="9463d-182">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="9463d-183">Ad esempio, è possibile indicare allo scaffolder di generare il codice usato nella registrazione.</span><span class="sxs-lookup"><span data-stu-id="9463d-183">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="9463d-184">Il codice generato ha la precedenza sullo stesso codice in Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="9463d-184">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="9463d-185">Per ottenere il controllo completo dell'interfaccia utente e non usare il RCL predefinito, vedere la sezione [creare l'origine dell'interfaccia utente Full Identity](#full).</span><span class="sxs-lookup"><span data-stu-id="9463d-185">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="9463d-186">Le applicazioni che **non** includono l'autenticazione possono applicare l'impalcatura per aggiungere il Identity pacchetto RCL.</span><span class="sxs-lookup"><span data-stu-id="9463d-186">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="9463d-187">È possibile scegliere di selezionare Identity il codice da generare.</span><span class="sxs-lookup"><span data-stu-id="9463d-187">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="9463d-188">Sebbene l'impalcatura generi la maggior parte del codice necessario, sarà necessario aggiornare il progetto per completare il processo.</span><span class="sxs-lookup"><span data-stu-id="9463d-188">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="9463d-189">Questo documento illustra i passaggi necessari per completare un Identity aggiornamento di ponteggi.</span><span class="sxs-lookup"><span data-stu-id="9463d-189">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="9463d-190">Quando si Identity esegue l'impalcatura, viene creato un file *ScaffoldingReadme. txt* nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="9463d-190">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="9463d-191">Il file *ScaffoldingReadme. txt* contiene istruzioni generali sugli elementi necessari per completare l' Identity aggiornamento di ponteggi.</span><span class="sxs-lookup"><span data-stu-id="9463d-191">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="9463d-192">Questo documento contiene istruzioni più complete rispetto al file *ScaffoldingReadme. txt* .</span><span class="sxs-lookup"><span data-stu-id="9463d-192">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="9463d-193">È consigliabile usare un sistema di controllo del codice sorgente che mostra le differenze dei file e consente di eseguire il backup delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="9463d-193">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="9463d-194">Controllare le modifiche dopo l'esecuzione Identity dell'impalcatura.</span><span class="sxs-lookup"><span data-stu-id="9463d-194">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="9463d-195">I servizi sono necessari quando si usa [l'autenticazione a due fattori](xref:security/authentication/identity-enable-qrcodes), la [conferma dell'account e il recupero della password](xref:security/authentication/accconfirm)e altre funzionalità di sicurezza con. Identity</span><span class="sxs-lookup"><span data-stu-id="9463d-195">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="9463d-196">I servizi o gli stub di servizio non vengono Identitygenerati durante l'impalcatura.</span><span class="sxs-lookup"><span data-stu-id="9463d-196">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="9463d-197">I servizi per abilitare queste funzionalità devono essere aggiunti manualmente.</span><span class="sxs-lookup"><span data-stu-id="9463d-197">Services to enable these features must be added manually.</span></span> <span data-ttu-id="9463d-198">Vedere ad esempio [Richiedi conferma tramite posta elettronica](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="9463d-198">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="9463d-199">Identità del patibolo in un progetto vuoto</span><span class="sxs-lookup"><span data-stu-id="9463d-199">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="9463d-200">Aggiungere le seguenti chiamate evidenziate alla `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="9463d-200">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="9463d-201">Identità del patibolo Razor in un progetto senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="9463d-201">Scaffold identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="9463d-202">viene configurato in *area/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="9463d-202"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="9463d-203">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="9463d-203">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="9463d-204">Migrazioni, UseAuthentication e layout</span><span class="sxs-lookup"><span data-stu-id="9463d-204">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="9463d-205">Abilita autenticazione</span><span class="sxs-lookup"><span data-stu-id="9463d-205">Enable authentication</span></span>

<span data-ttu-id="9463d-206">Nel `Configure` metodo della `Startup` classe, chiamare [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) dopo: `UseStaticFiles`</span><span class="sxs-lookup"><span data-stu-id="9463d-206">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="9463d-207">Modifiche del layout</span><span class="sxs-lookup"><span data-stu-id="9463d-207">Layout changes</span></span>

<span data-ttu-id="9463d-208">Facoltativo: aggiungere l'account di accesso`_LoginPartial`parziale () al file di layout:</span><span class="sxs-lookup"><span data-stu-id="9463d-208">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="9463d-209">Identità del patibolo Razor in un progetto con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="9463d-209">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
<span data-ttu-id="9463d-210">Alcune Identity opzioni sono configurate in *aree/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="9463d-210">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="9463d-211">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="9463d-211">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="9463d-212">Identità del patibolo in un progetto MVC senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="9463d-212">Scaffold identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="9463d-213">Facoltativo: aggiungere l'account di accesso`_LoginPartial`parziale () al file *Views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="9463d-213">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="9463d-214">Spostare il file *pages/Shared/_LoginPartial. cshtml* in *Views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="9463d-214">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="9463d-215">viene configurato in *area/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="9463d-215"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="9463d-216">Per ulteriori informazioni, vedere IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="9463d-216">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="9463d-217">Chiama [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) dopo `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="9463d-217">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="9463d-218">Identità del patibolo in un progetto MVC con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="9463d-218">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="9463d-219">Eliminare le *pagine o* la cartella condivisa e i file in tale cartella.</span><span class="sxs-lookup"><span data-stu-id="9463d-219">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="9463d-220">Crea origine interfaccia utente con identità completa</span><span class="sxs-lookup"><span data-stu-id="9463d-220">Create full identity UI source</span></span>

<span data-ttu-id="9463d-221">Per mantenere il Identity controllo completo dell'interfaccia utente, eseguire Identity l'impalcatura e selezionare **Sostituisci tutti i file**.</span><span class="sxs-lookup"><span data-stu-id="9463d-221">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="9463d-222">Il codice evidenziato seguente mostra le modifiche per sostituire l' Identity interfaccia utente Identity predefinita con in un'app Web ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="9463d-222">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="9463d-223">Questa operazione può essere eseguita per avere il Identity controllo completo dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="9463d-223">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="9463d-224">Il valore Identity predefinito viene sostituito nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="9463d-224">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="9463d-225">Il codice seguente imposta [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="9463d-225">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="9463d-226">Registrare un' `IEmailSender` implementazione, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9463d-226">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="9463d-227">Disabilita pagina Registro</span><span class="sxs-lookup"><span data-stu-id="9463d-227">Disable register page</span></span>

<span data-ttu-id="9463d-228">Per disabilitare la registrazione dell'utente:</span><span class="sxs-lookup"><span data-stu-id="9463d-228">To disable user registration:</span></span>

* <span data-ttu-id="9463d-229">Impalcature Identity.</span><span class="sxs-lookup"><span data-stu-id="9463d-229">Scaffold Identity.</span></span> <span data-ttu-id="9463d-230">Includere account. Register, account. login e account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="9463d-230">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="9463d-231">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9463d-231">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="9463d-232">Aggiornare le *areeIdentity//Pages/account/Register.cshtml.cs* in modo che gli utenti non possano effettuare la registrazione da questo endpoint:</span><span class="sxs-lookup"><span data-stu-id="9463d-232">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="9463d-233">Aggiornare le *areeIdentity//Pages/account/Register.cshtml* in modo che siano coerenti con le modifiche precedenti:</span><span class="sxs-lookup"><span data-stu-id="9463d-233">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="9463d-234">Impostare come commento o rimuovere il collegamento di registrazione da *aree/Identity/Pages/account/login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="9463d-234">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="9463d-235">Aggiornare la pagina *areeIdentity//Pages/account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="9463d-235">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="9463d-236">Rimuovere il codice e i collegamenti dal file cshtml.</span><span class="sxs-lookup"><span data-stu-id="9463d-236">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="9463d-237">Rimuovere il codice di conferma da `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="9463d-237">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="9463d-238">Usare un'altra app per aggiungere utenti</span><span class="sxs-lookup"><span data-stu-id="9463d-238">Use another app to add users</span></span>

<span data-ttu-id="9463d-239">Fornire un meccanismo per aggiungere utenti all'esterno dell'app Web.</span><span class="sxs-lookup"><span data-stu-id="9463d-239">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="9463d-240">Le opzioni per aggiungere utenti includono:</span><span class="sxs-lookup"><span data-stu-id="9463d-240">Options to add users include:</span></span>

* <span data-ttu-id="9463d-241">Un'app Web amministrativa dedicata.</span><span class="sxs-lookup"><span data-stu-id="9463d-241">A dedicated admin web app.</span></span>
* <span data-ttu-id="9463d-242">Un'app console.</span><span class="sxs-lookup"><span data-stu-id="9463d-242">A console app.</span></span>

<span data-ttu-id="9463d-243">Il codice seguente illustra un approccio per l'aggiunta di utenti:</span><span class="sxs-lookup"><span data-stu-id="9463d-243">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="9463d-244">Un elenco di utenti viene letto in memoria.</span><span class="sxs-lookup"><span data-stu-id="9463d-244">A list of users is read into memory.</span></span>
* <span data-ttu-id="9463d-245">Per ogni utente viene generata una password univoca complessa.</span><span class="sxs-lookup"><span data-stu-id="9463d-245">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="9463d-246">L'utente viene aggiunto al Identity database.</span><span class="sxs-lookup"><span data-stu-id="9463d-246">The user is added to the Identity database.</span></span>
* <span data-ttu-id="9463d-247">Viene inviata una notifica all'utente e viene chiesto di modificare la password.</span><span class="sxs-lookup"><span data-stu-id="9463d-247">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="9463d-248">Il codice seguente illustra l'aggiunta di un utente:</span><span class="sxs-lookup"><span data-stu-id="9463d-248">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="9463d-249">Per gli scenari di produzione è possibile seguire un approccio simile.</span><span class="sxs-lookup"><span data-stu-id="9463d-249">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9463d-250">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="9463d-250">Additional resources</span></span>

* [<span data-ttu-id="9463d-251">Modifiche al codice di autenticazione in ASP.NET Core 2,1 e versioni successive</span><span class="sxs-lookup"><span data-stu-id="9463d-251">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end