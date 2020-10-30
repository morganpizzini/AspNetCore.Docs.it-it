---
title: Impalcatura Identity nei progetti ASP.NET Core
author: rick-anderson
description: Informazioni su come eseguire Identity l'impalcatura in un progetto ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
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
uid: security/authentication/scaffold-identity
ms.openlocfilehash: c79dfc64d4311088c3f9ea03aad7570189000e2a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053319"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a>Impalcatura Identity nei progetti ASP.NET Core

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core fornisce [ASP.NET Core Identity](xref:security/authentication/identity) una [ Razor libreria di classi](xref:razor-pages/ui-class). Le applicazioni che includono Identity possono applicare l'impalcatura per aggiungere selettivamente il codice sorgente contenuto nella Identity Razor libreria di classi (RCL). Se si vuole generare un codice sorgente, è possibile modificare il codice e modificarne il comportamento. Ad esempio, è possibile indicare allo scaffolder di generare il codice usato nella registrazione. Il codice generato ha la precedenza sullo stesso codice in Identity RCL. Per ottenere il controllo completo dell'interfaccia utente e non usare il RCL predefinito, vedere la sezione [creare un'origine completa dell' Identity interfaccia utente](#full).

Le applicazioni che **non** includono l'autenticazione possono applicare l'impalcatura per aggiungere il Identity pacchetto RCL. È possibile scegliere di selezionare il Identity codice da generare.

Sebbene l'impalcatura generi la maggior parte del codice necessario, è necessario aggiornare il progetto per completare il processo. Questo documento illustra i passaggi necessari per completare un Identity aggiornamento di ponteggi.

È consigliabile usare un sistema di controllo del codice sorgente che mostra le differenze dei file e consente di eseguire il backup delle modifiche. Controllare le modifiche dopo l'esecuzione dell' Identity impalcatura.

I servizi sono necessari quando si usa [l'autenticazione a due fattori](xref:security/authentication/identity-enable-qrcodes), la [conferma dell'account e il recupero della password](xref:security/authentication/accconfirm)e altre funzionalità di sicurezza con Identity . I servizi o gli stub di servizio non vengono generati durante l'impalcatura Identity . I servizi per abilitare queste funzionalità devono essere aggiunti manualmente. Vedere ad esempio [Richiedi conferma tramite posta elettronica](xref:security/authentication/accconfirm#require-email-confirmation).

Quando si esegue il ponteggi Identity con un nuovo contesto dati in un progetto con account singoli esistenti:

* In `Startup.ConfigureServices` rimuovere le chiamate a:
  * `AddDbContext`
  * `AddDefaultIdentity`

Ad esempio, `AddDbContext` e `AddDefaultIdentity` sono impostati come commento nel codice seguente:

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

Il codice precedente commenta il codice duplicato in *aree/ Identity / Identity HostingStartup.cs*

In genere, le app create con singoli account devono * **non** _ creare un nuovo contesto dati.

## <a name="scaffold-no-locidentity-into-an-empty-project"></a>Impalcatura Identity in un progetto vuoto

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Aggiornare la `Startup` classe con codice simile al seguente:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a>Impalcatura Identity in un Razor progetto senza autorizzazione esistente

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

Identityè configurato in _Areas/ Identity / Identity HostingStartup.cs *. Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrazioni, UseAuthentication e layout

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Abilita autenticazione

Aggiornare la `Startup` classe con codice simile al seguente:

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Modifiche del layout

Facoltativo: aggiungere l'account di accesso parziale ( `_LoginPartial` ) al file di layout:

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a>Impalcatura Identity in un Razor progetto con autorizzazione

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

Alcune Identity opzioni sono configurate in *aree/ Identity / Identity HostingStartup.cs* . Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a>Impalcatura Identity in un progetto MVC senza autorizzazione esistente

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

Facoltativo: aggiungere l'account di accesso parziale ( `_LoginPartial` ) al file *Views/Shared/_Layout. cshtml* :

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* Spostare il file *pages/Shared/_LoginPartial. cshtml* in *Views/Shared/_LoginPartial. cshtml*

Identityè configurato in *areas/ Identity / Identity HostingStartup.cs* . Per ulteriori informazioni, vedere IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Aggiornare la `Startup` classe con codice simile al seguente:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a>Impalcatura Identity in un progetto MVC con autorizzazione

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a>Impalcatura Identity in un Blazor Server progetto senza autorizzazione esistente

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identityè configurato in *areas/ Identity / Identity HostingStartup.cs* . Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

### <a name="migrations"></a>Migrazioni

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a>Passare un token XSRF all'app

I token possono essere passati ai componenti:

* Quando i token di autenticazione vengono sottoposti a provisioning e salvati nell'autenticazione cookie , possono essere passati ai componenti di.
* Razor i componenti non possono usare `HttpContext` direttamente, quindi non è possibile ottenere un [token anti-request falsificazione (XSRF)](xref:security/anti-request-forgery) per pubblicare l' Identity endpoint di disconnessione in `/Identity/Account/Logout` . Un token XSRF può essere passato ai componenti.

Per altre informazioni, vedere <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.

Nel file *pages/_Host. cshtml* , stabilire il token dopo averlo aggiunto alle `InitialApplicationState` classi e `TokenProvider` :

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

Aggiornare il `App` componente ( *app. Razor* ) per assegnare `InitialState.XsrfToken` :

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

Il `TokenProvider` servizio illustrato nell'argomento viene usato nel `LoginDisplay` componente nella sezione [modifiche del flusso di layout e autenticazione](#layout-and-authentication-flow-changes) seguente.

### <a name="enable-authentication"></a>Abilita autenticazione

Nella `Startup` classe:

* Verificare che Razor i servizi Pages siano stati aggiunti in `Startup.ConfigureServices` .
* Se si usa [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), registrare il servizio.
* Chiamare il `UseDatabaseErrorPage` Generatore di applicazioni in `Startup.Configure` per l'ambiente di sviluppo.
* Chiamare `UseAuthentication` e `UseAuthorization` dopo `UseRouting` .
* Aggiungere un endpoint per le Razor pagine.

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a>Modifiche al layout e al flusso di autenticazione

Aggiungere un `RedirectToLogin` componente ( *RedirectToLogin. Razor* ) alla cartella *condivisa* dell'app nella radice del progetto:

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo("Identity/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

Aggiungere un `LoginDisplay` componente ( *LoginDisplay. Razor* ) alla cartella *condivisa* dell'app. Il [servizio TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) fornisce il token XSRF per il form HTML che invia un messaggio all' Identity endpoint di disconnessione:

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage/Index">
            Hello, @context.User.Identity.Name!
        </a>
        <form action="/Identity/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

Nel `MainLayout` componente ( *Shared/MainLayout. Razor* ) aggiungere il `LoginDisplay` componente al contenuto dell'elemento della riga superiore `<div>` :

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a>Endpoint di autenticazione dello stile

Poiché Blazor Server utilizza Razor pagine Identity di pagine, lo stile dell'interfaccia utente cambia quando un visitatore si sposta tra le Identity pagine e i componenti. Sono disponibili due opzioni per risolvere gli stili incongruenti:

#### <a name="build-no-locidentity-components"></a>Componenti di compilazione Identity

Un approccio all'utilizzo di componenti per Identity invece di pagine consiste nel creare Identity componenti. Poiché `SignInManager` e `UserManager` non sono supportati nei Razor componenti, usare gli endpoint API nell' Blazor Server app per elaborare le azioni dell'account utente.

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a>Usare un layout personalizzato con Blazor stili di app

Il Identity layout e gli stili delle pagine possono essere modificati per produrre pagine che usano il Blazor tema predefinito.

> [!NOTE]
> L'esempio in questa sezione è semplicemente un punto di partenza per la personalizzazione. Il lavoro aggiuntivo è probabilmente necessario per la migliore esperienza utente.

Creare un nuovo `NavMenu_IdentityLayout` componente ( *Shared/NavMenu_ Identity layout. Razor* ). Per il markup e il codice del componente, usare lo stesso contenuto del componente dell'app `NavMenu` ( *Shared/NavMenu. Razor* ). Rimuovere i `NavLink` componenti che non possono essere raggiunti in modo anonimo perché i reindirizzamenti automatici nel `RedirectToLogin` componente hanno esito negativo per i componenti che richiedono l'autenticazione o l'autorizzazione.

Nel file *pages/Shared/layout. cshtml* apportare le modifiche seguenti:

* Aggiungere le Razor direttive all'inizio del file per usare gli helper tag e i componenti dell'app nella cartella *condivisa* :

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  Sostituire `{APPLICATION ASSEMBLY}` con il nome dell'assembly dell'app.

* Aggiungere un `<base>` tag e un Blazor foglio `<link>` di stile al `<head>` contenuto:

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* Modificare il contenuto del `<body>` tag come segue:

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_IdentityLayout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default Identity UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/Identity/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/Identity/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/Identity/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a>Impalcatura Identity in un Blazor Server progetto con autorizzazione

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Alcune Identity opzioni sono configurate in *aree/ Identity / Identity HostingStartup.cs* . Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a>Crea Identity origine interfaccia utente completa

Per mantenere il controllo completo dell' Identity interfaccia utente, eseguire l' Identity impalcatura e selezionare **Sostituisci tutti i file** .

Il codice evidenziato seguente mostra le modifiche per sostituire l' Identity interfaccia utente predefinita con Identity in un'app web ASP.NET Core 2,1. Questa operazione può essere eseguita per avere il controllo completo dell' Identity interfaccia utente.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Il valore predefinito Identity viene sostituito nel codice seguente:

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

## <a name="password-configuration"></a>Configurazione password

Se <xref:Microsoft.AspNetCore.Identity.PasswordOptions> sono configurati in `Startup.ConfigureServices` , la configurazione degli [ `[StringLength]` attributi](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) potrebbe essere necessaria per la `Password` proprietà nelle pagine con impalcature Identity . `InputModel``Password`le proprietà si trovano nei file seguenti:

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a>Disabilitare una pagina

In questa sezione viene illustrato come disabilitare la pagina Register, ma è possibile utilizzare l'approccio per disabilitare qualsiasi pagina.

Per disabilitare la registrazione dell'utente:

* Impalcature Identity . Includere account. Register, account. login e account. RegisterConfirmation. Ad esempio:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Aggiornare le *aree/ Identity /pages/account/Register.cshtml.cs* in modo che gli utenti non possano effettuare la registrazione da questo endpoint:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Aggiornare le *aree/ Identity /pages/account/Register.cshtml* in modo che siano coerenti con le modifiche precedenti:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Impostare come commento o rimuovere il collegamento di registrazione da *aree/ Identity /pages/account/login.cshtml*

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* Aggiornare la pagina *aree/ Identity /pages/account/RegisterConfirmation* .

  * Rimuovere il codice e i collegamenti dal file cshtml.
  * Rimuovere il codice di conferma da `PageModel` :

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
* L'utente viene aggiunto al Identity database.
* Viene inviata una notifica all'utente e viene chiesto di modificare la password.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

Il codice seguente illustra l'aggiunta di un utente:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Per gli scenari di produzione è possibile seguire un approccio simile.

## <a name="prevent-publish-of-static-no-locidentity-assets"></a>Impedisci la pubblicazione di Identity Asset statici

Per evitare la pubblicazione Identity di asset statici nella radice Web, vedere <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .

## <a name="additional-resources"></a>Risorse aggiuntive

* [Modifiche al codice di autenticazione in ASP.NET Core 2,1 e versioni successive](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2,1 e versioni successive fornisce [ASP.NET Core Identity](xref:security/authentication/identity) una [ Razor libreria di classi](xref:razor-pages/ui-class). Le applicazioni che includono Identity possono applicare l'impalcatura per aggiungere selettivamente il codice sorgente contenuto nella Identity Razor libreria di classi (RCL). Se si vuole generare un codice sorgente, è possibile modificare il codice e modificarne il comportamento. Ad esempio, è possibile indicare allo scaffolder di generare il codice usato nella registrazione. Il codice generato ha la precedenza sullo stesso codice in Identity RCL. Per ottenere il controllo completo dell'interfaccia utente e non usare il RCL predefinito, vedere la sezione [creare l'origine dell'interfaccia utente Full Identity](#full).

Le applicazioni che **non** includono l'autenticazione possono applicare l'impalcatura per aggiungere il Identity pacchetto RCL. È possibile scegliere di selezionare il Identity codice da generare.

Sebbene l'impalcatura generi la maggior parte del codice necessario, sarà necessario aggiornare il progetto per completare il processo. Questo documento illustra i passaggi necessari per completare un Identity aggiornamento di ponteggi.

Quando Identity si esegue l'impalcatura, viene creato un file di *ScaffoldingReadme.txt* nella directory del progetto. Il file di *ScaffoldingReadme.txt* contiene istruzioni generali sugli elementi necessari per completare l' Identity aggiornamento di ponteggi. Questo documento contiene istruzioni più complete rispetto al file *ScaffoldingReadme.txt* .

È consigliabile usare un sistema di controllo del codice sorgente che mostra le differenze dei file e consente di eseguire il backup delle modifiche. Controllare le modifiche dopo l'esecuzione dell' Identity impalcatura.

> [!NOTE]
> I servizi sono necessari quando si usa [l'autenticazione a due fattori](xref:security/authentication/identity-enable-qrcodes), la [conferma dell'account e il recupero della password](xref:security/authentication/accconfirm)e altre funzionalità di sicurezza con Identity . I servizi o gli stub di servizio non vengono generati durante l'impalcatura Identity . I servizi per abilitare queste funzionalità devono essere aggiunti manualmente. Vedere ad esempio [Richiedi conferma tramite posta elettronica](xref:security/authentication/accconfirm#require-email-confirmation).

## <a name="scaffold-no-locidentity-into-an-empty-project"></a>Impalcatura Identity in un progetto vuoto

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Aggiungere le seguenti chiamate evidenziate alla `Startup` classe:

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a>Impalcatura Identity in un Razor progetto senza autorizzazione esistente

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

Identityè configurato in *areas/ Identity / Identity HostingStartup.cs* . Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrazioni, UseAuthentication e layout

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Abilita autenticazione

Nel `Configure` metodo della `Startup` classe, chiamare [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) dopo `UseStaticFiles` :

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Modifiche del layout

Facoltativo: aggiungere l'account di accesso parziale ( `_LoginPartial` ) al file di layout:

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a>Impalcatura Identity in un Razor progetto con autorizzazione

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

Alcune Identity opzioni sono configurate in *aree/ Identity / Identity HostingStartup.cs* . Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a>Impalcatura Identity in un progetto MVC senza autorizzazione esistente

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

Facoltativo: aggiungere l'account di accesso parziale ( `_LoginPartial` ) al file *Views/Shared/_Layout. cshtml* :

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* Spostare il file *pages/Shared/_LoginPartial. cshtml* in *Views/Shared/_LoginPartial. cshtml*

Identityè configurato in *areas/ Identity / Identity HostingStartup.cs* . Per ulteriori informazioni, vedere IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Chiama [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) dopo `UseStaticFiles` :

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a>Impalcatura Identity in un progetto MVC con autorizzazione

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

## <a name="create-full-no-locidentity-ui-source"></a>Crea Identity origine interfaccia utente completa

Per mantenere il controllo completo dell' Identity interfaccia utente, eseguire l' Identity impalcatura e selezionare **Sostituisci tutti i file** .

Il codice evidenziato seguente mostra le modifiche per sostituire l' Identity interfaccia utente predefinita con Identity in un'app web ASP.NET Core 2,1. Questa operazione può essere eseguita per avere il controllo completo dell' Identity interfaccia utente.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Il valore predefinito Identity viene sostituito nel codice seguente:

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

## <a name="password-configuration"></a>Configurazione password

Se <xref:Microsoft.AspNetCore.Identity.PasswordOptions> sono configurati in `Startup.ConfigureServices` , la configurazione degli [ `[StringLength]` attributi](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) potrebbe essere necessaria per la `Password` proprietà nelle pagine con impalcature Identity . `InputModel``Password`le proprietà si trovano nei file seguenti:

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a>Disabilita pagina Registro

Per disabilitare la registrazione dell'utente:

* Impalcature Identity . Includere account. Register, account. login e account. RegisterConfirmation. Ad esempio:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Aggiornare le *aree/ Identity /pages/account/Register.cshtml.cs* in modo che gli utenti non possano effettuare la registrazione da questo endpoint:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Aggiornare le *aree/ Identity /pages/account/Register.cshtml* in modo che siano coerenti con le modifiche precedenti:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Impostare come commento o rimuovere il collegamento di registrazione da *aree/ Identity /pages/account/login.cshtml*

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Aggiornare la pagina *aree/ Identity /pages/account/RegisterConfirmation* .

  * Rimuovere il codice e i collegamenti dal file cshtml.
  * Rimuovere il codice di conferma da `PageModel` :

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
* L'utente viene aggiunto al Identity database.
* Viene inviata una notifica all'utente e viene chiesto di modificare la password.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

Il codice seguente illustra l'aggiunta di un utente:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Per gli scenari di produzione è possibile seguire un approccio simile.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Modifiche al codice di autenticazione in ASP.NET Core 2,1 e versioni successive](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
