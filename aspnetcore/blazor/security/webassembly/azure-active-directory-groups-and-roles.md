---
title: ASP.NET Core Blazor webassembly con gruppi e ruoli di Azure Active Directory
author: guardrex
description: Informazioni su come configurare Blazor webassembly per l'uso di Azure Active Directory gruppi e ruoli.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: ed49ba13842f2b5805250d8c12535397c542cfd4
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242875"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a>Gruppi di Azure AD, ruoli amministrativi e ruoli definiti dall'utente

Di [Luke Latham](https://github.com/guardrex) e [Javier Calvarro Nelson](https://github.com/javiercn)

Azure Active Directory (AAD) fornisce diversi approcci di autorizzazione che possono essere combinati con ASP.NET Core Identity :

* Gruppi definiti dall'utente
  * Sicurezza
  * O365
  * Distribuzione
* Ruoli
  * Ruoli amministrativi predefiniti
  * Ruoli definiti dall'utente

Le indicazioni fornite in questo articolo sono valide per gli Blazor scenari di distribuzione di AAD di webassembly descritti negli argomenti seguenti:

* [App autonoma con account Microsoft](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [App autonoma con AAD](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [App ospitata con AAD](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a>Gruppi definiti dall'utente e ruoli amministrativi predefiniti

Per configurare l'app nella portale di Azure per fornire un' `groups` attestazione di appartenenza, vedere gli articoli seguenti su Azure. Assegnare gli utenti ai gruppi AAD definiti dall'utente e ai ruoli amministrativi predefiniti.

* [Definizione dei ruoli con i gruppi di sicurezza di Azure AD](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [`groupMembershipClaims`attributo](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

Gli esempi seguenti presuppongono che un utente sia assegnato al ruolo di *amministratore fatturazione* incorporato di AAD.

La singola `groups` attestazione inviata da AAD presenta i gruppi e i ruoli dell'utente come ID oggetto (Guid) in una matrice JSON. L'app deve convertire la matrice JSON di gruppi e ruoli in singole `group` attestazioni in base a cui l'app può compilare i [criteri](xref:security/authorization/policies) .

Estendi <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> per includere le proprietà della matrice per gruppi e ruoli.

`CustomUserAccount.cs`:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; }

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; }
}
```

Creare una factory utente personalizzata nell'app autonoma o nell'app client di una soluzione ospitata. La Factory seguente viene configurata anche per gestire le `roles` matrici di attestazioni, descritte nella sezione [ruoli definiti dall'utente](#user-defined-roles) :

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomUserFactory(NavigationManager navigationManager,
        IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            foreach (var group in account.Groups)
            {
                userIdentity.AddClaim(new Claim("group", group));
            }
        }

        return initialUser;
    }
}
```

Non è necessario fornire codice per rimuovere l' `groups` attestazione originale perché viene automaticamente rimossa dal Framework.

Registrare la factory in `Program.Main` ( `Program.cs` ) dell'app autonoma o dell'app client di una soluzione ospitata:

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");
    
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

Creare un [criterio](xref:security/authorization/policies) per ogni gruppo o ruolo in `Program.Main` . L'esempio seguente crea un criterio per il ruolo di *amministratore fatturazione* incorporato di AAD:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Per l'elenco completo degli ID oggetto ruolo AAD, vedere la sezione [ID del gruppo di ruoli di AAD Adminstrative](#aad-adminstrative-role-group-ids) .

Negli esempi seguenti l'app usa il criterio precedente per autorizzare l'utente.

Il [ `AuthorizeView` componente](xref:blazor/security/index#authorizeview-component) funziona con i criteri:

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrative Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

L'accesso a un intero componente può essere basato sui criteri che usano la [ `[Authorize]` direttiva attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Se l'utente non è connesso, viene reindirizzato alla pagina di accesso di AAD e quindi di nuovo al componente dopo l'accesso.

Un controllo dei criteri può essere [eseguito anche nel codice con la logica procedurale](xref:blazor/security/index#procedural-logic):

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

### <a name="user-defined-roles"></a>Ruoli definiti dall'utente

È anche possibile configurare un'app registrata da AAD per usare i ruoli definiti dall'utente.

Per configurare l'app nella portale di Azure per fornire un' `roles` attestazione di appartenenza, vedere [procedura: aggiungere i ruoli dell'app nell'applicazione e riceverli nel token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) nella documentazione di Azure.

Nell'esempio seguente si presuppone che un'app sia configurata con due ruoli:

* `admin`
* `developer`

> [!NOTE]
> Sebbene non sia possibile assegnare ruoli a gruppi di sicurezza senza un account Azure AD Premium, è possibile assegnare utenti ai ruoli e ricevere un' `roles` attestazione per gli utenti con un account Azure standard. Le indicazioni fornite in questa sezione non richiedono un account Azure AD Premium.
>
> Nel portale di Azure vengono assegnati più ruoli aggiungendo **_nuovamente un utente_** per ogni assegnazione di ruolo aggiuntiva.

La singola `roles` attestazione inviata da AAD presenta i ruoli definiti dall'utente come `appRoles` `value` s in una matrice JSON. L'app deve convertire la matrice JSON dei ruoli in singole `role` attestazioni.

Il `CustomUserFactory` illustrato nella sezione [gruppi definiti dall'utente e ruoli amministrativi predefiniti di AAD](#user-defined-groups-and-built-in-administrative-roles) è configurato per agire su un' `roles` attestazione con un valore di matrice JSON. Aggiungere e registrare `CustomUserFactory` nell'app autonoma o client di una soluzione ospitata, come illustrato nella sezione [gruppi definiti dall'utente e ruoli amministrativi predefiniti di AAD](#user-defined-groups-and-built-in-administrative-roles) . Non è necessario fornire codice per rimuovere l' `roles` attestazione originale perché viene automaticamente rimossa dal Framework.

Nell'app `Program.Main` autonoma o nell'app client di una soluzione ospitata specificare l'attestazione denominata " `role` " come attestazione del ruolo:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

A questo punto sono funzionali gli approcci di autorizzazione per i componenti. Qualsiasi meccanismo di autorizzazione nei componenti può utilizzare il `admin` ruolo per autorizzare l'utente:

* [ `AuthorizeView` Component](xref:blazor/security/index#authorizeview-component) (esempio: `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` direttiva attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (esempio: `@attribute [Authorize(Roles = "admin")]` )
* [Logica procedurale](xref:blazor/security/index#procedural-logic) (esempio: `if (user.IsInRole("admin")) { ... }` )

  Sono supportati più test di ruolo:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a>ID gruppo di ruoli di AAD Adminstrative

Gli ID oggetto presentati nella tabella seguente vengono usati per creare [criteri](xref:security/authorization/policies) per le `group` attestazioni. I criteri consentono a un'app di autorizzare gli utenti per varie attività in un'app. Per altre informazioni, vedere la sezione [gruppi definiti dall'utente e ruoli amministrativi predefiniti di AAD](#user-defined-groups-and-built-in-administrative-roles) .

Ruolo amministrativo AAD | ID dell'oggetto.
--- | ---
Amministratore di applicazioni | fa11557b-4f15-4ddd-85d5-313c7cd74047
Sviluppatore di applicazioni | 68adcbb8-9504-44f6-89f2-5cd48dc74a2c
Amministratore dell'autenticazione | 02d110a1-96b1-419e-af87-746461b60ed7
Amministratore di Azure DevOps | a5311ace-ca41-44cd-b833-8d22caa0b34f
Amministratore di Azure Information Protection | 18632dce-f9b5-4f01-abb5-37051f06860e
Amministratore keyset Framework dell'esperienza B2C | 0c2e87e5-94f9-4adb-ae8c-bcafe11bd368
Amministratore criteri B2C Framework dell'esperienza | bfcab36c-10c6-4b13-b63c-4d8b62c0c44e
Amministratore flusso utente B2C | baa531b7-8cf0-44ad-8f98-eded88dae827
Amministratore attributi flusso utente B2C | dd0baca0-a535-48c1-b871-8431abe16452
Amministratore fatturazione | 69ff516a-B57D-4697-A429-9de4af7b5609
Amministratore di applicazioni cloud | 250b5fe3-b553-458d-9a53-b782c13c34bf
Amministratore dispositivo cloud | 26cd4b44-2636-4ddb-bdfa-27feae66f86d
Amministratore di conformità | 9d6e1dd0-c9f8-45f8-b558-b134f700116c
Amministratore dei dati sulla conformità | 4c0ca3a2-231e-416c-9411-4abe57d5cb9d
Amministratore di accesso condizionale | 8f71a611-137d-49af-87ad-e97f1fd5da76
Responsabile approvazione per l'accesso a Customer Lockbox | c18d54a8-B13E-4954-A1A4-7deaf2e4f184
Amministratore di analisi desktop | c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15
Ruoli con autorizzazioni di lettura nella directory | e1fc84a6-7762-4b9b-8e29-518b4adbc23b
Amministratore di Dynamics 365 | f20a9cfa-9fdf-49a8-a977-1afe446a1d6e
Amministratore di Exchange | b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652
IdentityAmministratore provider esterno | febfaeb4-e478-407a-b4b3-f4d9716618a2
Amministratore globale | a45ba61b-44db-462c-924b-3b2719152588
Ruolo con autorizzazioni di lettura globali | f6903b21-6aba-4124-b44c-76671796b9d5
Amministratore di gruppi | 158b3e5a-d89d-460b-92b5-3b34985f0197
Mittente dell'invito guest | 4c730a1d-cc22-44af-8f9f-4eec635c7502
Amministratore dell'help desk | 108678c8-6628-44e1-8d01-caf598a6a5f5
Amministratore di Intune | 79950741-23fa-4189-b2cb-46640601c497
Amministratore di Kaizala | d6322af2-48e7-42e0-8c68-0bbe31af3412
Amministratore licenze | 3355458a-e423-44bf-8b98-4ac5e572cea5
Ruolo con autorizzazioni di lettura per la privacy del Centro messaggi | 6395db95-9fb8-42b9-b1ed-30a2405eee6f
Ruolo con autorizzazioni di lettura per il Centro messaggi | fd5d37b8-4e24-434b-9e63-70ed3b759a16
Amministratore delle app di Office | 5f3870cd-b042-4f93-86d7-c9d77c664dc7
Amministratore password | 466e48b7-5d66-4ae5-8911-1a118de74941
Amministratore di Power BI | 984e83b8-8337-4255-91a1-acb663175ab4
Amministratore di Power Platform | 76d6f95e-9a15-4d7d-8d21-00de00faf9fd
Amministratore autenticazione con privilegi | 0829f731-b46d-419f-9742-aeb122367d11
Amministratore dei ruoli con privilegi | f20a725a-d1c8-4107-83ea-1171c97d00c7
Lettore di report | 54635450-e8ed-4f2d-9632-07db2517b4de
Amministratore della ricerca | c770a2f1-c9ba-4e60-9176-9f52b1eb1a31
Editor della ricerca | 6a6858c6-5f0d-44ac-87c7-0190fbedd271
Amministratore della sicurezza | 20fa50e3-6531-44d8-bd39-b251420568ad
Operatore per la sicurezza | 43aae017-8e51-4188-91ab-e6debd572800
Ruolo con autorizzazioni di lettura per la sicurezza | 45035cd3-fd97-4250-8197-3a53d3562d9b
Amministratore del supporto per il servizio | 2c92cf45-c914-48f8-9bf9-fc14b28818ab
Amministratore di SharePoint | e1c32229-875e-461d-ae24-3cb99116e86c
Amministratore di Skype for Business | 0a8cee12-e21d-43ef-abd9-f1ea85710e30
Amministratore comunicazioni Teams | 2393e455-6e13-4743-9f52-63fcec2b6a9c
Tecnico di supporto comunicazioni Teams | 802dd94e-d717-46f6-af98-b9167071e9fc
Specialista comunicazione Teams | ef547281-cf46-4cc6-bcaa-f5eac3f030c9
Amministratore del servizio Teams | 8846a0be-197b-443a-b13c-11192691fa24
Amministratore utenti | 1f6eed58-7dd3-460b-a298-666f975427a1

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
