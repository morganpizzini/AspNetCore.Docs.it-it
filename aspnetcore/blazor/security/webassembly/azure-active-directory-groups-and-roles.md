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
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="ca8fd-103">Gruppi di Azure AD, ruoli amministrativi e ruoli definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="ca8fd-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="ca8fd-104">Di [Luke Latham](https://github.com/guardrex) e [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="ca8fd-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="ca8fd-105">Azure Active Directory (AAD) fornisce diversi approcci di autorizzazione che possono essere combinati con ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="ca8fd-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="ca8fd-106">Gruppi definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="ca8fd-106">User-defined groups</span></span>
  * <span data-ttu-id="ca8fd-107">Sicurezza</span><span class="sxs-lookup"><span data-stu-id="ca8fd-107">Security</span></span>
  * <span data-ttu-id="ca8fd-108">O365</span><span class="sxs-lookup"><span data-stu-id="ca8fd-108">O365</span></span>
  * <span data-ttu-id="ca8fd-109">Distribuzione</span><span class="sxs-lookup"><span data-stu-id="ca8fd-109">Distribution</span></span>
* <span data-ttu-id="ca8fd-110">Ruoli</span><span class="sxs-lookup"><span data-stu-id="ca8fd-110">Roles</span></span>
  * <span data-ttu-id="ca8fd-111">Ruoli amministrativi predefiniti</span><span class="sxs-lookup"><span data-stu-id="ca8fd-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="ca8fd-112">Ruoli definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="ca8fd-112">User-defined roles</span></span>

<span data-ttu-id="ca8fd-113">Le indicazioni fornite in questo articolo sono valide per gli Blazor scenari di distribuzione di AAD di webassembly descritti negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="ca8fd-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="ca8fd-114">App autonoma con account Microsoft</span><span class="sxs-lookup"><span data-stu-id="ca8fd-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="ca8fd-115">App autonoma con AAD</span><span class="sxs-lookup"><span data-stu-id="ca8fd-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="ca8fd-116">App ospitata con AAD</span><span class="sxs-lookup"><span data-stu-id="ca8fd-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="ca8fd-117">Gruppi definiti dall'utente e ruoli amministrativi predefiniti</span><span class="sxs-lookup"><span data-stu-id="ca8fd-117">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="ca8fd-118">Per configurare l'app nella portale di Azure per fornire un' `groups` attestazione di appartenenza, vedere gli articoli seguenti su Azure.</span><span class="sxs-lookup"><span data-stu-id="ca8fd-118">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="ca8fd-119">Assegnare gli utenti ai gruppi AAD definiti dall'utente e ai ruoli amministrativi predefiniti.</span><span class="sxs-lookup"><span data-stu-id="ca8fd-119">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="ca8fd-120">Definizione dei ruoli con i gruppi di sicurezza di Azure AD</span><span class="sxs-lookup"><span data-stu-id="ca8fd-120">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="ca8fd-121">`groupMembershipClaims`attributo</span><span class="sxs-lookup"><span data-stu-id="ca8fd-121">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="ca8fd-122">Gli esempi seguenti presuppongono che un utente sia assegnato al ruolo di *amministratore fatturazione* incorporato di AAD.</span><span class="sxs-lookup"><span data-stu-id="ca8fd-122">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="ca8fd-123">La singola `groups` attestazione inviata da AAD presenta i gruppi e i ruoli dell'utente come ID oggetto (Guid) in una matrice JSON.</span><span class="sxs-lookup"><span data-stu-id="ca8fd-123">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="ca8fd-124">L'app deve convertire la matrice JSON di gruppi e ruoli in singole `group` attestazioni in base a cui l'app può compilare i [criteri](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="ca8fd-124">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="ca8fd-125">Estendi <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> per includere le proprietà della matrice per gruppi e ruoli.</span><span class="sxs-lookup"><span data-stu-id="ca8fd-125">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span>

