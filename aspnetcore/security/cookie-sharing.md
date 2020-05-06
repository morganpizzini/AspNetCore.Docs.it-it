---
title: Condividere i cookie di autenticazione tra le app ASP.NET
author: rick-anderson
description: Informazioni su come condividere i cookie di autenticazione tra ASP.NET 4. x e le app ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cookie-sharing
ms.openlocfilehash: a6aac53008e634e87b18bd34b3d2babdad74ae50
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776142"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a>Condividere i cookie di autenticazione tra le app ASP.NET

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

I siti Web sono spesso costituiti da singole app Web che interagiscono. Per offrire un'esperienza di Single Sign-On (SSO), le app Web all'interno di un sito devono condividere i cookie di autenticazione. Per supportare questo scenario, lo stack di protezione dei dati consente la condivisione dell'autenticazione del cookie Katana e dei ticket di autenticazione ASP.NET Core cookie.

Negli esempi seguenti:

* Il nome del cookie di autenticazione è impostato su un valore `.AspNet.SharedCookie`comune di.
* L' `AuthenticationType` oggetto è impostato `Identity.Application` su in modo esplicito o per impostazione predefinita.
* Un nome di app comune viene usato per consentire al sistema di protezione dei dati di condividere le`SharedCookieApp`chiavi di protezione dei dati ().
* `Identity.Application`viene usato come schema di autenticazione. Indipendentemente dallo schema usato, è necessario usarlo *in modo coerente all'interno e* nelle app di cookie condivise come schema predefinito o impostarlo in modo esplicito. Lo schema viene usato durante la crittografia e la decrittografia dei cookie, quindi è necessario usare uno schema coerente tra le app.
* Viene utilizzata una posizione di archiviazione della [chiave di protezione dati](xref:security/data-protection/implementation/key-management) comune.
  * In ASP.NET Core Apps <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> viene usato per impostare il percorso di archiviazione delle chiavi.
  * Nelle app .NET Framework il middleware di autenticazione dei cookie usa un' <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>implementazione di. `DataProtectionProvider`fornisce servizi di protezione dei dati per la crittografia e la decrittografia dei dati di payload del cookie di autenticazione. L' `DataProtectionProvider` istanza è isolata dal sistema di protezione dei dati usato da altre parti dell'app. [DataProtectionProvider. Create (System. IO. DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accetta un <xref:System.IO.DirectoryInfo> oggetto per specificare il percorso per l'archiviazione delle chiavi di protezione dati.
