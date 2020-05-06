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
# <a name="introduction-to-identity-on-aspnet-core"></a>Introduzione all'identità in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

Identità ASP.NET Core:

* È un'API che supporta la funzionalità di accesso dell'interfaccia utente.
* Gestisce utenti, password, dati di profilo, ruoli, attestazioni, token, conferma tramite posta elettronica e altro ancora.

Gli utenti possono creare un account con le informazioni di accesso archiviate in Identity oppure possono usare un provider di accesso esterno. I provider di accesso esterni supportati includono [Facebook, Google, account Microsoft e Twitter](xref:security/authentication/social/index).

Il [codice sorgente di identità](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) è disponibile in GitHub. [Identità del patibolo](xref:security/authentication/scaffold-identity) e visualizzare i file generati per esaminare l'interazione del modello con l'identità.

L'identità viene in genere configurata utilizzando un database SQL Server per archiviare i nomi utente, le password e i dati di profilo. In alternativa, è possibile usare un altro archivio permanente, ad esempio archiviazione tabelle di Azure.

In questo argomento si apprenderà come usare l'identità per la registrazione, l'accesso e la disconnessione di un utente. Nota: i modelli considerano il nome utente e il messaggio di posta elettronica come lo stesso per gli utenti. Per istruzioni più dettagliate sulla creazione di app che usano l'identità, vedere la sezione passaggi successivi alla fine di questo articolo.

[Piattaforma di identità Microsoft](/azure/active-directory/develop/) :

* Evoluzione della piattaforma per sviluppatori Azure Active Directory (Azure AD).
* Non correlato all'identità del ASP.NET Core.

[!INCLUDE[](~/includes/IdentityServer4.md)]

