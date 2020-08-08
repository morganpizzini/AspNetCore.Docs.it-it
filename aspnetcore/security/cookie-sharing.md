---
title: Condividere l'autenticazione cookie tra le app ASP.NET
author: rick-anderson
description: Informazioni su come condividere l'autenticazione cookie tra ASP.NET 4. x e le app ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cookie-sharing
ms.openlocfilehash: f4762871cbae77f690d8478e1342e0d53918eb51
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022199"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a>Condividere l'autenticazione cookie tra le app ASP.NET

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)

I siti Web sono spesso costituiti da singole app Web che interagiscono. Per offrire un'esperienza di Single Sign-On (SSO), le app Web all'interno di un sito devono condividere le autenticazioni cookie . Per supportare questo scenario, lo stack di protezione dei dati consente la condivisione dell' cookie autenticazione Katana e dei cookie ticket di autenticazione ASP.NET Core.

Negli esempi seguenti:

* Il nome dell'autenticazione cookie è impostato su un valore comune di `.AspNet.SharedCookie` .
* L'oggetto `AuthenticationType` è impostato su in `Identity.Application` modo esplicito o per impostazione predefinita.
* Un nome di app comune viene usato per consentire al sistema di protezione dei dati di condividere le chiavi di protezione dei dati ( `SharedCookieApp` ).
* `Identity.Application`viene usato come schema di autenticazione. Indipendentemente dallo schema utilizzato, è necessario utilizzarlo *in modo coerente all'interno e tra* le cookie app condivise come schema predefinito o impostarlo in modo esplicito. Lo schema viene usato durante la crittografia e la decrittografia di cookie s, quindi è necessario usare uno schema coerente tra le app.
* Viene utilizzata una posizione di archiviazione della [chiave di protezione dati](xref:security/data-protection/implementation/key-management) comune.
  * In ASP.NET Core Apps <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> viene usato per impostare il percorso di archiviazione delle chiavi.
  * Nelle app .NET Framework il Cookie middleware di autenticazione usa un'implementazione di <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> . `DataProtectionProvider`fornisce servizi di protezione dei dati per la crittografia e la decrittografia dei dati del payload di autenticazione cookie . L' `DataProtectionProvider` istanza è isolata dal sistema di protezione dei dati usato da altre parti dell'app. [DataProtectionProvider. Create (System. io. DirectoryInfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accetta un oggetto <xref:System.IO.DirectoryInfo> per specificare il percorso per l'archiviazione delle chiavi di protezione dati.
