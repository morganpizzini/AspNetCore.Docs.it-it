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
# <a name="scaffold-identity-in-aspnet-core-projects"></a>Identità di impalcatura nei progetti ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core fornisce [ASP.NET Core identità](xref:security/authentication/identity) come [libreria di classi Razor](xref:razor-pages/ui-class). Le applicazioni che includono l'identità possono applicare l'impalcatura per aggiungere selettivamente il codice sorgente contenuto nella libreria di classi Razor (RCL). Se si vuole generare un codice sorgente, è possibile modificare il codice e modificarne il comportamento. Ad esempio, è possibile indicare allo scaffolder di generare il codice usato nella registrazione. Il codice generato ha la precedenza rispetto allo stesso codice nella libreria di classi Razor per Identity. Per ottenere il controllo completo dell'interfaccia utente e non usare il RCL predefinito, vedere la sezione [creare l'origine dell'interfaccia utente Full Identity](#full).

Le applicazioni che **non** includono l'autenticazione possono applicare l'impalcatura per aggiungere il pacchetto di identità RCL. È possibile selezionare il codice Identity da generare.

Sebbene l'impalcatura generi la maggior parte del codice necessario, è necessario aggiornare il progetto per completare il processo. Questo documento illustra i passaggi necessari per completare un aggiornamento dell'impalcatura delle identità.

È consigliabile usare un sistema di controllo del codice sorgente che mostra le differenze dei file e consente di eseguire il backup delle modifiche. Controllare le modifiche dopo l'esecuzione dell'impalcatura di identità.

I servizi sono necessari quando si usa [l'autenticazione a due fattori](xref:security/authentication/identity-enable-qrcodes), la [conferma dell'account e il recupero della password](xref:security/authentication/accconfirm)e altre funzionalità di sicurezza con identità. I servizi o gli stub di servizio non vengono generati durante l'identità di ponteggi. I servizi per abilitare queste funzionalità devono essere aggiunti manualmente. Vedere ad esempio [Richiedi conferma tramite posta elettronica](xref:security/authentication/accconfirm#require-email-confirmation).

Quando si esegue il ponteggi dell'identità con un nuovo contesto dati in un progetto con account singoli esistenti:

* In `Startup.ConfigureServices`rimuovere le chiamate a:
  * `AddDbContext`
  * `AddDefaultIdentity`

Ad esempio, `AddDbContext` e `AddDefaultIdentity` sono impostati come commento nel codice seguente:

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

Il codice precedente commenta il codice duplicato in *aree/identità/IdentityHostingStartup. cs*

In genere, le app create con singoli account ***non*** devono creare un nuovo contesto dati.

## <a name="scaffold-identity-into-an-empty-project"></a>Identità del patibolo in un progetto vuoto

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Aggiornare la `Startup` classe con codice simile al seguente:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Identità del patibolo in un progetto Razor senza autorizzazione esistente

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

L'identità è configurata in *areas/Identity/IdentityHostingStartup. cs*. Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrazioni, UseAuthentication e layout

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Abilita autenticazione

Aggiornare la `Startup` classe con codice simile al seguente:

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Modifiche del layout

Facoltativo: aggiungere l'account di accesso`_LoginPartial`parziale () al file di layout:

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Identità del patibolo in un progetto Razor con autorizzazione

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
Alcune opzioni di identità sono configurate in *areas/Identity/IdentityHostingStartup. cs*. Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Identità del patibolo in un progetto MVC senza autorizzazione esistente

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

Facoltativo: aggiungere l'account di accesso`_LoginPartial`parziale () al file *Views/Shared/_Layout. cshtml* :

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* Spostare il file *pages/Shared/_LoginPartial. cshtml* in *Views/Shared/_LoginPartial. cshtml*

L'identità è configurata in *areas/Identity/IdentityHostingStartup. cs*. Per ulteriori informazioni, vedere IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Aggiornare la `Startup` classe con codice simile al seguente:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Identità del patibolo in un progetto MVC con autorizzazione

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Crea origine interfaccia utente con identità completa

Per mantenere il controllo completo dell'interfaccia utente di identità, eseguire l'impalcatura delle identità e selezionare **Sostituisci tutti i file**.