Consente di [visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([come scaricare)](xref:index#how-to-download-a-sample).

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a>Creare un'app Web con l'autenticazione

Creare un progetto di applicazione Web di ASP.NET Core con singoli account utente.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Selezionare **file** > **nuovo** > **progetto**.
* Selezionare **Applicazione Web ASP.NET Core**. Denominare il progetto **app Web 1** in modo che abbia lo stesso spazio dei nomi del download del progetto. Fare clic su **OK**.
* Selezionare un' **applicazione Web**ASP.NET Core, quindi selezionare **Modifica autenticazione**.
* Selezionare **singoli account utente** e fare clic su **OK**.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

Il comando precedente crea un'app Web Razor con SQLite. Per creare l'app Web con il database locale, eseguire il comando seguente:

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

Il progetto generato fornisce [ASP.NET Core identità](xref:security/authentication/identity) come [libreria di classi Razor](xref:razor-pages/ui-class). La libreria della classe Razor Identity espone gli endpoint con `Identity` l'area. Ad esempio:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>Applicare le migrazioni

Applicare le migrazioni per inizializzare il database.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Eseguire il comando seguente nella console di gestione pacchetti (PMC):

`PM> Update-Database`

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Le migrazioni non sono necessarie in questo passaggio quando si usa SQLite. Per il database locale, eseguire il comando seguente:

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Registro di test e account di accesso

Eseguire l'app e registrare un utente. A seconda delle dimensioni dello schermo, potrebbe essere necessario selezionare l'interruttore di spostamento per visualizzare i collegamenti di **Registro** e di **accesso** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Configurare i servizi di identità

I servizi vengono aggiunti `ConfigureServices`in. Il modello tipico consiste nel chiamare tutti i metodi `Add{Service}` e quindi chiamare tutti i metodi `services.Configure{Service}`.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

Il codice evidenziato precedente configura l'identità con i valori delle opzioni predefinite. I servizi vengono resi disponibili per l'applicazione tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).

L'identità viene abilitata <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>chiamando. `UseAuthentication`aggiunge il [middleware](xref:fundamentals/middleware/index) di autenticazione alla pipeline della richiesta.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

L'app generata dal modello non usa l' [autorizzazione](xref:security/authorization/secure-data). `app.UseAuthorization`è incluso per assicurarsi che venga aggiunto nell'ordine corretto se l'app aggiunge l'autorizzazione. `UseRouting``UseAuthorization` `UseEndpoints` , `UseAuthentication`, e devono essere chiamati nell'ordine indicato nel codice precedente.

Per ulteriori informazioni su `IdentityOptions` e `Startup`, vedere <xref:Microsoft.AspNetCore.Identity.IdentityOptions> e [avvio dell'applicazione](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Registrazione, accesso e disconnessione del patibolo

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Aggiungere i file di registro, di accesso e di disconnessione. Seguire l' [identità del patibolo in un progetto Razor con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) le istruzioni di autorizzazione per generare il codice illustrato in questa sezione.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Se il progetto è stato creato con il nome **app Web 1**, eseguire i comandi seguenti. In caso contrario, utilizzare lo spazio dei `ApplicationDbContext`nomi corretto per:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell usa il punto e virgola come separatore di comandi. Quando si usa PowerShell, usare il carattere di escape per il punto e virgola nell'elenco dei file o inserire l'elenco di file tra virgolette doppie, come illustrato nell'esempio precedente.

Per altre informazioni sull'identità di impalcatura, vedere la pagina relativa all' [identità di impalcatura in un progetto Razor con autorizzazione](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).

---

### <a name="examine-register"></a>Esaminare il registro

Quando un utente fa clic sul collegamento **Register** , `RegisterModel.OnPostAsync` viene richiamata l'azione. L'utente viene creato da [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) sull' `_userManager` oggetto. `_userManager`viene fornito dall'inserimento delle dipendenze:

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

Se l'utente è stato creato correttamente, l'utente è connesso dalla chiamata a `_signInManager.SignInAsync`.

Vedere la [conferma dell'account](xref:security/authentication/accconfirm#prevent-login-at-registration) per i passaggi per impedire l'accesso immediato alla registrazione.

### <a name="log-in"></a>Accesso

Il modulo di accesso viene visualizzato quando:

* Il collegamento **Accedi** è selezionato.
* Un utente tenta di accedere a una pagina con restrizioni che non è autorizzata ad accedere **o** quando non è stata autenticata dal sistema.

Quando viene inviato il modulo nella pagina di accesso, viene `OnPostAsync` chiamata l'azione. `PasswordSignInAsync`viene chiamato sull' `_signInManager` oggetto, fornito dall'inserimento delle dipendenze.

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

La classe `Controller` base espone una `User` proprietà a cui è possibile accedere dai metodi del controller. È possibile, ad esempio, `User.Claims` enumerare e prendere decisioni di autorizzazione. Per altre informazioni, vedere <xref:security/authorization/introduction>.

### <a name="log-out"></a>Effettuare la disconnessione

Il collegamento **Disconnetti** richiama l' `LogoutModel.OnPost` azione. 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

Nel codice precedente, il codice `return RedirectToPage();` deve essere un reindirizzamento in modo che il browser esegua una nuova richiesta e venga aggiornata l'identità dell'utente.

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) Cancella le attestazioni dell'utente archiviate in un cookie.

Post viene specificato nelle *pagine/Shared/_LoginPartial. cshtml*:

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a>Identità del test

I modelli di progetto Web predefiniti consentono l'accesso anonimo alle Home page. Per verificare l'identità, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)aggiungere:

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

Se è stato eseguito l'accesso, disconnettersi. Eseguire l'app e selezionare il collegamento per la **privacy** . Si verrà reindirizzati alla pagina di accesso.

### <a name="explore-identity"></a>Esplora identità

Per esplorare l'identità in modo più dettagliato:

* [Crea origine interfaccia utente con identità completa](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Esaminare l'origine di ogni pagina ed eseguire un'istruzione alla volta nel debugger.

## <a name="identity-components"></a>Componenti Identity

Tutti i pacchetti NuGet dipendenti dall'identità sono inclusi nel [Framework condiviso ASP.NET Core](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).

Il pacchetto primario per Identity è [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/). Questo pacchetto contiene il set principale di interfacce per ASP.NET Core identità ed è incluso in `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.

## <a name="migrating-to-aspnet-core-identity"></a>Migrazione a identità ASP.NET Core

Per altre informazioni e indicazioni sulla migrazione dell'archivio identità esistente, vedere [eseguire la migrazione dell'autenticazione e dell'identità](xref:migration/identity).

## <a name="setting-password-strength"></a>Impostazione della complessità della password

Vedere [configurazione](#pw) per un esempio che consente di impostare i requisiti minimi per le password.

## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity e AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>è stato introdotto in ASP.NET Core 2,1. La `AddDefaultIdentity` chiamata a è simile alla chiamata a quanto segue:

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

Per altre informazioni, vedere [origine AddDefaultIdentity](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .

## <a name="prevent-publish-of-static-identity-assets"></a>Impedisci la pubblicazione di asset di identità statici

Per evitare la pubblicazione di risorse di identità statiche (fogli di stile e file JavaScript per l'interfaccia utente dell'identità) nella `ResolveStaticWebAssetsInputsDependsOn` radice Web `RemoveIdentityAssets` , aggiungere la proprietà e la destinazione seguenti al file di progetto dell'app:

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

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla configurazione dell'identità con SQLite, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) .
* [Configurare Identity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core identità è un sistema di appartenenza che aggiunge la funzionalità di accesso alle app ASP.NET Core. Gli utenti possono creare un account con le informazioni di accesso archiviate in Identity oppure possono usare un provider di accesso esterno. I provider di accesso esterni supportati includono [Facebook, Google, account Microsoft e Twitter](xref:security/authentication/social/index).

L'identità può essere configurata utilizzando un database SQL Server per archiviare i nomi utente, le password e i dati di profilo. In alternativa, è possibile usare un altro archivio permanente, ad esempio archiviazione tabelle di Azure.

Consente di [visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([come scaricare)](xref:index#how-to-download-a-sample).

In questo argomento si apprenderà come usare l'identità per la registrazione, l'accesso e la disconnessione di un utente. Per istruzioni più dettagliate sulla creazione di app che usano l'identità, vedere la sezione passaggi successivi alla fine di questo articolo.

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity e AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>è stato introdotto in ASP.NET Core 2,1. La `AddDefaultIdentity` chiamata a è simile alla chiamata a quanto segue:

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

Per altre informazioni, vedere [origine AddDefaultIdentity](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .

## <a name="create-a-web-app-with-authentication"></a>Creare un'app Web con l'autenticazione

Creare un progetto di applicazione Web di ASP.NET Core con singoli account utente.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Selezionare **file** > **nuovo** > **progetto**.
* Selezionare **Applicazione Web ASP.NET Core**. Denominare il progetto **app Web 1** in modo che abbia lo stesso spazio dei nomi del download del progetto. Fare clic su **OK**.
* Selezionare un' **applicazione Web**ASP.NET Core, quindi selezionare **Modifica autenticazione**.
* Selezionare **singoli account utente** e fare clic su **OK**.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

Il progetto generato fornisce [ASP.NET Core identità](xref:security/authentication/identity) come [libreria di classi Razor](xref:razor-pages/ui-class). La libreria della classe Razor Identity espone gli endpoint con `Identity` l'area. Ad esempio:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>Applicare le migrazioni

Applicare le migrazioni per inizializzare il database.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Eseguire il comando seguente nella console di gestione pacchetti (PMC):

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Registro di test e account di accesso

Eseguire l'app e registrare un utente. A seconda delle dimensioni dello schermo, potrebbe essere necessario selezionare l'interruttore di spostamento per visualizzare i collegamenti di **Registro** e di **accesso** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Configurare i servizi di identità

I servizi vengono aggiunti `ConfigureServices`in. Il modello tipico consiste nel chiamare tutti i metodi `Add{Service}` e quindi chiamare tutti i metodi `services.Configure{Service}`.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

Il codice precedente configura l'identità con i valori delle opzioni predefinite. I servizi vengono resi disponibili per l'applicazione tramite l' [inserimento di dipendenze](xref:fundamentals/dependency-injection).

L'identità viene abilitata chiamando [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_). `UseAuthentication`aggiunge il [middleware](xref:fundamentals/middleware/index) di autenticazione alla pipeline della richiesta.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

Per ulteriori informazioni, vedere la [Classe IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) e l' [avvio dell'applicazione](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Registrazione, accesso e disconnessione del patibolo

Seguire l' [identità del patibolo in un progetto Razor con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) le istruzioni di autorizzazione per generare il codice illustrato in questa sezione.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Aggiungere i file di registro, di accesso e di disconnessione.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Se il progetto è stato creato con il nome **app Web 1**, eseguire i comandi seguenti. In caso contrario, utilizzare lo spazio dei `ApplicationDbContext`nomi corretto per:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell usa il punto e virgola come separatore di comandi. Quando si usa PowerShell, usare il carattere di escape per il punto e virgola nell'elenco dei file o inserire l'elenco di file tra virgolette doppie, come illustrato nell'esempio precedente.

---

### <a name="examine-register"></a>Esaminare il registro

Quando un utente fa clic sul collegamento **Register** , `RegisterModel.OnPostAsync` viene richiamata l'azione. L'utente viene creato da [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) sull' `_userManager` oggetto. `_userManager`viene fornito dall'inserimento delle dipendenze:

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

Se l'utente è stato creato correttamente, l'utente è connesso dalla chiamata a `_signInManager.SignInAsync`.

**Nota:** Vedere la [conferma dell'account](xref:security/authentication/accconfirm#prevent-login-at-registration) per i passaggi per impedire l'accesso immediato alla registrazione.

### <a name="log-in"></a>Accesso

Il modulo di accesso viene visualizzato quando:

* Il collegamento **Accedi** è selezionato.
* Un utente tenta di accedere a una pagina con restrizioni che non è autorizzata ad accedere **o** quando non è stata autenticata dal sistema.

Quando viene inviato il modulo nella pagina di accesso, viene `OnPostAsync` chiamata l'azione. `PasswordSignInAsync`viene chiamato sull' `_signInManager` oggetto, fornito dall'inserimento delle dipendenze.

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

La classe `Controller` base espone una `User` proprietà a cui è possibile accedere dai metodi del controller. È possibile, ad esempio, `User.Claims` enumerare e prendere decisioni di autorizzazione. Per altre informazioni, vedere <xref:security/authorization/introduction>.

### <a name="log-out"></a>Effettuare la disconnessione

Il collegamento **Disconnetti** richiama l' `LogoutModel.OnPost` azione. 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) Cancella le attestazioni dell'utente archiviate in un cookie.

Post viene specificato nelle *pagine/Shared/_LoginPartial. cshtml*:

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a>TestIdentity

I modelli di progetto Web predefiniti consentono l'accesso anonimo alle Home page. Per eseguire Identityil test [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) , aggiungere alla pagina privacy.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

Se è stato eseguito l'accesso, disconnettersi. Eseguire l'app e selezionare il collegamento per la **privacy** . Si verrà reindirizzati alla pagina di accesso.

### <a name="explore-identity"></a>EsplorareIdentity

Per esplorare Identity in modo più dettagliato:

* [Crea origine interfaccia utente con identità completa](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Esaminare l'origine di ogni pagina ed eseguire un'istruzione alla volta nel debugger.

## <a name="identity-components"></a>IdentityComponenti

Tutti i Identity pacchetti NuGet dipendenti sono inclusi nel [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).

Il pacchetto primario per Identity è [Microsoft. AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/) Questo pacchetto contiene il set principale di interfacce per ASP.NET Core Identityed è incluso in `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.

## <a name="migrating-to-aspnet-core-identity"></a>Migrazione a ASP.NET CoreIdentity

Per ulteriori informazioni e istruzioni sulla migrazione dell'archivio Identity esistente, vedere [eseguire la migrazione Identitydell'autenticazione e ](xref:migration/identity).

## <a name="setting-password-strength"></a>Impostazione della complessità della password

Vedere [configurazione](#pw) per un esempio che consente di impostare i requisiti minimi per le password.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla configurazione Identity di using SQLite, vedere [questo problema di GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) .
* [ConfigurareIdentity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
