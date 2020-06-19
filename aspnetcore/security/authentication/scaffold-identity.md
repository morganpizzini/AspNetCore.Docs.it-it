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
ms.openlocfilehash: f3314458a504af7f44dcdc276de890fa9485a2b3
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103032"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="21265-103">Impalcatura Identity nei progetti ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21265-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="21265-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="21265-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="21265-105">ASP.NET Core fornisce [ASP.NET Core Identity ](xref:security/authentication/identity) come [ Razor libreria di classi](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="21265-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="21265-106">Le applicazioni che includono Identity possono applicare l'impalcatura per aggiungere selettivamente il codice sorgente contenuto nella Identity Razor libreria di classi (RCL).</span><span class="sxs-lookup"><span data-stu-id="21265-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="21265-107">Se si vuole generare un codice sorgente, è possibile modificare il codice e modificarne il comportamento.</span><span class="sxs-lookup"><span data-stu-id="21265-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="21265-108">Ad esempio, è possibile indicare allo scaffolder di generare il codice usato nella registrazione.</span><span class="sxs-lookup"><span data-stu-id="21265-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="21265-109">Il codice generato ha la precedenza sullo stesso codice in Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="21265-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="21265-110">Per ottenere il controllo completo dell'interfaccia utente e non usare il RCL predefinito, vedere la sezione [creare un'origine completa dell' Identity interfaccia utente](#full).</span><span class="sxs-lookup"><span data-stu-id="21265-110">To gain full control of the UI and not use the default RCL, see the section [Create full Identity UI source](#full).</span></span>

<span data-ttu-id="21265-111">Le applicazioni che **non** includono l'autenticazione possono applicare l'impalcatura per aggiungere il Identity pacchetto RCL.</span><span class="sxs-lookup"><span data-stu-id="21265-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="21265-112">È possibile scegliere di selezionare il Identity codice da generare.</span><span class="sxs-lookup"><span data-stu-id="21265-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="21265-113">Sebbene l'impalcatura generi la maggior parte del codice necessario, è necessario aggiornare il progetto per completare il processo.</span><span class="sxs-lookup"><span data-stu-id="21265-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="21265-114">Questo documento illustra i passaggi necessari per completare un Identity aggiornamento di ponteggi.</span><span class="sxs-lookup"><span data-stu-id="21265-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="21265-115">È consigliabile usare un sistema di controllo del codice sorgente che mostra le differenze dei file e consente di eseguire il backup delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="21265-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="21265-116">Controllare le modifiche dopo l'esecuzione dell' Identity impalcatura.</span><span class="sxs-lookup"><span data-stu-id="21265-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="21265-117">I servizi sono necessari quando si usa [l'autenticazione a due fattori](xref:security/authentication/identity-enable-qrcodes), la [conferma dell'account e il recupero della password](xref:security/authentication/accconfirm)e altre funzionalità di sicurezza con Identity .</span><span class="sxs-lookup"><span data-stu-id="21265-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="21265-118">I servizi o gli stub di servizio non vengono generati durante l'impalcatura Identity .</span><span class="sxs-lookup"><span data-stu-id="21265-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="21265-119">I servizi per abilitare queste funzionalità devono essere aggiunti manualmente.</span><span class="sxs-lookup"><span data-stu-id="21265-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="21265-120">Vedere ad esempio [Richiedi conferma tramite posta elettronica](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="21265-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="21265-121">Quando si esegue il ponteggi Identity con un nuovo contesto dati in un progetto con account singoli esistenti:</span><span class="sxs-lookup"><span data-stu-id="21265-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="21265-122">In `Startup.ConfigureServices` rimuovere le chiamate a:</span><span class="sxs-lookup"><span data-stu-id="21265-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="21265-123">Ad esempio, `AddDbContext` e `AddDefaultIdentity` sono impostati come commento nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="21265-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="21265-124">Il codice precedente commenta il codice duplicato in *aree/ Identity /IdentityHostingStartup.cs*</span><span class="sxs-lookup"><span data-stu-id="21265-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="21265-125">In genere, le app create con singoli account ***non*** devono creare un nuovo contesto dati.</span><span class="sxs-lookup"><span data-stu-id="21265-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="21265-126">Impalcatura Identity in un progetto vuoto</span><span class="sxs-lookup"><span data-stu-id="21265-126">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="21265-127">Aggiornare la `Startup` classe con codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="21265-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="21265-128">Impalcatura Identity in un Razor progetto senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="21265-128">Scaffold Identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="21265-129">viene configurato in *area/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="21265-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="21265-130">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="21265-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="21265-131">Migrazioni, UseAuthentication e layout</span><span class="sxs-lookup"><span data-stu-id="21265-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="21265-132">Abilita autenticazione</span><span class="sxs-lookup"><span data-stu-id="21265-132">Enable authentication</span></span>

<span data-ttu-id="21265-133">Aggiornare la `Startup` classe con codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="21265-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="21265-134">Modifiche del layout</span><span class="sxs-lookup"><span data-stu-id="21265-134">Layout changes</span></span>

<span data-ttu-id="21265-135">Facoltativo: aggiungere l'account di accesso parziale ( `_LoginPartial` ) al file di layout:</span><span class="sxs-lookup"><span data-stu-id="21265-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="21265-136">Impalcatura Identity in un Razor progetto con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="21265-136">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="21265-137">Alcune Identity opzioni sono configurate in *aree/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="21265-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="21265-138">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="21265-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="21265-139">Impalcatura Identity in un progetto MVC senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="21265-139">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="21265-140">Facoltativo: aggiungere l'account di accesso parziale ( `_LoginPartial` ) al file *Views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="21265-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="21265-141">Spostare il file *pages/Shared/_LoginPartial. cshtml* in *Views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="21265-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="21265-142">viene configurato in *area/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="21265-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="21265-143">Per ulteriori informazioni, vedere IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="21265-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="21265-144">Aggiornare la `Startup` classe con codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="21265-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="21265-145">Impalcatura Identity in un progetto MVC con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="21265-145">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-identity-into-a-blazor-server-project-without-existing-authorization"></a><span data-ttu-id="21265-146">Impalcatura Identity in un Blazor progetto server senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="21265-146">Scaffold Identity into a Blazor Server project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="21265-147">viene configurato in *area/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="21265-147"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="21265-148">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="21265-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="21265-149">Migrazioni</span><span class="sxs-lookup"><span data-stu-id="21265-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="21265-150">Passare un token XSRF all'app</span><span class="sxs-lookup"><span data-stu-id="21265-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="21265-151">I token possono essere passati ai componenti:</span><span class="sxs-lookup"><span data-stu-id="21265-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="21265-152">Quando i token di autenticazione vengono sottoposti a provisioning e salvati nel cookie di autenticazione, possono essere passati ai componenti di.</span><span class="sxs-lookup"><span data-stu-id="21265-152">When authentication tokens are provisioned and saved to the authentication cookie, they can be passed to components.</span></span>
* Razor<span data-ttu-id="21265-153">i componenti non possono usare `HttpContext` direttamente, quindi non è possibile ottenere un [token anti-request falsificazione (XSRF)](xref:security/anti-request-forgery) per pubblicare l' Identity endpoint di disconnessione in `/Identity/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="21265-153"> components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to Identity's logout endpoint at `/Identity/Account/Logout`.</span></span> <span data-ttu-id="21265-154">Un token XSRF può essere passato ai componenti.</span><span class="sxs-lookup"><span data-stu-id="21265-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="21265-155">Per altre informazioni, vedere <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="21265-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="21265-156">Nel file *pages/_Host. cshtml* , stabilire il token dopo averlo aggiunto alle `InitialApplicationState` classi e `TokenProvider` :</span><span class="sxs-lookup"><span data-stu-id="21265-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="21265-157">Aggiornare il `App` componente (*app. Razor*) per assegnare `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="21265-157">Update the `App` component (*App.razor*) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="21265-158">Il `TokenProvider` servizio illustrato nell'argomento viene usato nel `LoginDisplay` componente nella sezione [modifiche del flusso di layout e autenticazione](#layout-and-authentication-flow-changes) seguente.</span><span class="sxs-lookup"><span data-stu-id="21265-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="21265-159">Abilita autenticazione</span><span class="sxs-lookup"><span data-stu-id="21265-159">Enable authentication</span></span>

<span data-ttu-id="21265-160">Nella `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="21265-160">In the `Startup` class:</span></span>

* <span data-ttu-id="21265-161">Verificare che Razor i servizi Pages siano stati aggiunti in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="21265-161">Confirm that Razor Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="21265-162">Se si usa [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), registrare il servizio.</span><span class="sxs-lookup"><span data-stu-id="21265-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="21265-163">Chiamare il `UseDatabaseErrorPage` Generatore di applicazioni in `Startup.Configure` per l'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="21265-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="21265-164">Chiamare `UseAuthentication` e `UseAuthorization` dopo `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="21265-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="21265-165">Aggiungere un endpoint per le Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="21265-165">Add an endpoint for Razor Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="21265-166">Modifiche al layout e al flusso di autenticazione</span><span class="sxs-lookup"><span data-stu-id="21265-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="21265-167">Aggiungere un `RedirectToLogin` componente (*RedirectToLogin. Razor*) alla cartella *condivisa* dell'app nella radice del progetto:</span><span class="sxs-lookup"><span data-stu-id="21265-167">Add a `RedirectToLogin` component (*RedirectToLogin.razor*) to the app's *Shared* folder in the project root:</span></span>

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

Aggiungere un `LoginDisplay` componente (*LoginDisplay. Razor*) alla cartella *condivisa* dell'app. <span data-ttu-id="21265-169">Il [servizio TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) fornisce il token XSRF per il form HTML che invia un messaggio all' Identity endpoint di disconnessione:</span><span class="sxs-lookup"><span data-stu-id="21265-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to Identity's logout endpoint:</span></span>

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

<span data-ttu-id="21265-170">Nel `MainLayout` componente (*Shared/MainLayout. Razor*) aggiungere il `LoginDisplay` componente al contenuto dell'elemento della riga superiore `<div>` :</span><span class="sxs-lookup"><span data-stu-id="21265-170">In the `MainLayout` component (*Shared/MainLayout.razor*), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="21265-171">Endpoint di autenticazione dello stile</span><span class="sxs-lookup"><span data-stu-id="21265-171">Style authentication endpoints</span></span>

<span data-ttu-id="21265-172">Poiché Blazor il server utilizza pagine Razor Identity di pagine, lo stile dell'interfaccia utente cambia quando un visitatore si sposta tra le Identity pagine e i componenti.</span><span class="sxs-lookup"><span data-stu-id="21265-172">Because Blazor Server uses Razor Pages Identity pages, the styling of the UI changes when a visitor navigates between Identity pages and components.</span></span> <span data-ttu-id="21265-173">Sono disponibili due opzioni per risolvere gli stili incongruenti:</span><span class="sxs-lookup"><span data-stu-id="21265-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-identity-components"></a><span data-ttu-id="21265-174">Componenti di compilazione Identity</span><span class="sxs-lookup"><span data-stu-id="21265-174">Build Identity components</span></span>

<span data-ttu-id="21265-175">Un approccio all'utilizzo di componenti per Identity invece di pagine consiste nel creare Identity componenti.</span><span class="sxs-lookup"><span data-stu-id="21265-175">An approach to using components for Identity instead of pages is to build Identity components.</span></span> <span data-ttu-id="21265-176">Poiché `SignInManager` e `UserManager` non sono supportati nei Razor componenti, usare gli endpoint API nell' Blazor app Server per elaborare le azioni dell'account utente.</span><span class="sxs-lookup"><span data-stu-id="21265-176">Because `SignInManager` and `UserManager` aren't supported in Razor components, use API endpoints in the Blazor Server app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-blazor-app-styles"></a><span data-ttu-id="21265-177">Usare un layout personalizzato con Blazor stili di app</span><span class="sxs-lookup"><span data-stu-id="21265-177">Use a custom layout with Blazor app styles</span></span>

<span data-ttu-id="21265-178">Il Identity layout e gli stili delle pagine possono essere modificati per produrre pagine che usano il Blazor tema predefinito.</span><span class="sxs-lookup"><span data-stu-id="21265-178">The Identity pages layout and styles can be modified to produce pages that use the default Blazor theme.</span></span>

> [!NOTE]
> <span data-ttu-id="21265-179">L'esempio in questa sezione è semplicemente un punto di partenza per la personalizzazione.</span><span class="sxs-lookup"><span data-stu-id="21265-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="21265-180">Il lavoro aggiuntivo è probabilmente necessario per la migliore esperienza utente.</span><span class="sxs-lookup"><span data-stu-id="21265-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="21265-181">Creare un nuovo `NavMenu_IdentityLayout` componente (*Shared/NavMenu_IdentityLayout. Razor*).</span><span class="sxs-lookup"><span data-stu-id="21265-181">Create a new `NavMenu_IdentityLayout` component (*Shared/NavMenu_IdentityLayout.razor*).</span></span> <span data-ttu-id="21265-182">Per il markup e il codice del componente, usare lo stesso contenuto del componente dell'app `NavMenu` (*Shared/NavMenu. Razor*).</span><span class="sxs-lookup"><span data-stu-id="21265-182">For the markup and code of the component, use the same content of the app's `NavMenu` component (*Shared/NavMenu.razor*).</span></span> <span data-ttu-id="21265-183">Rimuovere i `NavLink` componenti che non possono essere raggiunti in modo anonimo perché i reindirizzamenti automatici nel `RedirectToLogin` componente hanno esito negativo per i componenti che richiedono l'autenticazione o l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="21265-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="21265-184">Nel file *pages/Shared/layout. cshtml* apportare le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="21265-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="21265-185">Aggiungere le Razor direttive all'inizio del file per usare gli helper tag e i componenti dell'app nella cartella *condivisa* :</span><span class="sxs-lookup"><span data-stu-id="21265-185">Add Razor directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="21265-186">Sostituire `{APPLICATION ASSEMBLY}` con il nome dell'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="21265-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="21265-187">Aggiungere un `<base>` tag e un Blazor foglio `<link>` di stile al `<head>` contenuto:</span><span class="sxs-lookup"><span data-stu-id="21265-187">Add a `<base>` tag and Blazor stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="21265-188">Modificare il contenuto del `<body>` tag come segue:</span><span class="sxs-lookup"><span data-stu-id="21265-188">Change the content of the `<body>` tag to the following:</span></span>

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

## <a name="scaffold-identity-into-a-blazor-server-project-with-authorization"></a><span data-ttu-id="21265-189">Impalcatura Identity in un Blazor progetto server con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="21265-189">Scaffold Identity into a Blazor Server project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="21265-190">Alcune Identity opzioni sono configurate in *aree/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="21265-190">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="21265-191">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="21265-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="21265-192">Crea Identity origine interfaccia utente completa</span><span class="sxs-lookup"><span data-stu-id="21265-192">Create full Identity UI source</span></span>

<span data-ttu-id="21265-193">Per mantenere il controllo completo dell' Identity interfaccia utente, eseguire l' Identity impalcatura e selezionare **Sostituisci tutti i file**.</span><span class="sxs-lookup"><span data-stu-id="21265-193">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="21265-194">Il codice evidenziato seguente mostra le modifiche per sostituire l' Identity interfaccia utente predefinita con Identity in un'app web ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="21265-194">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="21265-195">Questa operazione può essere eseguita per avere il controllo completo dell' Identity interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="21265-195">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="21265-196">Il valore predefinito Identity viene sostituito nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="21265-196">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="21265-197">Il codice seguente imposta [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="21265-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="21265-198">Registrare un' `IEmailSender` implementazione, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="21265-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-a-page"></a><span data-ttu-id="21265-199">Disabilitare una pagina</span><span class="sxs-lookup"><span data-stu-id="21265-199">Disable a page</span></span>

<span data-ttu-id="21265-200">In questa sezione viene illustrato come disabilitare la pagina Register, ma è possibile utilizzare l'approccio per disabilitare qualsiasi pagina.</span><span class="sxs-lookup"><span data-stu-id="21265-200">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="21265-201">Per disabilitare la registrazione dell'utente:</span><span class="sxs-lookup"><span data-stu-id="21265-201">To disable user registration:</span></span>

* <span data-ttu-id="21265-202">Impalcature Identity .</span><span class="sxs-lookup"><span data-stu-id="21265-202">Scaffold Identity.</span></span> <span data-ttu-id="21265-203">Includere account. Register, account. login e account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="21265-203">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="21265-204">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="21265-204">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="21265-205">Aggiornare le *aree/ Identity /pages/account/Register.cshtml.cs* in modo che gli utenti non possano effettuare la registrazione da questo endpoint:</span><span class="sxs-lookup"><span data-stu-id="21265-205">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="21265-206">Aggiornare le *aree/ Identity /pages/account/Register.cshtml* in modo che siano coerenti con le modifiche precedenti:</span><span class="sxs-lookup"><span data-stu-id="21265-206">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="21265-207">Impostare come commento o rimuovere il collegamento di registrazione da *aree/ Identity /pages/account/login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="21265-207">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="21265-208">Aggiornare la pagina *aree/ Identity /pages/account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="21265-208">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="21265-209">Rimuovere il codice e i collegamenti dal file cshtml.</span><span class="sxs-lookup"><span data-stu-id="21265-209">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="21265-210">Rimuovere il codice di conferma da `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="21265-210">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="21265-211">Usare un'altra app per aggiungere utenti</span><span class="sxs-lookup"><span data-stu-id="21265-211">Use another app to add users</span></span>

<span data-ttu-id="21265-212">Fornire un meccanismo per aggiungere utenti all'esterno dell'app Web.</span><span class="sxs-lookup"><span data-stu-id="21265-212">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="21265-213">Le opzioni per aggiungere utenti includono:</span><span class="sxs-lookup"><span data-stu-id="21265-213">Options to add users include:</span></span>

* <span data-ttu-id="21265-214">Un'app Web amministrativa dedicata.</span><span class="sxs-lookup"><span data-stu-id="21265-214">A dedicated admin web app.</span></span>
* <span data-ttu-id="21265-215">Un'app console.</span><span class="sxs-lookup"><span data-stu-id="21265-215">A console app.</span></span>

<span data-ttu-id="21265-216">Il codice seguente illustra un approccio per l'aggiunta di utenti:</span><span class="sxs-lookup"><span data-stu-id="21265-216">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="21265-217">Un elenco di utenti viene letto in memoria.</span><span class="sxs-lookup"><span data-stu-id="21265-217">A list of users is read into memory.</span></span>
* <span data-ttu-id="21265-218">Per ogni utente viene generata una password univoca complessa.</span><span class="sxs-lookup"><span data-stu-id="21265-218">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="21265-219">L'utente viene aggiunto al Identity database.</span><span class="sxs-lookup"><span data-stu-id="21265-219">The user is added to the Identity database.</span></span>
* <span data-ttu-id="21265-220">Viene inviata una notifica all'utente e viene chiesto di modificare la password.</span><span class="sxs-lookup"><span data-stu-id="21265-220">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="21265-221">Il codice seguente illustra l'aggiunta di un utente:</span><span class="sxs-lookup"><span data-stu-id="21265-221">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="21265-222">Per gli scenari di produzione è possibile seguire un approccio simile.</span><span class="sxs-lookup"><span data-stu-id="21265-222">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="21265-223">Impedisci la pubblicazione di Identity Asset statici</span><span class="sxs-lookup"><span data-stu-id="21265-223">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="21265-224">Per evitare la pubblicazione Identity di asset statici nella radice Web, vedere <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .</span><span class="sxs-lookup"><span data-stu-id="21265-224">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="21265-225">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="21265-225">Additional resources</span></span>

* [<span data-ttu-id="21265-226">Modifiche al codice di autenticazione in ASP.NET Core 2,1 e versioni successive</span><span class="sxs-lookup"><span data-stu-id="21265-226">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="21265-227">ASP.NET Core 2,1 e versioni successive [fornisce Identity ASP.NET Core](xref:security/authentication/identity) come [ Razor libreria di classi](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="21265-227">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="21265-228">Le applicazioni che includono Identity possono applicare l'impalcatura per aggiungere selettivamente il codice sorgente contenuto nella Identity Razor libreria di classi (RCL).</span><span class="sxs-lookup"><span data-stu-id="21265-228">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="21265-229">Se si vuole generare un codice sorgente, è possibile modificare il codice e modificarne il comportamento.</span><span class="sxs-lookup"><span data-stu-id="21265-229">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="21265-230">Ad esempio, è possibile indicare allo scaffolder di generare il codice usato nella registrazione.</span><span class="sxs-lookup"><span data-stu-id="21265-230">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="21265-231">Il codice generato ha la precedenza sullo stesso codice in Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="21265-231">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="21265-232">Per ottenere il controllo completo dell'interfaccia utente e non usare il RCL predefinito, vedere la sezione [creare l'origine dell'interfaccia utente Full Identity](#full).</span><span class="sxs-lookup"><span data-stu-id="21265-232">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="21265-233">Le applicazioni che **non** includono l'autenticazione possono applicare l'impalcatura per aggiungere il Identity pacchetto RCL.</span><span class="sxs-lookup"><span data-stu-id="21265-233">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="21265-234">È possibile scegliere di selezionare il Identity codice da generare.</span><span class="sxs-lookup"><span data-stu-id="21265-234">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="21265-235">Sebbene l'impalcatura generi la maggior parte del codice necessario, sarà necessario aggiornare il progetto per completare il processo.</span><span class="sxs-lookup"><span data-stu-id="21265-235">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="21265-236">Questo documento illustra i passaggi necessari per completare un Identity aggiornamento di ponteggi.</span><span class="sxs-lookup"><span data-stu-id="21265-236">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="21265-237">Quando Identity si esegue l'impalcatura, viene creato un file di *ScaffoldingReadme.txt* nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="21265-237">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="21265-238">Il file di *ScaffoldingReadme.txt* contiene istruzioni generali sugli elementi necessari per completare l' Identity aggiornamento di ponteggi.</span><span class="sxs-lookup"><span data-stu-id="21265-238">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="21265-239">Questo documento contiene istruzioni più complete rispetto al file *ScaffoldingReadme.txt* .</span><span class="sxs-lookup"><span data-stu-id="21265-239">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="21265-240">È consigliabile usare un sistema di controllo del codice sorgente che mostra le differenze dei file e consente di eseguire il backup delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="21265-240">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="21265-241">Controllare le modifiche dopo l'esecuzione dell' Identity impalcatura.</span><span class="sxs-lookup"><span data-stu-id="21265-241">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="21265-242">I servizi sono necessari quando si usa [l'autenticazione a due fattori](xref:security/authentication/identity-enable-qrcodes), la [conferma dell'account e il recupero della password](xref:security/authentication/accconfirm)e altre funzionalità di sicurezza con Identity .</span><span class="sxs-lookup"><span data-stu-id="21265-242">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="21265-243">I servizi o gli stub di servizio non vengono generati durante l'impalcatura Identity .</span><span class="sxs-lookup"><span data-stu-id="21265-243">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="21265-244">I servizi per abilitare queste funzionalità devono essere aggiunti manualmente.</span><span class="sxs-lookup"><span data-stu-id="21265-244">Services to enable these features must be added manually.</span></span> <span data-ttu-id="21265-245">Vedere ad esempio [Richiedi conferma tramite posta elettronica](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="21265-245">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="21265-246">Impalcatura Identity in un progetto vuoto</span><span class="sxs-lookup"><span data-stu-id="21265-246">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="21265-247">Aggiungere le seguenti chiamate evidenziate alla `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="21265-247">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="21265-248">Impalcatura Identity in un Razor progetto senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="21265-248">Scaffold Identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="21265-249">viene configurato in *area/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="21265-249"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="21265-250">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="21265-250">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="21265-251">Migrazioni, UseAuthentication e layout</span><span class="sxs-lookup"><span data-stu-id="21265-251">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="21265-252">Abilita autenticazione</span><span class="sxs-lookup"><span data-stu-id="21265-252">Enable authentication</span></span>

<span data-ttu-id="21265-253">Nel `Configure` metodo della `Startup` classe, chiamare [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) dopo `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="21265-253">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="21265-254">Modifiche del layout</span><span class="sxs-lookup"><span data-stu-id="21265-254">Layout changes</span></span>

<span data-ttu-id="21265-255">Facoltativo: aggiungere l'account di accesso parziale ( `_LoginPartial` ) al file di layout:</span><span class="sxs-lookup"><span data-stu-id="21265-255">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="21265-256">Impalcatura Identity in un Razor progetto con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="21265-256">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="21265-257">Alcune Identity opzioni sono configurate in *aree/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="21265-257">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="21265-258">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="21265-258">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="21265-259">Impalcatura Identity in un progetto MVC senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="21265-259">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="21265-260">Facoltativo: aggiungere l'account di accesso parziale ( `_LoginPartial` ) al file *Views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="21265-260">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="21265-261">Spostare il file *pages/Shared/_LoginPartial. cshtml* in *Views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="21265-261">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="21265-262">viene configurato in *area/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="21265-262"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="21265-263">Per ulteriori informazioni, vedere IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="21265-263">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="21265-264">Chiama [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) dopo `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="21265-264">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="21265-265">Impalcatura Identity in un progetto MVC con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="21265-265">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="21265-266">Eliminare le *pagine o* la cartella condivisa e i file in tale cartella.</span><span class="sxs-lookup"><span data-stu-id="21265-266">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="21265-267">Crea Identity origine interfaccia utente completa</span><span class="sxs-lookup"><span data-stu-id="21265-267">Create full Identity UI source</span></span>

<span data-ttu-id="21265-268">Per mantenere il controllo completo dell' Identity interfaccia utente, eseguire l' Identity impalcatura e selezionare **Sostituisci tutti i file**.</span><span class="sxs-lookup"><span data-stu-id="21265-268">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="21265-269">Il codice evidenziato seguente mostra le modifiche per sostituire l' Identity interfaccia utente predefinita con Identity in un'app web ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="21265-269">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="21265-270">Questa operazione può essere eseguita per avere il controllo completo dell' Identity interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="21265-270">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="21265-271">Il valore predefinito Identity viene sostituito nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="21265-271">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="21265-272">Il codice seguente imposta [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="21265-272">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="21265-273">Registrare un' `IEmailSender` implementazione, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="21265-273">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="21265-274">Disabilita pagina Registro</span><span class="sxs-lookup"><span data-stu-id="21265-274">Disable register page</span></span>

<span data-ttu-id="21265-275">Per disabilitare la registrazione dell'utente:</span><span class="sxs-lookup"><span data-stu-id="21265-275">To disable user registration:</span></span>

* <span data-ttu-id="21265-276">Impalcature Identity .</span><span class="sxs-lookup"><span data-stu-id="21265-276">Scaffold Identity.</span></span> <span data-ttu-id="21265-277">Includere account. Register, account. login e account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="21265-277">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="21265-278">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="21265-278">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="21265-279">Aggiornare le *aree/ Identity /pages/account/Register.cshtml.cs* in modo che gli utenti non possano effettuare la registrazione da questo endpoint:</span><span class="sxs-lookup"><span data-stu-id="21265-279">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="21265-280">Aggiornare le *aree/ Identity /pages/account/Register.cshtml* in modo che siano coerenti con le modifiche precedenti:</span><span class="sxs-lookup"><span data-stu-id="21265-280">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="21265-281">Impostare come commento o rimuovere il collegamento di registrazione da *aree/ Identity /pages/account/login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="21265-281">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="21265-282">Aggiornare la pagina *aree/ Identity /pages/account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="21265-282">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="21265-283">Rimuovere il codice e i collegamenti dal file cshtml.</span><span class="sxs-lookup"><span data-stu-id="21265-283">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="21265-284">Rimuovere il codice di conferma da `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="21265-284">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="21265-285">Usare un'altra app per aggiungere utenti</span><span class="sxs-lookup"><span data-stu-id="21265-285">Use another app to add users</span></span>

<span data-ttu-id="21265-286">Fornire un meccanismo per aggiungere utenti all'esterno dell'app Web.</span><span class="sxs-lookup"><span data-stu-id="21265-286">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="21265-287">Le opzioni per aggiungere utenti includono:</span><span class="sxs-lookup"><span data-stu-id="21265-287">Options to add users include:</span></span>

* <span data-ttu-id="21265-288">Un'app Web amministrativa dedicata.</span><span class="sxs-lookup"><span data-stu-id="21265-288">A dedicated admin web app.</span></span>
* <span data-ttu-id="21265-289">Un'app console.</span><span class="sxs-lookup"><span data-stu-id="21265-289">A console app.</span></span>

<span data-ttu-id="21265-290">Il codice seguente illustra un approccio per l'aggiunta di utenti:</span><span class="sxs-lookup"><span data-stu-id="21265-290">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="21265-291">Un elenco di utenti viene letto in memoria.</span><span class="sxs-lookup"><span data-stu-id="21265-291">A list of users is read into memory.</span></span>
* <span data-ttu-id="21265-292">Per ogni utente viene generata una password univoca complessa.</span><span class="sxs-lookup"><span data-stu-id="21265-292">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="21265-293">L'utente viene aggiunto al Identity database.</span><span class="sxs-lookup"><span data-stu-id="21265-293">The user is added to the Identity database.</span></span>
* <span data-ttu-id="21265-294">Viene inviata una notifica all'utente e viene chiesto di modificare la password.</span><span class="sxs-lookup"><span data-stu-id="21265-294">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="21265-295">Il codice seguente illustra l'aggiunta di un utente:</span><span class="sxs-lookup"><span data-stu-id="21265-295">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="21265-296">Per gli scenari di produzione è possibile seguire un approccio simile.</span><span class="sxs-lookup"><span data-stu-id="21265-296">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="21265-297">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="21265-297">Additional resources</span></span>

* [<span data-ttu-id="21265-298">Modifiche al codice di autenticazione in ASP.NET Core 2,1 e versioni successive</span><span class="sxs-lookup"><span data-stu-id="21265-298">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