* `DataProtectionProvider`richiede il pacchetto NuGet [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :
  * Nelle app ASP.NET Core 2. x fare riferimento al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).
  * In .NET Framework app aggiungere un riferimento al pacchetto a [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>imposta il nome comune dell'app.

## <a name="share-authentication-no-loccookies-with-aspnet-core-no-locidentity"></a>Condividere l'autenticazione cookie con ASP.NET CoreIdentity

Quando si usa ASP.NET Core Identity :

* Le chiavi di protezione dei dati e il nome dell'app devono essere condivise tra le app. Un percorso di archiviazione delle chiavi comune viene fornito al <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> Metodo negli esempi seguenti. Usare <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> per configurare un nome comune per l'app condivisa ( `SharedCookieApp` negli esempi seguenti). Per altre informazioni, vedere <xref:security/data-protection/configuration/overview>.
* Utilizzare il <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> metodo di estensione per configurare il servizio di protezione dei dati per cookie .
* Il tipo di autenticazione predefinito è `Identity.Application` .

In `Startup.ConfigureServices`:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-no-loccookies-without-aspnet-core-no-locidentity"></a>Condividi le autenticazioni cookie senza ASP.NET CoreIdentity

Quando si usano cookie direttamente i senza ASP.NET Core Identity , configurare la protezione dei dati e l'autenticazione in `Startup.ConfigureServices` . Nell'esempio seguente, il tipo di autenticazione è impostato su `Identity.Application` :

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

## <a name="share-no-loccookies-across-different-base-paths"></a>Condividi cookie tra percorsi di base diversi

cookiePer impostazione predefinita, un'autenticazione usa [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) [ Cookie . Percorso](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path). Se l'app cookie deve essere condivisa tra percorsi di base diversi, è `Path` necessario eseguire l'override di:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a>Condividi cookie tra i sottodomini

Quando si ospitano le app che condividono cookie s nei sottodomini, specificare un dominio comune in [ Cookie . ](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain)Proprietà di dominio. Per condividere cookie tra app in `contoso.com` , ad esempio `first_subdomain.contoso.com` e `second_subdomain.contoso.com` , specificare `Cookie.Domain` come `.contoso.com` :

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>Crittografare le chiavi di protezione dei dati inattivi

Per le distribuzioni di produzione, configurare `DataProtectionProvider` per crittografare le chiavi inattive con DPAPI o un X509Certificate. Per altre informazioni, vedere <xref:security/data-protection/implementation/key-encryption-at-rest>. Nell'esempio seguente viene fornita un'identificazione personale del certificato per <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a>Condividere l'autenticazione cookie tra ASP.NET 4. x e app ASP.NET Core

Le app ASP.NET 4. x che usano Cookie il middleware di autenticazione Katana possono essere configurate per generare le autenticazioni cookie compatibili con il Cookie middleware di autenticazione ASP.NET Core. In questo modo è possibile aggiornare le singole app di un sito di grandi dimensioni in diversi passaggi garantendo al tempo stesso un'esperienza di accesso SSO uniforme nel sito.

Quando un'app usa Cookie il middleware di autenticazione Katana, chiama `UseCookieAuthentication` nel file *Startup.auth.cs* del progetto. Per impostazione predefinita, i progetti di app Web ASP.NET 4. x creati con Visual Studio 2013 e versioni successive usano il Cookie middleware di autenticazione Katana. Sebbene `UseCookieAuthentication` sia obsoleto e non supportato per ASP.NET Core app, la chiamata `UseCookieAuthentication` in un'app ASP.NET 4. x che usa il Cookie middleware di autenticazione Katana è valida.

Un'app ASP.NET 4. x deve avere come destinazione .NET Framework 4.5.1 o versione successiva. In caso contrario, non è possibile installare i pacchetti NuGet necessari.

Per condividere l'autenticazione cookie tra un'app ASP.NET 4. x e un'app ASP.NET Core, configurare l'app ASP.NET Core come indicato nella sezione [autenticazione di condivisione cookie tra ASP.NET Core app](#share-authentication-cookies-with-aspnet-core-identity) , quindi configurare l'app ASP.NET 4. x come indicato di seguito.

Verificare che i pacchetti dell'app vengano aggiornati alle versioni più recenti. Installare il pacchetto [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) in ogni app ASP.NET 4. x.

Individuare e modificare la chiamata a `UseCookieAuthentication` :

* Modificare il cookie nome in modo che corrisponda al nome usato dal Cookie middleware di autenticazione ASP.NET Core ( `.AspNet.SharedCookie` nell'esempio).
* Nell'esempio seguente, il tipo di autenticazione è impostato su `Identity.Application` .
* Fornire un'istanza di un oggetto `DataProtectionProvider` inizializzato al percorso di archiviazione della chiave di protezione dati comune.
* Verificare che il nome dell'app sia impostato sul nome dell'app comune usato da tutte le app che condividono l'autenticazione cookie ( `SharedCookieApp` nell'esempio).

Se non `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` si imposta e `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` , impostare <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> su un'attestazione che distingue gli utenti univoci.

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

Quando si genera un'identità utente, il tipo di autenticazione ( `Identity.Application` ) deve corrispondere al tipo definito in `AuthenticationType` set con `UseCookieAuthentication` in *app_start/Startup.auth.cs*.

*Modelli/ Identity Models.cs*:

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

Quando le app usano lo stesso Identity schema (stessa versione di Identity ), verificare che il Identity sistema per ogni app punti allo stesso database utente. In caso contrario, il sistema di identità genera errori in fase di esecuzione quando tenta di trovare una corrispondenza tra le informazioni nell'autenticazione e cookie quelle del relativo database.

Quando lo Identity schema è diverso tra le app, in genere perché le app usano Identity versioni diverse, la condivisione di un database comune basato sulla versione più recente di Identity non è possibile senza rimappare e aggiungere colonne negli schemi di altre app Identity . È spesso più efficiente aggiornare le altre app per usare la versione più recente, in Identity modo che un database comune possa essere condiviso dalle app.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:host-and-deploy/web-farm>
