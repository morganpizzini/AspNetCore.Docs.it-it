---
title: Identità di impalcatura nei progetti ASP.NET Core
author: rick-anderson
description: Informazioni su come eseguire l'impalcatura dell'identità in un progetto ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
uid: security/authentication/scaffold-identity
ms.openlocfilehash: ac95035b114274ddaa6ccb0b5b6e3da98885e39e
ms.sourcegitcommit: 6318d2bdd63116e178c34492a904be85ec9ac108
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82604727"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="41253-103">Identità di impalcatura nei progetti ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="41253-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="41253-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="41253-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="41253-105">ASP.NET Core fornisce [ASP.NET Core identità](xref:security/authentication/identity) come [libreria di classi Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="41253-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="41253-106">Le applicazioni che includono l'identità possono applicare l'impalcatura per aggiungere selettivamente il codice sorgente contenuto nella libreria di classi Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="41253-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="41253-107">Se si vuole generare un codice sorgente, è possibile modificare il codice e modificarne il comportamento.</span><span class="sxs-lookup"><span data-stu-id="41253-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="41253-108">Ad esempio, è possibile indicare allo scaffolder di generare il codice usato nella registrazione.</span><span class="sxs-lookup"><span data-stu-id="41253-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="41253-109">Il codice generato ha la precedenza rispetto allo stesso codice nella libreria di classi Razor per Identity.</span><span class="sxs-lookup"><span data-stu-id="41253-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="41253-110">Per ottenere il controllo completo dell'interfaccia utente e non usare il RCL predefinito, vedere la sezione [creare l'origine dell'interfaccia utente Full Identity](#full).</span><span class="sxs-lookup"><span data-stu-id="41253-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="41253-111">Le applicazioni che **non** includono l'autenticazione possono applicare l'impalcatura per aggiungere il pacchetto di identità RCL.</span><span class="sxs-lookup"><span data-stu-id="41253-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="41253-112">È possibile selezionare il codice Identity da generare.</span><span class="sxs-lookup"><span data-stu-id="41253-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="41253-113">Sebbene l'impalcatura generi la maggior parte del codice necessario, è necessario aggiornare il progetto per completare il processo.</span><span class="sxs-lookup"><span data-stu-id="41253-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="41253-114">Questo documento illustra i passaggi necessari per completare un aggiornamento dell'impalcatura delle identità.</span><span class="sxs-lookup"><span data-stu-id="41253-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="41253-115">È consigliabile usare un sistema di controllo del codice sorgente che mostra le differenze dei file e consente di eseguire il backup delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="41253-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="41253-116">Controllare le modifiche dopo l'esecuzione dell'impalcatura di identità.</span><span class="sxs-lookup"><span data-stu-id="41253-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="41253-117">I servizi sono necessari quando si usa [l'autenticazione a due fattori](xref:security/authentication/identity-enable-qrcodes), la [conferma dell'account e il recupero della password](xref:security/authentication/accconfirm)e altre funzionalità di sicurezza con identità.</span><span class="sxs-lookup"><span data-stu-id="41253-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="41253-118">I servizi o gli stub di servizio non vengono generati durante l'identità di ponteggi.</span><span class="sxs-lookup"><span data-stu-id="41253-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="41253-119">I servizi per abilitare queste funzionalità devono essere aggiunti manualmente.</span><span class="sxs-lookup"><span data-stu-id="41253-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="41253-120">Vedere ad esempio [Richiedi conferma tramite posta elettronica](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="41253-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="41253-121">Quando si esegue il ponteggi dell'identità con un nuovo contesto dati in un progetto con account singoli esistenti:</span><span class="sxs-lookup"><span data-stu-id="41253-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="41253-122">In `Startup.ConfigureServices`rimuovere le chiamate a:</span><span class="sxs-lookup"><span data-stu-id="41253-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="41253-123">Ad esempio, `AddDbContext` e `AddDefaultIdentity` sono impostati come commento nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="41253-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="41253-124">Il codice precedente commenta il codice duplicato in *aree/identità/IdentityHostingStartup. cs*</span><span class="sxs-lookup"><span data-stu-id="41253-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="41253-125">In genere, le app create con singoli account ***non*** devono creare un nuovo contesto dati.</span><span class="sxs-lookup"><span data-stu-id="41253-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="41253-126">Identità del patibolo in un progetto vuoto</span><span class="sxs-lookup"><span data-stu-id="41253-126">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="41253-127">Aggiornare la `Startup` classe con codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="41253-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="41253-128">Identità del patibolo in un progetto Razor senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="41253-128">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="41253-129">L'identità è configurata in *areas/Identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="41253-129">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="41253-130">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="41253-130">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="41253-131">Migrazioni, UseAuthentication e layout</span><span class="sxs-lookup"><span data-stu-id="41253-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="41253-132">Abilita autenticazione</span><span class="sxs-lookup"><span data-stu-id="41253-132">Enable authentication</span></span>

<span data-ttu-id="41253-133">Aggiornare la `Startup` classe con codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="41253-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="41253-134">Modifiche del layout</span><span class="sxs-lookup"><span data-stu-id="41253-134">Layout changes</span></span>

<span data-ttu-id="41253-135">Facoltativo: aggiungere l'account di accesso`_LoginPartial`parziale () al file di layout:</span><span class="sxs-lookup"><span data-stu-id="41253-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="41253-136">Identità del patibolo in un progetto Razor con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="41253-136">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="41253-137">Alcune opzioni di identità sono configurate in *areas/Identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="41253-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="41253-138">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="41253-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="41253-139">Identità del patibolo in un progetto MVC senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="41253-139">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="41253-140">Facoltativo: aggiungere l'account di accesso`_LoginPartial`parziale () al file *Views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="41253-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="41253-141">Spostare il file *pages/Shared/_LoginPartial. cshtml* in *Views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="41253-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="41253-142">L'identità è configurata in *areas/Identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="41253-142">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="41253-143">Per ulteriori informazioni, vedere IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="41253-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="41253-144">Aggiornare la `Startup` classe con codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="41253-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="41253-145">Identità del patibolo in un progetto MVC con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="41253-145">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="41253-146">Crea origine interfaccia utente con identità completa</span><span class="sxs-lookup"><span data-stu-id="41253-146">Create full identity UI source</span></span>

<span data-ttu-id="41253-147">Per mantenere il controllo completo dell'interfaccia utente di identità, eseguire l'impalcatura delle identità e selezionare **Sostituisci tutti i file**.</span><span class="sxs-lookup"><span data-stu-id="41253-147">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="41253-148">Il codice evidenziato seguente mostra le modifiche per sostituire l'interfaccia utente di identità predefinita con Identity in un'app Web ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="41253-148">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="41253-149">Questa operazione può essere eseguita per avere il controllo completo dell'interfaccia utente di identità.</span><span class="sxs-lookup"><span data-stu-id="41253-149">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="41253-150">L'identità predefinita viene sostituita dal codice seguente:</span><span class="sxs-lookup"><span data-stu-id="41253-150">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="41253-151">Il codice seguente imposta [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="41253-151">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="41253-152">Registrare un' `IEmailSender` implementazione, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="41253-152">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="41253-153">Disabilita pagina Registro</span><span class="sxs-lookup"><span data-stu-id="41253-153">Disable register page</span></span>

<span data-ttu-id="41253-154">Per disabilitare la registrazione dell'utente:</span><span class="sxs-lookup"><span data-stu-id="41253-154">To disable user registration:</span></span>

* <span data-ttu-id="41253-155">Identità del patibolo.</span><span class="sxs-lookup"><span data-stu-id="41253-155">Scaffold Identity.</span></span> <span data-ttu-id="41253-156">Includere account. Register, account. login e account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="41253-156">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="41253-157">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="41253-157">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="41253-158">Aggiornare le *aree/Identity/Pages/account/Register. cshtml. cs,* in modo che gli utenti non possano effettuare la registrazione da questo endpoint:</span><span class="sxs-lookup"><span data-stu-id="41253-158">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="41253-159">Aggiornare le *aree/Identity/Pages/account/Register. cshtml* in modo che siano coerenti con le modifiche precedenti:</span><span class="sxs-lookup"><span data-stu-id="41253-159">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="41253-160">Impostare come commento o rimuovere il collegamento di registrazione da *aree/Identity/Pages/account/login. cshtml*</span><span class="sxs-lookup"><span data-stu-id="41253-160">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="41253-161">Aggiornare la pagina *aree/identità/pagine/account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="41253-161">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="41253-162">Rimuovere il codice e i collegamenti dal file cshtml.</span><span class="sxs-lookup"><span data-stu-id="41253-162">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="41253-163">Rimuovere il codice di conferma da `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="41253-163">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="41253-164">Usare un'altra app per aggiungere utenti</span><span class="sxs-lookup"><span data-stu-id="41253-164">Use another app to add users</span></span>

<span data-ttu-id="41253-165">Fornire un meccanismo per aggiungere utenti all'esterno dell'app Web.</span><span class="sxs-lookup"><span data-stu-id="41253-165">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="41253-166">Le opzioni per aggiungere utenti includono:</span><span class="sxs-lookup"><span data-stu-id="41253-166">Options to add users include:</span></span>

* <span data-ttu-id="41253-167">Un'app Web amministrativa dedicata.</span><span class="sxs-lookup"><span data-stu-id="41253-167">A dedicated admin web app.</span></span>
* <span data-ttu-id="41253-168">Un'app console.</span><span class="sxs-lookup"><span data-stu-id="41253-168">A console app.</span></span>

<span data-ttu-id="41253-169">Il codice seguente illustra un approccio per l'aggiunta di utenti:</span><span class="sxs-lookup"><span data-stu-id="41253-169">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="41253-170">Un elenco di utenti viene letto in memoria.</span><span class="sxs-lookup"><span data-stu-id="41253-170">A list of users is read into memory.</span></span>
* <span data-ttu-id="41253-171">Per ogni utente viene generata una password univoca complessa.</span><span class="sxs-lookup"><span data-stu-id="41253-171">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="41253-172">L'utente viene aggiunto al database di identità.</span><span class="sxs-lookup"><span data-stu-id="41253-172">The user is added to the Identity database.</span></span>
* <span data-ttu-id="41253-173">Viene inviata una notifica all'utente e viene chiesto di modificare la password.</span><span class="sxs-lookup"><span data-stu-id="41253-173">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="41253-174">Il codice seguente illustra l'aggiunta di un utente:</span><span class="sxs-lookup"><span data-stu-id="41253-174">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="41253-175">Per gli scenari di produzione è possibile seguire un approccio simile.</span><span class="sxs-lookup"><span data-stu-id="41253-175">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="41253-176">Impedisci la pubblicazione di asset di identità statici</span><span class="sxs-lookup"><span data-stu-id="41253-176">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="41253-177">Per evitare la pubblicazione di asset di identità statici nella radice Web <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>, vedere.</span><span class="sxs-lookup"><span data-stu-id="41253-177">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="41253-178">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="41253-178">Additional resources</span></span>

* [<span data-ttu-id="41253-179">Modifiche al codice di autenticazione in ASP.NET Core 2,1 e versioni successive</span><span class="sxs-lookup"><span data-stu-id="41253-179">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="41253-180">ASP.NET Core 2,1 e versioni successive fornisce [ASP.NET Core identità](xref:security/authentication/identity) come [libreria di classi Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="41253-180">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="41253-181">Le applicazioni che includono l'identità possono applicare l'impalcatura per aggiungere selettivamente il codice sorgente contenuto nella libreria di classi Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="41253-181">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="41253-182">Se si vuole generare un codice sorgente, è possibile modificare il codice e modificarne il comportamento.</span><span class="sxs-lookup"><span data-stu-id="41253-182">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="41253-183">Ad esempio, è possibile indicare allo scaffolder di generare il codice usato nella registrazione.</span><span class="sxs-lookup"><span data-stu-id="41253-183">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="41253-184">Il codice generato ha la precedenza rispetto allo stesso codice nella libreria di classi Razor per Identity.</span><span class="sxs-lookup"><span data-stu-id="41253-184">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="41253-185">Per ottenere il controllo completo dell'interfaccia utente e non usare il RCL predefinito, vedere la sezione [creare l'origine dell'interfaccia utente Full Identity](#full).</span><span class="sxs-lookup"><span data-stu-id="41253-185">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="41253-186">Le applicazioni che **non** includono l'autenticazione possono applicare l'impalcatura per aggiungere il pacchetto di identità RCL.</span><span class="sxs-lookup"><span data-stu-id="41253-186">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="41253-187">È possibile selezionare il codice Identity da generare.</span><span class="sxs-lookup"><span data-stu-id="41253-187">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="41253-188">Sebbene l'impalcatura generi la maggior parte del codice necessario, sarà necessario aggiornare il progetto per completare il processo.</span><span class="sxs-lookup"><span data-stu-id="41253-188">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="41253-189">Questo documento illustra i passaggi necessari per completare un aggiornamento dell'impalcatura delle identità.</span><span class="sxs-lookup"><span data-stu-id="41253-189">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="41253-190">Quando si esegue l'impalcatura delle identità, viene creato un file *ScaffoldingReadme. txt* nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="41253-190">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="41253-191">Il file *ScaffoldingReadme. txt* contiene istruzioni generali sugli elementi necessari per completare l'aggiornamento dell'impalcatura delle identità.</span><span class="sxs-lookup"><span data-stu-id="41253-191">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="41253-192">Questo documento contiene istruzioni più complete rispetto al file *ScaffoldingReadme. txt* .</span><span class="sxs-lookup"><span data-stu-id="41253-192">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="41253-193">È consigliabile usare un sistema di controllo del codice sorgente che mostra le differenze dei file e consente di eseguire il backup delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="41253-193">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="41253-194">Controllare le modifiche dopo l'esecuzione dell'impalcatura di identità.</span><span class="sxs-lookup"><span data-stu-id="41253-194">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="41253-195">I servizi sono necessari quando si usa [l'autenticazione a due fattori](xref:security/authentication/identity-enable-qrcodes), la [conferma dell'account e il recupero della password](xref:security/authentication/accconfirm)e altre funzionalità di sicurezza con identità.</span><span class="sxs-lookup"><span data-stu-id="41253-195">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="41253-196">I servizi o gli stub di servizio non vengono generati durante l'identità di ponteggi.</span><span class="sxs-lookup"><span data-stu-id="41253-196">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="41253-197">I servizi per abilitare queste funzionalità devono essere aggiunti manualmente.</span><span class="sxs-lookup"><span data-stu-id="41253-197">Services to enable these features must be added manually.</span></span> <span data-ttu-id="41253-198">Vedere ad esempio [Richiedi conferma tramite posta elettronica](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="41253-198">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="41253-199">Identità del patibolo in un progetto vuoto</span><span class="sxs-lookup"><span data-stu-id="41253-199">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="41253-200">Aggiungere le seguenti chiamate evidenziate alla `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="41253-200">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="41253-201">Identità del patibolo in un progetto Razor senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="41253-201">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="41253-202">L'identità è configurata in *areas/Identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="41253-202">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="41253-203">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="41253-203">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="41253-204">Migrazioni, UseAuthentication e layout</span><span class="sxs-lookup"><span data-stu-id="41253-204">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="41253-205">Abilita autenticazione</span><span class="sxs-lookup"><span data-stu-id="41253-205">Enable authentication</span></span>

<span data-ttu-id="41253-206">Nel `Configure` metodo della `Startup` classe, chiamare [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) dopo: `UseStaticFiles`</span><span class="sxs-lookup"><span data-stu-id="41253-206">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="41253-207">Modifiche del layout</span><span class="sxs-lookup"><span data-stu-id="41253-207">Layout changes</span></span>

<span data-ttu-id="41253-208">Facoltativo: aggiungere l'account di accesso`_LoginPartial`parziale () al file di layout:</span><span class="sxs-lookup"><span data-stu-id="41253-208">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="41253-209">Identità del patibolo in un progetto Razor con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="41253-209">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="41253-210">Alcune opzioni di identità sono configurate in *areas/Identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="41253-210">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="41253-211">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="41253-211">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="41253-212">Identità del patibolo in un progetto MVC senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="41253-212">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="41253-213">Facoltativo: aggiungere l'account di accesso`_LoginPartial`parziale () al file *Views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="41253-213">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="41253-214">Spostare il file *pages/Shared/_LoginPartial. cshtml* in *Views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="41253-214">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="41253-215">L'identità è configurata in *areas/Identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="41253-215">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="41253-216">Per ulteriori informazioni, vedere IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="41253-216">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="41253-217">Chiama [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) dopo `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="41253-217">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="41253-218">Identità del patibolo in un progetto MVC con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="41253-218">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="41253-219">Eliminare le *pagine o* la cartella condivisa e i file in tale cartella.</span><span class="sxs-lookup"><span data-stu-id="41253-219">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="41253-220">Crea origine interfaccia utente con identità completa</span><span class="sxs-lookup"><span data-stu-id="41253-220">Create full identity UI source</span></span>

<span data-ttu-id="41253-221">Per mantenere il controllo completo dell'interfaccia utente di identità, eseguire l'impalcatura delle identità e selezionare **Sostituisci tutti i file**.</span><span class="sxs-lookup"><span data-stu-id="41253-221">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="41253-222">Il codice evidenziato seguente mostra le modifiche per sostituire l'interfaccia utente di identità predefinita con Identity in un'app Web ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="41253-222">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="41253-223">Questa operazione può essere eseguita per avere il controllo completo dell'interfaccia utente di identità.</span><span class="sxs-lookup"><span data-stu-id="41253-223">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="41253-224">L'identità predefinita viene sostituita dal codice seguente:</span><span class="sxs-lookup"><span data-stu-id="41253-224">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="41253-225">Il codice seguente imposta [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="41253-225">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="41253-226">Registrare un' `IEmailSender` implementazione, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="41253-226">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="41253-227">Disabilita pagina Registro</span><span class="sxs-lookup"><span data-stu-id="41253-227">Disable register page</span></span>

<span data-ttu-id="41253-228">Per disabilitare la registrazione dell'utente:</span><span class="sxs-lookup"><span data-stu-id="41253-228">To disable user registration:</span></span>

* <span data-ttu-id="41253-229">Identità del patibolo.</span><span class="sxs-lookup"><span data-stu-id="41253-229">Scaffold Identity.</span></span> <span data-ttu-id="41253-230">Includere account. Register, account. login e account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="41253-230">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="41253-231">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="41253-231">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="41253-232">Aggiornare le *aree/Identity/Pages/account/Register. cshtml. cs,* in modo che gli utenti non possano effettuare la registrazione da questo endpoint:</span><span class="sxs-lookup"><span data-stu-id="41253-232">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="41253-233">Aggiornare le *aree/Identity/Pages/account/Register. cshtml* in modo che siano coerenti con le modifiche precedenti:</span><span class="sxs-lookup"><span data-stu-id="41253-233">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="41253-234">Impostare come commento o rimuovere il collegamento di registrazione da *aree/Identity/Pages/account/login. cshtml*</span><span class="sxs-lookup"><span data-stu-id="41253-234">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="41253-235">Aggiornare la pagina *aree/identità/pagine/account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="41253-235">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="41253-236">Rimuovere il codice e i collegamenti dal file cshtml.</span><span class="sxs-lookup"><span data-stu-id="41253-236">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="41253-237">Rimuovere il codice di conferma da `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="41253-237">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="41253-238">Usare un'altra app per aggiungere utenti</span><span class="sxs-lookup"><span data-stu-id="41253-238">Use another app to add users</span></span>

<span data-ttu-id="41253-239">Fornire un meccanismo per aggiungere utenti all'esterno dell'app Web.</span><span class="sxs-lookup"><span data-stu-id="41253-239">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="41253-240">Le opzioni per aggiungere utenti includono:</span><span class="sxs-lookup"><span data-stu-id="41253-240">Options to add users include:</span></span>

* <span data-ttu-id="41253-241">Un'app Web amministrativa dedicata.</span><span class="sxs-lookup"><span data-stu-id="41253-241">A dedicated admin web app.</span></span>
* <span data-ttu-id="41253-242">Un'app console.</span><span class="sxs-lookup"><span data-stu-id="41253-242">A console app.</span></span>

<span data-ttu-id="41253-243">Il codice seguente illustra un approccio per l'aggiunta di utenti:</span><span class="sxs-lookup"><span data-stu-id="41253-243">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="41253-244">Un elenco di utenti viene letto in memoria.</span><span class="sxs-lookup"><span data-stu-id="41253-244">A list of users is read into memory.</span></span>
* <span data-ttu-id="41253-245">Per ogni utente viene generata una password univoca complessa.</span><span class="sxs-lookup"><span data-stu-id="41253-245">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="41253-246">L'utente viene aggiunto al database di identità.</span><span class="sxs-lookup"><span data-stu-id="41253-246">The user is added to the Identity database.</span></span>
* <span data-ttu-id="41253-247">Viene inviata una notifica all'utente e viene chiesto di modificare la password.</span><span class="sxs-lookup"><span data-stu-id="41253-247">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="41253-248">Il codice seguente illustra l'aggiunta di un utente:</span><span class="sxs-lookup"><span data-stu-id="41253-248">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="41253-249">Per gli scenari di produzione è possibile seguire un approccio simile.</span><span class="sxs-lookup"><span data-stu-id="41253-249">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="41253-250">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="41253-250">Additional resources</span></span>

* [<span data-ttu-id="41253-251">Modifiche al codice di autenticazione in ASP.NET Core 2,1 e versioni successive</span><span class="sxs-lookup"><span data-stu-id="41253-251">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end