* `DataProtectionProvider`richiede il pacchetto NuGet [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :
  * Nelle app ASP.NET Core 2. x fare riferimento al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).
  * In .NET Framework app aggiungere un riferimento al pacchetto a [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>imposta il nome comune dell'app.

## <a name="share-authentication-cookies-with-aspnet-core-identity"></a>Condividere i cookie di autenticazione con ASP.NET CoreIdentity

Quando si usa IdentityASP.NET Core:

* Le chiavi di protezione dei dati e il nome dell'app devono essere condivise tra le app. Un percorso di archiviazione delle <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> chiavi comune viene fornito al metodo negli esempi seguenti. Usare <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> per configurare un nome comune per l'app`SharedCookieApp` condivisa (negli esempi seguenti). Per altre informazioni, vedere <xref:security/data-protection/configuration/overview>.
* Usare il <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> metodo di estensione per configurare il servizio di protezione dei dati per i cookie.
* Il tipo di autenticazione predefinito `Identity.Application`è.

In `Startup.ConfigureServices`:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-cookies-without-aspnet-core-identity"></a>Condividi cookie di autenticazione senza ASP.NET CoreIdentity

Quando si usano i cookie direttamente Identitysenza ASP.NET Core, configurare la protezione dei `Startup.ConfigureServices`dati e l'autenticazione in. Nell'esempio seguente, il tipo di autenticazione è impostato su `Identity.Application`:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-cookies-across-different-base-paths"></a>Condividere i cookie tra percorsi di base diversi

Un cookie di autenticazione usa [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) come [cookie. Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path)predefinito. Se il cookie dell'app deve essere condiviso tra percorsi di base diversi `Path` , è necessario eseguire l'override di:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-cookies-across-subdomains"></a>Condividere i cookie tra sottodomini

Quando si ospitano app che condividono cookie tra sottodomini, specificare un dominio comune nella proprietà [cookie. Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) . Per condividere i cookie tra le `contoso.com` `Cookie.Domain` app in, `first_subdomain.contoso.com` ad `second_subdomain.contoso.com`esempio e, specificare `.contoso.com`come:

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>Crittografare le chiavi di protezione dei dati inattivi

Per le distribuzioni di produzione, `DataProtectionProvider` configurare per crittografare le chiavi inattive con DPAPI o un X509Certificate. Per altre informazioni, vedere <xref:security/data-protection/implementation/key-encryption-at-rest>. Nell'esempio seguente viene fornita un'identificazione personale del certificato per <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>Condividere i cookie di autenticazione tra ASP.NET 4. x e app ASP.NET Core

Le app ASP.NET 4. x che usano il middleware di autenticazione dei cookie Katana possono essere configurate per generare cookie di autenticazione compatibili con il middleware di autenticazione ASP.NET Core cookie. In questo modo è possibile aggiornare le singole app di un sito di grandi dimensioni in diversi passaggi garantendo al tempo stesso un'esperienza di accesso SSO uniforme nel sito.

Quando un'app usa il middleware di autenticazione dei cookie Katana `UseCookieAuthentication` , chiama nel file *Startup.auth.cs* del progetto. I progetti di app Web ASP.NET 4. x creati con Visual Studio 2013 e versioni successive usano il middleware di autenticazione dei cookie katana per impostazione predefinita. Anche `UseCookieAuthentication` se è obsoleto e non supportato per le app ASP.NET Core `UseCookieAuthentication` , la chiamata in un'app ASP.NET 4. x che usa il middleware di autenticazione dei cookie Katana è valida.

Un'app ASP.NET 4. x deve avere come destinazione .NET Framework 4.5.1 o versione successiva. In caso contrario, non è possibile installare i pacchetti NuGet necessari.

Per condividere i cookie di autenticazione tra un'app ASP.NET 4. x e un'app ASP.NET Core, configurare l'app ASP.NET Core come indicato nella sezione [condividere i cookie di autenticazione tra ASP.NET Core app](#share-authentication-cookies-with-aspnet-core-identity) , quindi configurare l'app ASP.NET 4. x come indicato di seguito.

Verificare che i pacchetti dell'app vengano aggiornati alle versioni più recenti. Installare il pacchetto [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) in ogni app ASP.NET 4. x.

Individuare e modificare la chiamata a `UseCookieAuthentication`:

* Modificare il nome del cookie in modo che corrisponda al nome usato dal middleware di`.AspNet.SharedCookie` autenticazione del cookie ASP.NET Core (nell'esempio).
* Nell'esempio seguente, il tipo di autenticazione è impostato su `Identity.Application`.
* Fornire un'istanza di un `DataProtectionProvider` oggetto inizializzato al percorso di archiviazione della chiave di protezione dati comune.
* Verificare che il nome dell'app sia impostato sul nome dell'app comune usato da tutte le app che condividono i`SharedCookieApp` cookie di autenticazione (nell'esempio).

Se non si `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` imposta `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`e, <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> impostare su un'attestazione che distingue gli utenti univoci.

*App_start/Startup.auth.cs*:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

Quando si genera un'identità utente, il tipo di`Identity.Application`autenticazione () deve corrispondere al tipo `AuthenticationType` definito in `UseCookieAuthentication` set con in *app_start/Startup.auth.cs*.

*Models/IdentityModels. cs*:

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a>Usare un database utente comune

Quando le app usano lo Identity stesso schema (stessa versione Identitydi), verificare che Identity il sistema per ogni app punti allo stesso database utente. In caso contrario, il sistema di identità genera errori in fase di esecuzione quando tenta di trovare una corrispondenza tra le informazioni nel cookie di autenticazione e le informazioni contenute nel relativo database.

Quando lo Identity schema è diverso tra le app, in genere perché le app Identity usano versioni diverse, la condivisione di un database comune basato sulla Identity versione più recente di non è possibile senza rimappare e Identity aggiungere colonne negli schemi di altre app. È spesso più efficiente aggiornare le altre app per usare la versione più recente Identity , in modo che un database comune possa essere condiviso dalle app.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:host-and-deploy/web-farm>