Il codice evidenziato seguente mostra le modifiche per sostituire l'interfaccia utente di identità predefinita con Identity in un'app Web ASP.NET Core 2,1. Questa operazione può essere eseguita per avere il controllo completo dell'interfaccia utente di identità.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

L'identità predefinita viene sostituita dal codice seguente:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Il codice seguente imposta [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Registrare un' `IEmailSender` implementazione, ad esempio:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>Disabilita pagina Registro

Per disabilitare la registrazione dell'utente:

* Identità del patibolo. Includere account. Register, account. login e account. RegisterConfirmation. Ad esempio:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Aggiornare le *aree/Identity/Pages/account/Register. cshtml. cs,* in modo che gli utenti non possano effettuare la registrazione da questo endpoint:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Aggiornare le *aree/Identity/Pages/account/Register. cshtml* in modo che siano coerenti con le modifiche precedenti:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Impostare come commento o rimuovere il collegamento di registrazione da *aree/Identity/Pages/account/login. cshtml*

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Aggiornare la pagina *aree/identità/pagine/account/RegisterConfirmation* .

  * Rimuovere il codice e i collegamenti dal file cshtml.
  * Rimuovere il codice di conferma da `PageModel`:

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
  
### <a name="use-another-app-to-add-users"></a>Usare un'altra app per aggiungere utenti

Fornire un meccanismo per aggiungere utenti all'esterno dell'app Web. Le opzioni per aggiungere utenti includono:

* Un'app Web amministrativa dedicata.
* Un'app console.

Il codice seguente illustra un approccio per l'aggiunta di utenti:

* Un elenco di utenti viene letto in memoria.
* Per ogni utente viene generata una password univoca complessa.
* L'utente viene aggiunto al database di identità.
* Viene inviata una notifica all'utente e viene chiesto di modificare la password.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

Il codice seguente illustra l'aggiunta di un utente:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Per gli scenari di produzione è possibile seguire un approccio simile.

## <a name="prevent-publish-of-static-identity-assets"></a>Impedisci la pubblicazione di asset di identità statici

