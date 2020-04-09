---
title: Aggiungere, scaricare ed eliminare i dati utente in Identity in un progetto ASP.NET CoreAdd, download, and delete user data to Identity in an ASP.NET Core project
author: rick-anderson
description: Informazioni su come aggiungere dati utente personalizzati a Identity in un progetto ASP.NET Core. Eliminare i dati per GDPR.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 76b83df22381429feab80056c36dbdac1e5f20c7
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501221"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>Aggiungere, scaricare ed eliminare dati utente personalizzati in Identity in un progetto ASP.NET CoreAdd, download, and delete custom user data to Identity in an ASP.NET Core project

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

Questo articolo illustra come:

* Aggiungere dati utente personalizzati a un'app Web ASP.NET Core.Add custom user data to an ASP.NET Core web app.
* Contrassegnare il modello di <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> dati utente personalizzato con l'attributo in modo che sia automaticamente disponibile per il download e l'eliminazione. Rendere i dati in grado di essere scaricati ed eliminati aiuta a soddisfare i requisiti [GDPR.](xref:security/gdpr)

L'esempio di progetto viene creato da un'app Web Razor Pages, ma le istruzioni sono simili per un'app Web MVC di base ASP.NET.

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ( come[scaricare](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prerequisiti

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a>Creare un'app Web Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**. Assegnare al progetto il nome **WebApp1** se si desidera che corrisponda allo spazio dei nomi del codice di esempio di [download.](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)
* Selezionare **ASP.NETapplicazione** > Web di base **OK**
* Selezionare **ASP.NET Core 3.0** nell'elenco a discesa
* Selezionare **Applicazione** > Web **OK**
* Compilare ed eseguire il progetto.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Dal menu **File** di Visual Studio selezionare **Nuovo** > **Progetto**. Assegnare al progetto il nome **WebApp1** se si desidera che corrisponda allo spazio dei nomi del codice di esempio di [download.](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)
* Selezionare **ASP.NETapplicazione** > Web di base **OK**
* Selezionare **ASP.NET Core 2.2** nell'elenco a discesa
* Selezionare **Applicazione** > Web **OK**
* Compilare ed eseguire il progetto.

::: moniker-end


# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a>Eseguire lo scaffolder di identità

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto > **Aggiungi** > **nuovo elemento con scaffolding**.
* Nel riquadro sinistro della finestra di dialogo **Aggiungi scaffold** selezionare **Identità** > **Aggiungi**.
* Nella finestra di dialogo **Aggiungi identità,** le seguenti opzioni:
  * Selezionare il file di layout *esistente: /Pages/Shared/_Layout.cshtml*
  * Selezionare i seguenti file da sostituire:
    * **Conto/Registra**
    * **Account/Gestione/Indice**
  * Selezionare **+** il pulsante per creare una nuova **classe di contesto dati**. Accettare il tipo (**WebApp1.Models.WebApp1Context** se il progetto è denominato **WebApp1**).
  * Selezionare **+** il pulsante per creare una nuova **classe User**. Accettare il tipo (**WebApp1User** se il progetto è denominato **WebApp1**) > **Aggiungi**.
* Selezionare **Aggiungi**.

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

Se in precedenza non è stato installato lo scaffolder ASP.NET Core, installarlo ora:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Aggiungere un riferimento al pacchetto [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) al file di progetto (con estensione csproj). Eseguire il comando seguente nella directory del progetto:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Eseguire il comando seguente per elencare le opzioni di Identity scaffolder:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Nella cartella del progetto, eseguire lo scaffolder di identità:

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

Seguire le istruzioni in [Migrazioni, UseAuthentication e layout](xref:security/authentication/scaffold-identity#efm) per eseguire la procedura seguente:

* Creare una migrazione e aggiornare il database.
* Aggiunta di `UseAuthentication` a `Startup.Configure`.
* Aggiungi `<partial name="_LoginPartial" />` al file di layout.
* Eseguire il test dell'app:
  * Registrare un utente
  * Selezionare il nuovo nome utente (accanto al collegamento **Disconnetti).** Potrebbe essere necessario espandere la finestra o selezionare l'icona della barra di spostamento per visualizzare il nome utente e altri collegamenti.
  * Selezionare la scheda **Dati personali.**
  * Selezionare il pulsante **Download** ed esaminare il file *PersonalData.json.*
  * Verificare il pulsante **Elimina,** che elimina l'utente connesso.

## <a name="add-custom-user-data-to-the-identity-db"></a>Aggiungere dati utente personalizzati al database di identitàAdd custom user data to the Identity DB

Aggiornare `IdentityUser` la classe derivata con proprietà personalizzate. Se è stato denominato il progetto WebApp1, il file è denominato *Areas/Identity/Data/WebApp1User.cs*. Aggiornare il file con il codice seguente:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

Le proprietà con l'attributo [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) sono:

* Eliminato quando la pagina Razor *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* chiama `UserManager.Delete`.
* Incluso nei dati scaricati dalla pagina Razor *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml.*

### <a name="update-the-accountmanageindexcshtml-page"></a>Aggiornare la pagina Account/Manage/Index.cshtml

Aggiornare `InputModel` il in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* con il seguente codice evidenziato:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

Aggiornare *aree/Identity/Pages/Account/Manage/Index.cshtml* con il seguente markup evidenziato:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

Aggiornare *aree/Identity/Pages/Account/Manage/Index.cshtml* con il seguente markup evidenziato:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>Aggiornare la pagina Account/Register.cshtml

Aggiornare `InputModel` il in *Areas/Identity/Pages/Account/Register.cshtml.cs* con il seguente codice evidenziato:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

Aggiornare il *codice Areas/Identity/Pages/Account/Register.cshtml* con il seguente markup evidenziato:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

Aggiornare il *codice Areas/Identity/Pages/Account/Register.cshtml* con il seguente markup evidenziato:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


Compilare il progetto.

### <a name="add-a-migration-for-the-custom-user-data"></a>Aggiungere una migrazione per i dati utente personalizzatiAdd a migration for the custom user data

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Nella console di Gestione **pacchetti**di Visual Studio:

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>Testare la creazione, la visualizzazione, il download, l'eliminazione dei dati utente personalizzati

Eseguire il test dell'app:

* Registrare un nuovo utente.
* Visualizzare i dati utente `/Identity/Account/Manage` personalizzati nella pagina.
* Scaricare e visualizzare i dati `/Identity/Account/Manage/PersonalData` personali degli utenti dalla pagina.

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a>Aggiungere attestazioni all'identità tramite IUserClaimsPrincipalFactoryAdd claims to Identity using IUserClaimsPrincipalFactory<ApplicationUser>

È possibile aggiungere attestazioni aggiuntive a `IUserClaimsPrincipalFactory<T>` ASP.NETidentità di base tramite l'interfaccia. Questa classe può essere aggiunta `Startup.ConfigureServices` all'app nel metodo. Aggiungere l'implementazione personalizzata della classe come segue:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

Il codice demo `ApplicationUser` utilizza la classe. Questa classe `IsAdmin` aggiunge una proprietà che viene utilizzata per aggiungere l'attestazione aggiuntiva.

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

L'oggetto `AdditionalUserClaimsPrincipalFactory` implementa l'interfaccia `UserClaimsPrincipalFactory`. Una nuova attestazione di `ClaimsPrincipal`ruolo viene aggiunta al file .

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

L'attestazione aggiuntiva può quindi essere usata nell'app. In una pagina Razor, l'istanza `IAuthorizationService` può essere utilizzata per accedere al valore dell'attestazione.

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