<span data-ttu-id="ca8fd-126">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="ca8fd-126">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="ca8fd-127">Creare una factory utente personalizzata nell'app autonoma o nell'app client di una soluzione ospitata.</span><span class="sxs-lookup"><span data-stu-id="ca8fd-127">Create a custom user factory in the standalone app or Client app of a Hosted solution.</span></span> <span data-ttu-id="ca8fd-128">La Factory seguente viene configurata anche per gestire le `roles` matrici di attestazioni, descritte nella sezione [ruoli definiti dall'utente](#user-defined-roles) :</span><span class="sxs-lookup"><span data-stu-id="ca8fd-128">The following factory is also configured to handle `roles` claim arrays, which are covered in the [User-defined roles](#user-defined-roles) section:</span></span>

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

<span data-ttu-id="ca8fd-129">Non è necessario fornire codice per rimuovere l' `groups` attestazione originale perché viene automaticamente rimossa dal Framework.</span><span class="sxs-lookup"><span data-stu-id="ca8fd-129">There's no need to provide code to remove the original `groups` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="ca8fd-130">Registrare la factory in `Program.Main` ( `Program.cs` ) dell'app autonoma o dell'app client di una soluzione ospitata:</span><span class="sxs-lookup"><span data-stu-id="ca8fd-130">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or Client app of a Hosted solution:</span></span>

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

<span data-ttu-id="ca8fd-131">Creare un [criterio](xref:security/authorization/policies) per ogni gruppo o ruolo in `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="ca8fd-131">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="ca8fd-132">L'esempio seguente crea un criterio per il ruolo di *amministratore fatturazione* incorporato di AAD:</span><span class="sxs-lookup"><span data-stu-id="ca8fd-132">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="ca8fd-133">Per l'elenco completo degli ID oggetto ruolo AAD, vedere la sezione [ID del gruppo di ruoli di AAD Adminstrative](#aad-adminstrative-role-group-ids) .</span><span class="sxs-lookup"><span data-stu-id="ca8fd-133">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="ca8fd-134">Negli esempi seguenti l'app usa il criterio precedente per autorizzare l'utente.</span><span class="sxs-lookup"><span data-stu-id="ca8fd-134">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="ca8fd-135">Il [ `AuthorizeView` componente](xref:blazor/security/index#authorizeview-component) funziona con i criteri:</span><span class="sxs-lookup"><span data-stu-id="ca8fd-135">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="ca8fd-136">L'accesso a un intero componente può essere basato sui criteri che usano la [ `[Authorize]` direttiva attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):</span><span class="sxs-lookup"><span data-stu-id="ca8fd-136">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="ca8fd-137">Se l'utente non è connesso, viene reindirizzato alla pagina di accesso di AAD e quindi di nuovo al componente dopo l'accesso.</span><span class="sxs-lookup"><span data-stu-id="ca8fd-137">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="ca8fd-138">Un controllo dei criteri può essere [eseguito anche nel codice con la logica procedurale](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="ca8fd-138">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

### <a name="user-defined-roles"></a><span data-ttu-id="ca8fd-139">Ruoli definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="ca8fd-139">User-defined roles</span></span>

<span data-ttu-id="ca8fd-140">È anche possibile configurare un'app registrata da AAD per usare i ruoli definiti dall'utente.</span><span class="sxs-lookup"><span data-stu-id="ca8fd-140">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="ca8fd-141">Per configurare l'app nella portale di Azure per fornire un' `roles` attestazione di appartenenza, vedere [procedura: aggiungere i ruoli dell'app nell'applicazione e riceverli nel token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) nella documentazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="ca8fd-141">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="ca8fd-142">Nell'esempio seguente si presuppone che un'app sia configurata con due ruoli:</span><span class="sxs-lookup"><span data-stu-id="ca8fd-142">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="ca8fd-143">Sebbene non sia possibile assegnare ruoli a gruppi di sicurezza senza un account Azure AD Premium, è possibile assegnare utenti ai ruoli e ricevere un' `roles` attestazione per gli utenti con un account Azure standard.</span><span class="sxs-lookup"><span data-stu-id="ca8fd-143">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="ca8fd-144">Le indicazioni fornite in questa sezione non richiedono un account Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="ca8fd-144">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="ca8fd-145">Nel portale di Azure vengono assegnati più ruoli aggiungendo **_nuovamente un utente_** per ogni assegnazione di ruolo aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="ca8fd-145">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="ca8fd-146">La singola `roles` attestazione inviata da AAD presenta i ruoli definiti dall'utente come `appRoles` `value` s in una matrice JSON.</span><span class="sxs-lookup"><span data-stu-id="ca8fd-146">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="ca8fd-147">L'app deve convertire la matrice JSON dei ruoli in singole `role` attestazioni.</span><span class="sxs-lookup"><span data-stu-id="ca8fd-147">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="ca8fd-148">Il `CustomUserFactory` illustrato nella sezione [gruppi definiti dall'utente e ruoli amministrativi predefiniti di AAD](#user-defined-groups-and-built-in-administrative-roles) è configurato per agire su un' `roles` attestazione con un valore di matrice JSON.</span><span class="sxs-lookup"><span data-stu-id="ca8fd-148">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="ca8fd-149">Aggiungere e registrare `CustomUserFactory` nell'app autonoma o client di una soluzione ospitata, come illustrato nella sezione [gruppi definiti dall'utente e ruoli amministrativi predefiniti di AAD](#user-defined-groups-and-built-in-administrative-roles) .</span><span class="sxs-lookup"><span data-stu-id="ca8fd-149">Add and register the `CustomUserFactory` in the standalone app or Client app of a Hosted solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="ca8fd-150">Non è necessario fornire codice per rimuovere l' `roles` attestazione originale perché viene automaticamente rimossa dal Framework.</span><span class="sxs-lookup"><span data-stu-id="ca8fd-150">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="ca8fd-151">Nell'app `Program.Main` autonoma o nell'app client di una soluzione ospitata specificare l'attestazione denominata " `role` " come attestazione del ruolo:</span><span class="sxs-lookup"><span data-stu-id="ca8fd-151">In `Program.Main` of the standalone app or Client app of a Hosted solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="ca8fd-152">A questo punto sono funzionali gli approcci di autorizzazione per i componenti.</span><span class="sxs-lookup"><span data-stu-id="ca8fd-152">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="ca8fd-153">Qualsiasi meccanismo di autorizzazione nei componenti può utilizzare il `admin` ruolo per autorizzare l'utente:</span><span class="sxs-lookup"><span data-stu-id="ca8fd-153">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="ca8fd-154">[ `AuthorizeView` Component](xref:blazor/security/index#authorizeview-component) (esempio: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="ca8fd-154">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="ca8fd-155">[ `[Authorize]` direttiva attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (esempio: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="ca8fd-155">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="ca8fd-156">[Logica procedurale](xref:blazor/security/index#procedural-logic) (esempio: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="ca8fd-156">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="ca8fd-157">Sono supportati più test di ruolo:</span><span class="sxs-lookup"><span data-stu-id="ca8fd-157">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="ca8fd-158">ID gruppo di ruoli di AAD Adminstrative</span><span class="sxs-lookup"><span data-stu-id="ca8fd-158">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="ca8fd-159">Gli ID oggetto presentati nella tabella seguente vengono usati per creare [criteri](xref:security/authorization/policies) per le `group` attestazioni.</span><span class="sxs-lookup"><span data-stu-id="ca8fd-159">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="ca8fd-160">I criteri consentono a un'app di autorizzare gli utenti per varie attività in un'app.</span><span class="sxs-lookup"><span data-stu-id="ca8fd-160">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="ca8fd-161">Per altre informazioni, vedere la sezione [gruppi definiti dall'utente e ruoli amministrativi predefiniti di AAD](#user-defined-groups-and-built-in-administrative-roles) .</span><span class="sxs-lookup"><span data-stu-id="ca8fd-161">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="ca8fd-162">Ruolo amministrativo AAD</span><span class="sxs-lookup"><span data-stu-id="ca8fd-162">AAD Administrative Role</span></span> | <span data-ttu-id="ca8fd-163">ID dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="ca8fd-163">Object ID</span></span>
--- | ---
<span data-ttu-id="ca8fd-164">Amministratore di applicazioni</span><span class="sxs-lookup"><span data-stu-id="ca8fd-164">Application administrator</span></span> | <span data-ttu-id="ca8fd-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="ca8fd-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="ca8fd-166">Sviluppatore di applicazioni</span><span class="sxs-lookup"><span data-stu-id="ca8fd-166">Application developer</span></span> | <span data-ttu-id="ca8fd-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="ca8fd-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="ca8fd-168">Amministratore dell'autenticazione</span><span class="sxs-lookup"><span data-stu-id="ca8fd-168">Authentication administrator</span></span> | <span data-ttu-id="ca8fd-169">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="ca8fd-169">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="ca8fd-170">Amministratore di Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="ca8fd-170">Azure DevOps administrator</span></span> | <span data-ttu-id="ca8fd-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="ca8fd-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="ca8fd-172">Amministratore di Azure Information Protection</span><span class="sxs-lookup"><span data-stu-id="ca8fd-172">Azure Information Protection administrator</span></span> | <span data-ttu-id="ca8fd-173">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="ca8fd-173">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="ca8fd-174">Amministratore keyset Framework dell'esperienza B2C</span><span class="sxs-lookup"><span data-stu-id="ca8fd-174">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="ca8fd-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="ca8fd-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="ca8fd-176">Amministratore criteri B2C Framework dell'esperienza</span><span class="sxs-lookup"><span data-stu-id="ca8fd-176">B2C IEF Policy administrator</span></span> | <span data-ttu-id="ca8fd-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="ca8fd-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="ca8fd-178">Amministratore flusso utente B2C</span><span class="sxs-lookup"><span data-stu-id="ca8fd-178">B2C user flow administrator</span></span> | <span data-ttu-id="ca8fd-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="ca8fd-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="ca8fd-180">Amministratore attributi flusso utente B2C</span><span class="sxs-lookup"><span data-stu-id="ca8fd-180">B2C user flow attribute administrator</span></span> | <span data-ttu-id="ca8fd-181">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="ca8fd-181">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="ca8fd-182">Amministratore fatturazione</span><span class="sxs-lookup"><span data-stu-id="ca8fd-182">Billing administrator</span></span> | <span data-ttu-id="ca8fd-183">69ff516a-B57D-4697-A429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="ca8fd-183">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="ca8fd-184">Amministratore di applicazioni cloud</span><span class="sxs-lookup"><span data-stu-id="ca8fd-184">Cloud application administrator</span></span> | <span data-ttu-id="ca8fd-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="ca8fd-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="ca8fd-186">Amministratore dispositivo cloud</span><span class="sxs-lookup"><span data-stu-id="ca8fd-186">Cloud device administrator</span></span> | <span data-ttu-id="ca8fd-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="ca8fd-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="ca8fd-188">Amministratore di conformità</span><span class="sxs-lookup"><span data-stu-id="ca8fd-188">Compliance administrator</span></span> | <span data-ttu-id="ca8fd-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="ca8fd-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="ca8fd-190">Amministratore dei dati sulla conformità</span><span class="sxs-lookup"><span data-stu-id="ca8fd-190">Compliance data administrator</span></span> | <span data-ttu-id="ca8fd-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="ca8fd-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="ca8fd-192">Amministratore di accesso condizionale</span><span class="sxs-lookup"><span data-stu-id="ca8fd-192">Conditional Access administrator</span></span> | <span data-ttu-id="ca8fd-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="ca8fd-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="ca8fd-194">Responsabile approvazione per l'accesso a Customer Lockbox</span><span class="sxs-lookup"><span data-stu-id="ca8fd-194">Customer LockBox access approver</span></span> | <span data-ttu-id="ca8fd-195">c18d54a8-B13E-4954-A1A4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="ca8fd-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="ca8fd-196">Amministratore di analisi desktop</span><span class="sxs-lookup"><span data-stu-id="ca8fd-196">Desktop Analytics administrator</span></span> | <span data-ttu-id="ca8fd-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="ca8fd-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="ca8fd-198">Ruoli con autorizzazioni di lettura nella directory</span><span class="sxs-lookup"><span data-stu-id="ca8fd-198">Directory readers</span></span> | <span data-ttu-id="ca8fd-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="ca8fd-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="ca8fd-200">Amministratore di Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="ca8fd-200">Dynamics 365 administrator</span></span> | <span data-ttu-id="ca8fd-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="ca8fd-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="ca8fd-202">Amministratore di Exchange</span><span class="sxs-lookup"><span data-stu-id="ca8fd-202">Exchange administrator</span></span> | <span data-ttu-id="ca8fd-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="ca8fd-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="ca8fd-204">IdentityAmministratore provider esterno</span><span class="sxs-lookup"><span data-stu-id="ca8fd-204">External Identity Provider administrator</span></span> | <span data-ttu-id="ca8fd-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="ca8fd-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="ca8fd-206">Amministratore globale</span><span class="sxs-lookup"><span data-stu-id="ca8fd-206">Global administrator</span></span> | <span data-ttu-id="ca8fd-207">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="ca8fd-207">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="ca8fd-208">Ruolo con autorizzazioni di lettura globali</span><span class="sxs-lookup"><span data-stu-id="ca8fd-208">Global reader</span></span> | <span data-ttu-id="ca8fd-209">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="ca8fd-209">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="ca8fd-210">Amministratore di gruppi</span><span class="sxs-lookup"><span data-stu-id="ca8fd-210">Groups administrator</span></span> | <span data-ttu-id="ca8fd-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="ca8fd-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="ca8fd-212">Mittente dell'invito guest</span><span class="sxs-lookup"><span data-stu-id="ca8fd-212">Guest inviter</span></span> | <span data-ttu-id="ca8fd-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="ca8fd-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="ca8fd-214">Amministratore dell'help desk</span><span class="sxs-lookup"><span data-stu-id="ca8fd-214">Helpdesk administrator</span></span> | <span data-ttu-id="ca8fd-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="ca8fd-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="ca8fd-216">Amministratore di Intune</span><span class="sxs-lookup"><span data-stu-id="ca8fd-216">Intune administrator</span></span> | <span data-ttu-id="ca8fd-217">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="ca8fd-217">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="ca8fd-218">Amministratore di Kaizala</span><span class="sxs-lookup"><span data-stu-id="ca8fd-218">Kaizala administrator</span></span> | <span data-ttu-id="ca8fd-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="ca8fd-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="ca8fd-220">Amministratore licenze</span><span class="sxs-lookup"><span data-stu-id="ca8fd-220">License administrator</span></span> | <span data-ttu-id="ca8fd-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="ca8fd-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="ca8fd-222">Ruolo con autorizzazioni di lettura per la privacy del Centro messaggi</span><span class="sxs-lookup"><span data-stu-id="ca8fd-222">Message center privacy reader</span></span> | <span data-ttu-id="ca8fd-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="ca8fd-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="ca8fd-224">Ruolo con autorizzazioni di lettura per il Centro messaggi</span><span class="sxs-lookup"><span data-stu-id="ca8fd-224">Message center reader</span></span> | <span data-ttu-id="ca8fd-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="ca8fd-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="ca8fd-226">Amministratore delle app di Office</span><span class="sxs-lookup"><span data-stu-id="ca8fd-226">Office apps administrator</span></span> | <span data-ttu-id="ca8fd-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="ca8fd-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="ca8fd-228">Amministratore password</span><span class="sxs-lookup"><span data-stu-id="ca8fd-228">Password administrator</span></span> | <span data-ttu-id="ca8fd-229">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="ca8fd-229">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="ca8fd-230">Amministratore di Power BI</span><span class="sxs-lookup"><span data-stu-id="ca8fd-230">Power BI administrator</span></span> | <span data-ttu-id="ca8fd-231">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="ca8fd-231">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="ca8fd-232">Amministratore di Power Platform</span><span class="sxs-lookup"><span data-stu-id="ca8fd-232">Power platform administrator</span></span> | <span data-ttu-id="ca8fd-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="ca8fd-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="ca8fd-234">Amministratore autenticazione con privilegi</span><span class="sxs-lookup"><span data-stu-id="ca8fd-234">Privileged authentication administrator</span></span> | <span data-ttu-id="ca8fd-235">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="ca8fd-235">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="ca8fd-236">Amministratore dei ruoli con privilegi</span><span class="sxs-lookup"><span data-stu-id="ca8fd-236">Privileged role administrator</span></span> | <span data-ttu-id="ca8fd-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="ca8fd-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="ca8fd-238">Lettore di report</span><span class="sxs-lookup"><span data-stu-id="ca8fd-238">Reports reader</span></span> | <span data-ttu-id="ca8fd-239">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="ca8fd-239">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="ca8fd-240">Amministratore della ricerca</span><span class="sxs-lookup"><span data-stu-id="ca8fd-240">Search administrator</span></span> | <span data-ttu-id="ca8fd-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="ca8fd-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="ca8fd-242">Editor della ricerca</span><span class="sxs-lookup"><span data-stu-id="ca8fd-242">Search editor</span></span> | <span data-ttu-id="ca8fd-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="ca8fd-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="ca8fd-244">Amministratore della sicurezza</span><span class="sxs-lookup"><span data-stu-id="ca8fd-244">Security administrator</span></span> | <span data-ttu-id="ca8fd-245">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="ca8fd-245">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="ca8fd-246">Operatore per la sicurezza</span><span class="sxs-lookup"><span data-stu-id="ca8fd-246">Security operator</span></span> | <span data-ttu-id="ca8fd-247">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="ca8fd-247">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="ca8fd-248">Ruolo con autorizzazioni di lettura per la sicurezza</span><span class="sxs-lookup"><span data-stu-id="ca8fd-248">Security reader</span></span> | <span data-ttu-id="ca8fd-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="ca8fd-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="ca8fd-250">Amministratore del supporto per il servizio</span><span class="sxs-lookup"><span data-stu-id="ca8fd-250">Service support administrator</span></span> | <span data-ttu-id="ca8fd-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="ca8fd-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="ca8fd-252">Amministratore di SharePoint</span><span class="sxs-lookup"><span data-stu-id="ca8fd-252">SharePoint administrator</span></span> | <span data-ttu-id="ca8fd-253">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="ca8fd-253">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="ca8fd-254">Amministratore di Skype for Business</span><span class="sxs-lookup"><span data-stu-id="ca8fd-254">Skype for Business administrator</span></span> | <span data-ttu-id="ca8fd-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="ca8fd-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="ca8fd-256">Amministratore comunicazioni Teams</span><span class="sxs-lookup"><span data-stu-id="ca8fd-256">Teams Communications Administrator</span></span> | <span data-ttu-id="ca8fd-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="ca8fd-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="ca8fd-258">Tecnico di supporto comunicazioni Teams</span><span class="sxs-lookup"><span data-stu-id="ca8fd-258">Teams Communications Support Engineer</span></span> | <span data-ttu-id="ca8fd-259">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="ca8fd-259">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="ca8fd-260">Specialista comunicazione Teams</span><span class="sxs-lookup"><span data-stu-id="ca8fd-260">Teams Communications Specialist</span></span> | <span data-ttu-id="ca8fd-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="ca8fd-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="ca8fd-262">Amministratore del servizio Teams</span><span class="sxs-lookup"><span data-stu-id="ca8fd-262">Teams Service Administrator</span></span> | <span data-ttu-id="ca8fd-263">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="ca8fd-263">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="ca8fd-264">Amministratore utenti</span><span class="sxs-lookup"><span data-stu-id="ca8fd-264">User administrator</span></span> | <span data-ttu-id="ca8fd-265">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="ca8fd-265">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ca8fd-266">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="ca8fd-266">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