Per evitare la pubblicazione di asset di identità statici nella radice Web <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>, vedere.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Modifiche al codice di autenticazione in ASP.NET Core 2,1 e versioni successive](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2,1 e versioni successive fornisce [ASP.NET Core identità](xref:security/authentication/identity) come [libreria di classi Razor](xref:razor-pages/ui-class). Le applicazioni che includono l'identità possono applicare l'impalcatura per aggiungere selettivamente il codice sorgente contenuto nella libreria di classi Razor (RCL). Se si vuole generare un codice sorgente, è possibile modificare il codice e modificarne il comportamento. Ad esempio, è possibile indicare allo scaffolder di generare il codice usato nella registrazione. Il codice generato ha la precedenza rispetto allo stesso codice nella libreria di classi Razor per Identity. Per ottenere il controllo completo dell'interfaccia utente e non usare il RCL predefinito, vedere la sezione [creare l'origine dell'interfaccia utente Full Identity](#full).

Le applicazioni che **non** includono l'autenticazione possono applicare l'impalcatura per aggiungere il pacchetto di identità RCL. È possibile selezionare il codice Identity da generare.

Sebbene l'impalcatura generi la maggior parte del codice necessario, sarà necessario aggiornare il progetto per completare il processo. Questo documento illustra i passaggi necessari per completare un aggiornamento dell'impalcatura delle identità.

Quando si esegue l'impalcatura delle identità, viene creato un file *ScaffoldingReadme. txt* nella directory del progetto. Il file *ScaffoldingReadme. txt* contiene istruzioni generali sugli elementi necessari per completare l'aggiornamento dell'impalcatura delle identità. Questo documento contiene istruzioni più complete rispetto al file *ScaffoldingReadme. txt* .

È consigliabile usare un sistema di controllo del codice sorgente che mostra le differenze dei file e consente di eseguire il backup delle modifiche. Controllare le modifiche dopo l'esecuzione dell'impalcatura di identità.

> [!NOTE]
> I servizi sono necessari quando si usa [l'autenticazione a due fattori](xref:security/authentication/identity-enable-qrcodes), la [conferma dell'account e il recupero della password](xref:security/authentication/accconfirm)e altre funzionalità di sicurezza con identità. I servizi o gli stub di servizio non vengono generati durante l'identità di ponteggi. I servizi per abilitare queste funzionalità devono essere aggiunti manualmente. Vedere ad esempio [Richiedi conferma tramite posta elettronica](xref:security/authentication/accconfirm#require-email-confirmation).

## <a name="scaffold-identity-into-an-empty-project"></a>Identità del patibolo in un progetto vuoto

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Aggiungere le seguenti chiamate evidenziate alla `Startup` classe:

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Identità del patibolo in un progetto Razor senza autorizzazione esistente

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

L'identità è configurata in *areas/Identity/IdentityHostingStartup. cs*. Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrazioni, UseAuthentication e layout

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Abilita autenticazione

Nel `Configure` metodo della `Startup` classe, chiamare [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) dopo: `UseStaticFiles`

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Modifiche del layout

Facoltativo: aggiungere l'account di accesso`_LoginPartial`parziale () al file di layout:

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Identità del patibolo in un progetto Razor con autorizzazione

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
Alcune opzioni di identità sono configurate in *areas/Identity/IdentityHostingStartup. cs*. Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Identità del patibolo in un progetto MVC senza autorizzazione esistente

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

Facoltativo: aggiungere l'account di accesso`_LoginPartial`parziale () al file *Views/Shared/_Layout. cshtml* :

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* Spostare il file *pages/Shared/_LoginPartial. cshtml* in *Views/Shared/_LoginPartial. cshtml*

L'identità è configurata in *areas/Identity/IdentityHostingStartup. cs*. Per ulteriori informazioni, vedere IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Chiama [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) dopo `UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Identità del patibolo in un progetto MVC con autorizzazione

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Eliminare le *pagine o* la cartella condivisa e i file in tale cartella.

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Crea origine interfaccia utente con identità completa

Per mantenere il controllo completo dell'interfaccia utente di identità, eseguire l'impalcatura delle identità e selezionare **Sostituisci tutti i file**.

Il codice evidenziato seguente mostra le modifiche per sostituire l'interfaccia utente di identità predefinita con Identity in un'app Web ASP.NET Core 2,1. Questa operazione può essere eseguita per avere il controllo completo dell'interfaccia utente di identità.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

L'identità predefinita viene sostituita dal codice seguente:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Il codice seguente imposta [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Registrare un' `IEmailSender` implementazione, ad esempio:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>Disabilita pagina Registro

Per disabilitare la registrazione dell'utente:

* Identità del patibolo. Includere account. Register, account. login e account. RegisterConfirmation. Ad esempio:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Aggiornare le *aree/Identity/Pages/account/Register. cshtml. cs,* in modo che gli utenti non possano effettuare la registrazione da questo endpoint:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Aggiornare le *aree/Identity/Pages/account/Register. cshtml* in modo che siano coerenti con le modifiche precedenti:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Impostare come commento o rimuovere il collegamento di registrazione da *aree/Identity/Pages/account/login. cshtml*

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Aggiornare la pagina *aree/identità/pagine/account/RegisterConfirmation* .

  * Rimuovere il codice e i collegamenti dal file cshtml.
  * Rimuovere il codice di conferma da `PageModel`:

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
  
### <a name="use-another-app-to-add-users"></a>Usare un'altra app per aggiungere utenti

Fornire un meccanismo per aggiungere utenti all'esterno dell'app Web. Le opzioni per aggiungere utenti includono:

* Un'app Web amministrativa dedicata.
* Un'app console.

Il codice seguente illustra un approccio per l'aggiunta di utenti:

* Un elenco di utenti viene letto in memoria.
* Per ogni utente viene generata una password univoca complessa.
* L'utente viene aggiunto al database di identità.
* Viene inviata una notifica all'utente e viene chiesto di modificare la password.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

Il codice seguente illustra l'aggiunta di un utente:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Per gli scenari di produzione è possibile seguire un approccio simile.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Modifiche al codice di autenticazione in ASP.NET Core 2,1 e versioni successive](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end