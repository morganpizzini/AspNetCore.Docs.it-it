---
title: 'Impalcatura Identity nei progetti ASP.NET Core'
author: rick-anderson
description: "Informazioni su come eseguire Identity l'impalcatura in un progetto ASP.NET Core."
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 813dd7837c265c78c584d66dd51bc23399d12fbe
ms.sourcegitcommit: 5156eab2118584405eb663e1fcd82f8bd7764504
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2020
ms.locfileid: "93141495"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a><span data-ttu-id="fc141-103">Impalcatura Identity nei progetti ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc141-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="fc141-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fc141-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fc141-105">ASP.NET Core fornisce [ASP.NET Core Identity](xref:security/authentication/identity) una [ Razor libreria di classi](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="fc141-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="fc141-106">Le applicazioni che includono Identity possono applicare l'impalcatura per aggiungere selettivamente il codice sorgente contenuto nella Identity Razor libreria di classi (RCL).</span><span class="sxs-lookup"><span data-stu-id="fc141-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="fc141-107">Se si vuole generare un codice sorgente, è possibile modificare il codice e modificarne il comportamento.</span><span class="sxs-lookup"><span data-stu-id="fc141-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="fc141-108">Ad esempio, è possibile indicare allo scaffolder di generare il codice usato nella registrazione.</span><span class="sxs-lookup"><span data-stu-id="fc141-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="fc141-109">Il codice generato ha la precedenza sullo stesso codice in Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="fc141-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="fc141-110">Per ottenere il controllo completo dell'interfaccia utente e non usare il RCL predefinito, vedere la sezione [creare un'origine completa dell' Identity interfaccia utente](#full).</span><span class="sxs-lookup"><span data-stu-id="fc141-110">To gain full control of the UI and not use the default RCL, see the section [Create full Identity UI source](#full).</span></span>

<span data-ttu-id="fc141-111">Le applicazioni che **non** includono l'autenticazione possono applicare l'impalcatura per aggiungere il Identity pacchetto RCL.</span><span class="sxs-lookup"><span data-stu-id="fc141-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="fc141-112">È possibile scegliere di selezionare il Identity codice da generare.</span><span class="sxs-lookup"><span data-stu-id="fc141-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="fc141-113">Sebbene l'impalcatura generi la maggior parte del codice necessario, è necessario aggiornare il progetto per completare il processo.</span><span class="sxs-lookup"><span data-stu-id="fc141-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="fc141-114">Questo documento illustra i passaggi necessari per completare un Identity aggiornamento di ponteggi.</span><span class="sxs-lookup"><span data-stu-id="fc141-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="fc141-115">È consigliabile usare un sistema di controllo del codice sorgente che mostra le differenze dei file e consente di eseguire il backup delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="fc141-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="fc141-116">Controllare le modifiche dopo l'esecuzione dell' Identity impalcatura.</span><span class="sxs-lookup"><span data-stu-id="fc141-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="fc141-117">I servizi sono necessari quando si usa [l'autenticazione a due fattori](xref:security/authentication/identity-enable-qrcodes), la [conferma dell'account e il recupero della password](xref:security/authentication/accconfirm)e altre funzionalità di sicurezza con Identity .</span><span class="sxs-lookup"><span data-stu-id="fc141-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="fc141-118">I servizi o gli stub di servizio non vengono generati durante l'impalcatura Identity .</span><span class="sxs-lookup"><span data-stu-id="fc141-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="fc141-119">I servizi per abilitare queste funzionalità devono essere aggiunti manualmente.</span><span class="sxs-lookup"><span data-stu-id="fc141-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="fc141-120">Vedere ad esempio [Richiedi conferma tramite posta elettronica](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="fc141-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="fc141-121">Quando si esegue il ponteggi Identity con un nuovo contesto dati in un progetto con account singoli esistenti:</span><span class="sxs-lookup"><span data-stu-id="fc141-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="fc141-122">In `Startup.ConfigureServices` rimuovere le chiamate a:</span><span class="sxs-lookup"><span data-stu-id="fc141-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="fc141-123">Ad esempio, `AddDbContext` e `AddDefaultIdentity` sono impostati come commento nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="fc141-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="fc141-124">Il codice precedente commenta il codice duplicato in *aree/ Identity / Identity HostingStartup.cs*</span><span class="sxs-lookup"><span data-stu-id="fc141-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="fc141-125">In genere, le app create con singoli account devono \* **non** _ creare un nuovo contesto dati.</span><span class="sxs-lookup"><span data-stu-id="fc141-125">Typically, apps that were created with individual accounts should \* **not** _ create a new data context.</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="fc141-126">Impalcatura Identity in un progetto vuoto</span><span class="sxs-lookup"><span data-stu-id="fc141-126">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="fc141-127">Aggiornare la `Startup` classe con codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="fc141-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="fc141-128">Impalcatura Identity in un Razor progetto senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="fc141-128">Scaffold Identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="fc141-129">Identityè configurato in _Areas/ Identity / Identity HostingStartup.cs \*.</span><span class="sxs-lookup"><span data-stu-id="fc141-129">Identity is configured in _Areas/Identity/IdentityHostingStartup.cs\*.</span></span> <span data-ttu-id="fc141-130">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="fc141-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="fc141-131">Migrazioni, UseAuthentication e layout</span><span class="sxs-lookup"><span data-stu-id="fc141-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="fc141-132">Abilita autenticazione</span><span class="sxs-lookup"><span data-stu-id="fc141-132">Enable authentication</span></span>

<span data-ttu-id="fc141-133">Aggiornare la `Startup` classe con codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="fc141-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="fc141-134">Modifiche del layout</span><span class="sxs-lookup"><span data-stu-id="fc141-134">Layout changes</span></span>

<span data-ttu-id="fc141-135">Facoltativo: aggiungere l'account di accesso parziale ( `_LoginPartial` ) al file di layout:</span><span class="sxs-lookup"><span data-stu-id="fc141-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="fc141-136">Impalcatura Identity in un Razor progetto con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="fc141-136">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="fc141-137">Alcune Identity opzioni sono configurate in *aree/ Identity / Identity HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="fc141-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="fc141-138">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="fc141-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="fc141-139">Impalcatura Identity in un progetto MVC senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="fc141-139">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="fc141-140">Facoltativo: aggiungere l'account di accesso parziale ( `_LoginPartial` ) al file *Views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="fc141-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="fc141-141">Spostare il file *pages/Shared/_LoginPartial. cshtml* in *Views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="fc141-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="fc141-142">Identityè configurato in *areas/ Identity / Identity HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="fc141-142">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="fc141-143">Per ulteriori informazioni, vedere IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="fc141-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="fc141-144">Aggiornare la `Startup` classe con codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="fc141-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="fc141-145">Impalcatura Identity in un progetto MVC con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="fc141-145">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a><span data-ttu-id="fc141-146">Impalcatura Identity in un Blazor Server progetto senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="fc141-146">Scaffold Identity into a Blazor Server project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="fc141-147">Identityè configurato in *areas/ Identity / Identity HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="fc141-147">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="fc141-148">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="fc141-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="fc141-149">Migrazioni</span><span class="sxs-lookup"><span data-stu-id="fc141-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="fc141-150">Passare un token XSRF all'app</span><span class="sxs-lookup"><span data-stu-id="fc141-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="fc141-151">I token possono essere passati ai componenti:</span><span class="sxs-lookup"><span data-stu-id="fc141-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="fc141-152">Quando i token di autenticazione vengono sottoposti a provisioning e salvati nell'autenticazione cookie , possono essere passati ai componenti di.</span><span class="sxs-lookup"><span data-stu-id="fc141-152">When authentication tokens are provisioned and saved to the authentication cookie, they can be passed to components.</span></span>
* <span data-ttu-id="fc141-153">Razor i componenti non possono usare `HttpContext` direttamente, quindi non è possibile ottenere un [token anti-request falsificazione (XSRF)](xref:security/anti-request-forgery) per pubblicare l' Identity endpoint di disconnessione in `/Identity/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="fc141-153">Razor components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to Identity's logout endpoint at `/Identity/Account/Logout`.</span></span> <span data-ttu-id="fc141-154">Un token XSRF può essere passato ai componenti.</span><span class="sxs-lookup"><span data-stu-id="fc141-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="fc141-155">Per altre informazioni, vedere <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="fc141-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="fc141-156">Nel file *pages/_Host. cshtml* , stabilire il token dopo averlo aggiunto alle `InitialApplicationState` classi e `TokenProvider` :</span><span class="sxs-lookup"><span data-stu-id="fc141-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="fc141-157">Aggiornare il `App` componente ( *app. Razor* ) per assegnare `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="fc141-157">Update the `App` component ( *App.razor* ) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="fc141-158">Il `TokenProvider` servizio illustrato nell'argomento viene usato nel `LoginDisplay` componente nella sezione [modifiche del flusso di layout e autenticazione](#layout-and-authentication-flow-changes) seguente.</span><span class="sxs-lookup"><span data-stu-id="fc141-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="fc141-159">Abilita autenticazione</span><span class="sxs-lookup"><span data-stu-id="fc141-159">Enable authentication</span></span>

<span data-ttu-id="fc141-160">Nella `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="fc141-160">In the `Startup` class:</span></span>

* <span data-ttu-id="fc141-161">Verificare che Razor i servizi Pages siano stati aggiunti in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="fc141-161">Confirm that Razor Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="fc141-162">Se si usa [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), registrare il servizio.</span><span class="sxs-lookup"><span data-stu-id="fc141-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="fc141-163">Chiamare il `UseDatabaseErrorPage` Generatore di applicazioni in `Startup.Configure` per l'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="fc141-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="fc141-164">Chiamare `UseAuthentication` e `UseAuthorization` dopo `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="fc141-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="fc141-165">Aggiungere un endpoint per le Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="fc141-165">Add an endpoint for Razor Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="fc141-166">Modifiche al layout e al flusso di autenticazione</span><span class="sxs-lookup"><span data-stu-id="fc141-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="fc141-167">Aggiungere un `RedirectToLogin` componente ( *RedirectToLogin. Razor* ) alla cartella *condivisa* dell'app nella radice del progetto:</span><span class="sxs-lookup"><span data-stu-id="fc141-167">Add a `RedirectToLogin` component ( *RedirectToLogin.razor* ) to the app's *Shared* folder in the project root:</span></span>

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

Aggiungere un `LoginDisplay` componente ( *LoginDisplay. Razor* ) alla cartella *condivisa* dell'app. <span data-ttu-id="fc141-169">Il [servizio TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) fornisce il token XSRF per il form HTML che invia un messaggio all' Identity endpoint di disconnessione:</span><span class="sxs-lookup"><span data-stu-id="fc141-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to Identity's logout endpoint:</span></span>

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

<span data-ttu-id="fc141-170">Nel `MainLayout` componente ( *Shared/MainLayout. Razor* ) aggiungere il `LoginDisplay` componente al contenuto dell'elemento della riga superiore `<div>` :</span><span class="sxs-lookup"><span data-stu-id="fc141-170">In the `MainLayout` component ( *Shared/MainLayout.razor* ), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="fc141-171">Endpoint di autenticazione dello stile</span><span class="sxs-lookup"><span data-stu-id="fc141-171">Style authentication endpoints</span></span>

<span data-ttu-id="fc141-172">Poiché Blazor Server utilizza Razor pagine Identity di pagine, lo stile dell'interfaccia utente cambia quando un visitatore si sposta tra le Identity pagine e i componenti.</span><span class="sxs-lookup"><span data-stu-id="fc141-172">Because Blazor Server uses Razor Pages Identity pages, the styling of the UI changes when a visitor navigates between Identity pages and components.</span></span> <span data-ttu-id="fc141-173">Sono disponibili due opzioni per risolvere gli stili incongruenti:</span><span class="sxs-lookup"><span data-stu-id="fc141-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-no-locidentity-components"></a><span data-ttu-id="fc141-174">Componenti di compilazione Identity</span><span class="sxs-lookup"><span data-stu-id="fc141-174">Build Identity components</span></span>

<span data-ttu-id="fc141-175">Un approccio all'utilizzo di componenti per Identity invece di pagine consiste nel creare Identity componenti.</span><span class="sxs-lookup"><span data-stu-id="fc141-175">An approach to using components for Identity instead of pages is to build Identity components.</span></span> <span data-ttu-id="fc141-176">Poiché `SignInManager` e `UserManager` non sono supportati nei Razor componenti, usare gli endpoint API nell' Blazor Server app per elaborare le azioni dell'account utente.</span><span class="sxs-lookup"><span data-stu-id="fc141-176">Because `SignInManager` and `UserManager` aren't supported in Razor components, use API endpoints in the Blazor Server app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a><span data-ttu-id="fc141-177">Usare un layout personalizzato con Blazor stili di app</span><span class="sxs-lookup"><span data-stu-id="fc141-177">Use a custom layout with Blazor app styles</span></span>

<span data-ttu-id="fc141-178">Il Identity layout e gli stili delle pagine possono essere modificati per produrre pagine che usano il Blazor tema predefinito.</span><span class="sxs-lookup"><span data-stu-id="fc141-178">The Identity pages layout and styles can be modified to produce pages that use the default Blazor theme.</span></span>

> [!NOTE]
> <span data-ttu-id="fc141-179">L'esempio in questa sezione è semplicemente un punto di partenza per la personalizzazione.</span><span class="sxs-lookup"><span data-stu-id="fc141-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="fc141-180">Il lavoro aggiuntivo è probabilmente necessario per la migliore esperienza utente.</span><span class="sxs-lookup"><span data-stu-id="fc141-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="fc141-181">Creare un nuovo `NavMenu_IdentityLayout` componente ( *Shared/NavMenu_ Identity layout. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="fc141-181">Create a new `NavMenu_IdentityLayout` component ( *Shared/NavMenu_IdentityLayout.razor* ).</span></span> <span data-ttu-id="fc141-182">Per il markup e il codice del componente, usare lo stesso contenuto del componente dell'app `NavMenu` ( *Shared/NavMenu. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="fc141-182">For the markup and code of the component, use the same content of the app's `NavMenu` component ( *Shared/NavMenu.razor* ).</span></span> <span data-ttu-id="fc141-183">Rimuovere i `NavLink` componenti che non possono essere raggiunti in modo anonimo perché i reindirizzamenti automatici nel `RedirectToLogin` componente hanno esito negativo per i componenti che richiedono l'autenticazione o l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="fc141-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="fc141-184">Nel file *pages/Shared/layout. cshtml* apportare le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="fc141-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="fc141-185">Aggiungere le Razor direttive all'inizio del file per usare gli helper tag e i componenti dell'app nella cartella *condivisa* :</span><span class="sxs-lookup"><span data-stu-id="fc141-185">Add Razor directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="fc141-186">Sostituire `{APPLICATION ASSEMBLY}` con il nome dell'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="fc141-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="fc141-187">Aggiungere un `<base>` tag e un Blazor foglio `<link>` di stile al `<head>` contenuto:</span><span class="sxs-lookup"><span data-stu-id="fc141-187">Add a `<base>` tag and Blazor stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="fc141-188">Modificare il contenuto del `<body>` tag come segue:</span><span class="sxs-lookup"><span data-stu-id="fc141-188">Change the content of the `<body>` tag to the following:</span></span>

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

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a><span data-ttu-id="fc141-189">Impalcatura Identity in un Blazor Server progetto con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="fc141-189">Scaffold Identity into a Blazor Server project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="fc141-190">Alcune Identity opzioni sono configurate in *aree/ Identity / Identity HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="fc141-190">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="fc141-191">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="fc141-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="standalone-or-hosted-no-locblazor-webassembly-apps"></a><span data-ttu-id="fc141-192">App autonome o ospitate Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="fc141-192">Standalone or hosted Blazor WebAssembly apps</span></span>

<span data-ttu-id="fc141-193">Le app sul lato client Blazor WebAssembly usano i propri Identity approcci dell'interfaccia utente e non possono usare l' ASP.NET Core Identity impalcatura.</span><span class="sxs-lookup"><span data-stu-id="fc141-193">Client-side Blazor WebAssembly apps use their own Identity UI approaches and can't use ASP.NET Core Identity scaffolding.</span></span> <span data-ttu-id="fc141-194">Le app ASP.NET Core lato server delle soluzioni ospitate Blazor possono seguire le Razor indicazioni relative a pagine/MVC in questo articolo e sono configurate esattamente come qualsiasi altro tipo di app ASP.NET Core supportata da Identity .</span><span class="sxs-lookup"><span data-stu-id="fc141-194">Server-side ASP.NET Core apps of hosted Blazor solutions can follow the Razor Pages/MVC guidance in this article and are configured just like any other type of ASP.NET Core app that supports Identity.</span></span>

<span data-ttu-id="fc141-195">Il Blazor Framework non include le Razor versioni dei componenti delle Identity pagine dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="fc141-195">The Blazor framework doesn't include Razor component versions of Identity UI pages.</span></span> <span data-ttu-id="fc141-196">Identity I componenti dell'interfaccia utente Razor possono essere personalizzati o ottenuti da origini di terze parti non supportate.</span><span class="sxs-lookup"><span data-stu-id="fc141-196">Identity UI Razor components can be custom built or obtained from unsupported third-party sources.</span></span>

<span data-ttu-id="fc141-197">Per ulteriori informazioni, vedere la pagina relativa alla [ Blazor sicurezza e Identity agli articoli](xref:blazor/security/index).</span><span class="sxs-lookup"><span data-stu-id="fc141-197">For more information, see the [Blazor Security and Identity articles](xref:blazor/security/index).</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="fc141-198">Crea Identity origine interfaccia utente completa</span><span class="sxs-lookup"><span data-stu-id="fc141-198">Create full Identity UI source</span></span>

<span data-ttu-id="fc141-199">Per mantenere il controllo completo dell' Identity interfaccia utente, eseguire l' Identity impalcatura e selezionare **Sostituisci tutti i file** .</span><span class="sxs-lookup"><span data-stu-id="fc141-199">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="fc141-200">Il codice evidenziato seguente mostra le modifiche per sostituire l' Identity interfaccia utente predefinita con Identity in un'app web ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="fc141-200">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="fc141-201">Questa operazione può essere eseguita per avere il controllo completo dell' Identity interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="fc141-201">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="fc141-202">Il valore predefinito Identity viene sostituito nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="fc141-202">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="fc141-203">Il codice seguente imposta [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="fc141-203">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="fc141-204">Registrare un' `IEmailSender` implementazione, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="fc141-204">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="fc141-205">Configurazione password</span><span class="sxs-lookup"><span data-stu-id="fc141-205">Password configuration</span></span>

<span data-ttu-id="fc141-206">Se <xref:Microsoft.AspNetCore.Identity.PasswordOptions> sono configurati in `Startup.ConfigureServices` , la configurazione degli [ `[StringLength]` attributi](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) potrebbe essere necessaria per la `Password` proprietà nelle pagine con impalcature Identity .</span><span class="sxs-lookup"><span data-stu-id="fc141-206">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="fc141-207">`InputModel``Password`le proprietà si trovano nei file seguenti:</span><span class="sxs-lookup"><span data-stu-id="fc141-207">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="fc141-208">Disabilitare una pagina</span><span class="sxs-lookup"><span data-stu-id="fc141-208">Disable a page</span></span>

<span data-ttu-id="fc141-209">In questa sezione viene illustrato come disabilitare la pagina Register, ma è possibile utilizzare l'approccio per disabilitare qualsiasi pagina.</span><span class="sxs-lookup"><span data-stu-id="fc141-209">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="fc141-210">Per disabilitare la registrazione dell'utente:</span><span class="sxs-lookup"><span data-stu-id="fc141-210">To disable user registration:</span></span>

* <span data-ttu-id="fc141-211">Impalcature Identity .</span><span class="sxs-lookup"><span data-stu-id="fc141-211">Scaffold Identity.</span></span> <span data-ttu-id="fc141-212">Includere account. Register, account. login e account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="fc141-212">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="fc141-213">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="fc141-213">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="fc141-214">Aggiornare le *aree/ Identity /pages/account/Register.cshtml.cs* in modo che gli utenti non possano effettuare la registrazione da questo endpoint:</span><span class="sxs-lookup"><span data-stu-id="fc141-214">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="fc141-215">Aggiornare le *aree/ Identity /pages/account/Register.cshtml* in modo che siano coerenti con le modifiche precedenti:</span><span class="sxs-lookup"><span data-stu-id="fc141-215">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="fc141-216">Impostare come commento o rimuovere il collegamento di registrazione da *aree/ Identity /pages/account/login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="fc141-216">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="fc141-217">Aggiornare la pagina *aree/ Identity /pages/account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="fc141-217">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="fc141-218">Rimuovere il codice e i collegamenti dal file cshtml.</span><span class="sxs-lookup"><span data-stu-id="fc141-218">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="fc141-219">Rimuovere il codice di conferma da `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="fc141-219">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="fc141-220">Usare un'altra app per aggiungere utenti</span><span class="sxs-lookup"><span data-stu-id="fc141-220">Use another app to add users</span></span>

<span data-ttu-id="fc141-221">Fornire un meccanismo per aggiungere utenti all'esterno dell'app Web.</span><span class="sxs-lookup"><span data-stu-id="fc141-221">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="fc141-222">Le opzioni per aggiungere utenti includono:</span><span class="sxs-lookup"><span data-stu-id="fc141-222">Options to add users include:</span></span>

* <span data-ttu-id="fc141-223">Un'app Web amministrativa dedicata.</span><span class="sxs-lookup"><span data-stu-id="fc141-223">A dedicated admin web app.</span></span>
* <span data-ttu-id="fc141-224">Un'app console.</span><span class="sxs-lookup"><span data-stu-id="fc141-224">A console app.</span></span>

<span data-ttu-id="fc141-225">Il codice seguente illustra un approccio per l'aggiunta di utenti:</span><span class="sxs-lookup"><span data-stu-id="fc141-225">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="fc141-226">Un elenco di utenti viene letto in memoria.</span><span class="sxs-lookup"><span data-stu-id="fc141-226">A list of users is read into memory.</span></span>
* <span data-ttu-id="fc141-227">Per ogni utente viene generata una password univoca complessa.</span><span class="sxs-lookup"><span data-stu-id="fc141-227">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="fc141-228">L'utente viene aggiunto al Identity database.</span><span class="sxs-lookup"><span data-stu-id="fc141-228">The user is added to the Identity database.</span></span>
* <span data-ttu-id="fc141-229">Viene inviata una notifica all'utente e viene chiesto di modificare la password.</span><span class="sxs-lookup"><span data-stu-id="fc141-229">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="fc141-230">Il codice seguente illustra l'aggiunta di un utente:</span><span class="sxs-lookup"><span data-stu-id="fc141-230">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="fc141-231">Per gli scenari di produzione è possibile seguire un approccio simile.</span><span class="sxs-lookup"><span data-stu-id="fc141-231">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="fc141-232">Impedisci la pubblicazione di Identity Asset statici</span><span class="sxs-lookup"><span data-stu-id="fc141-232">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="fc141-233">Per evitare la pubblicazione Identity di asset statici nella radice Web, vedere <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .</span><span class="sxs-lookup"><span data-stu-id="fc141-233">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fc141-234">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="fc141-234">Additional resources</span></span>

* [<span data-ttu-id="fc141-235">Modifiche al codice di autenticazione in ASP.NET Core 2,1 e versioni successive</span><span class="sxs-lookup"><span data-stu-id="fc141-235">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fc141-236">ASP.NET Core 2,1 e versioni successive fornisce [ASP.NET Core Identity](xref:security/authentication/identity) una [ Razor libreria di classi](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="fc141-236">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="fc141-237">Le applicazioni che includono Identity possono applicare l'impalcatura per aggiungere selettivamente il codice sorgente contenuto nella Identity Razor libreria di classi (RCL).</span><span class="sxs-lookup"><span data-stu-id="fc141-237">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="fc141-238">Se si vuole generare un codice sorgente, è possibile modificare il codice e modificarne il comportamento.</span><span class="sxs-lookup"><span data-stu-id="fc141-238">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="fc141-239">Ad esempio, è possibile indicare allo scaffolder di generare il codice usato nella registrazione.</span><span class="sxs-lookup"><span data-stu-id="fc141-239">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="fc141-240">Il codice generato ha la precedenza sullo stesso codice in Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="fc141-240">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="fc141-241">Per ottenere il controllo completo dell'interfaccia utente e non usare il RCL predefinito, vedere la sezione [creare l'origine dell'interfaccia utente Full Identity](#full).</span><span class="sxs-lookup"><span data-stu-id="fc141-241">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="fc141-242">Le applicazioni che **non** includono l'autenticazione possono applicare l'impalcatura per aggiungere il Identity pacchetto RCL.</span><span class="sxs-lookup"><span data-stu-id="fc141-242">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="fc141-243">È possibile scegliere di selezionare il Identity codice da generare.</span><span class="sxs-lookup"><span data-stu-id="fc141-243">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="fc141-244">Sebbene l'impalcatura generi la maggior parte del codice necessario, sarà necessario aggiornare il progetto per completare il processo.</span><span class="sxs-lookup"><span data-stu-id="fc141-244">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="fc141-245">Questo documento illustra i passaggi necessari per completare un Identity aggiornamento di ponteggi.</span><span class="sxs-lookup"><span data-stu-id="fc141-245">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="fc141-246">Quando Identity si esegue l'impalcatura, viene creato un file di *ScaffoldingReadme.txt* nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="fc141-246">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="fc141-247">Il file di *ScaffoldingReadme.txt* contiene istruzioni generali sugli elementi necessari per completare l' Identity aggiornamento di ponteggi.</span><span class="sxs-lookup"><span data-stu-id="fc141-247">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="fc141-248">Questo documento contiene istruzioni più complete rispetto al file *ScaffoldingReadme.txt* .</span><span class="sxs-lookup"><span data-stu-id="fc141-248">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="fc141-249">È consigliabile usare un sistema di controllo del codice sorgente che mostra le differenze dei file e consente di eseguire il backup delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="fc141-249">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="fc141-250">Controllare le modifiche dopo l'esecuzione dell' Identity impalcatura.</span><span class="sxs-lookup"><span data-stu-id="fc141-250">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="fc141-251">I servizi sono necessari quando si usa [l'autenticazione a due fattori](xref:security/authentication/identity-enable-qrcodes), la [conferma dell'account e il recupero della password](xref:security/authentication/accconfirm)e altre funzionalità di sicurezza con Identity .</span><span class="sxs-lookup"><span data-stu-id="fc141-251">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="fc141-252">I servizi o gli stub di servizio non vengono generati durante l'impalcatura Identity .</span><span class="sxs-lookup"><span data-stu-id="fc141-252">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="fc141-253">I servizi per abilitare queste funzionalità devono essere aggiunti manualmente.</span><span class="sxs-lookup"><span data-stu-id="fc141-253">Services to enable these features must be added manually.</span></span> <span data-ttu-id="fc141-254">Vedere ad esempio [Richiedi conferma tramite posta elettronica](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="fc141-254">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="fc141-255">Impalcatura Identity in un progetto vuoto</span><span class="sxs-lookup"><span data-stu-id="fc141-255">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="fc141-256">Aggiungere le seguenti chiamate evidenziate alla `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="fc141-256">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="fc141-257">Impalcatura Identity in un Razor progetto senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="fc141-257">Scaffold Identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="fc141-258">Identityè configurato in *areas/ Identity / Identity HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="fc141-258">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="fc141-259">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="fc141-259">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="fc141-260">Migrazioni, UseAuthentication e layout</span><span class="sxs-lookup"><span data-stu-id="fc141-260">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="fc141-261">Abilita autenticazione</span><span class="sxs-lookup"><span data-stu-id="fc141-261">Enable authentication</span></span>

<span data-ttu-id="fc141-262">Nel `Configure` metodo della `Startup` classe, chiamare <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> dopo `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="fc141-262">In the `Configure` method of the `Startup` class, call <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="fc141-263">Modifiche del layout</span><span class="sxs-lookup"><span data-stu-id="fc141-263">Layout changes</span></span>

<span data-ttu-id="fc141-264">Facoltativo: aggiungere l'account di accesso parziale ( `_LoginPartial` ) al file di layout:</span><span class="sxs-lookup"><span data-stu-id="fc141-264">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="fc141-265">Impalcatura Identity in un Razor progetto con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="fc141-265">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="fc141-266">Alcune Identity opzioni sono configurate in *aree/ Identity / Identity HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="fc141-266">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="fc141-267">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="fc141-267">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="fc141-268">Impalcatura Identity in un progetto MVC senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="fc141-268">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="fc141-269">Facoltativo: aggiungere l'account di accesso parziale ( `_LoginPartial` ) al file *Views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="fc141-269">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="fc141-270">Spostare il file *pages/Shared/_LoginPartial. cshtml* in *Views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="fc141-270">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="fc141-271">Identityè configurato in *areas/ Identity / Identity HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="fc141-271">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="fc141-272">Per ulteriori informazioni, vedere IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="fc141-272">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="fc141-273">Chiama <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> dopo `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="fc141-273">Call <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="fc141-274">Impalcatura Identity in un progetto MVC con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="fc141-274">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="fc141-275">Eliminare le *pagine o* la cartella condivisa e i file in tale cartella.</span><span class="sxs-lookup"><span data-stu-id="fc141-275">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="fc141-276">Crea Identity origine interfaccia utente completa</span><span class="sxs-lookup"><span data-stu-id="fc141-276">Create full Identity UI source</span></span>

<span data-ttu-id="fc141-277">Per mantenere il controllo completo dell' Identity interfaccia utente, eseguire l' Identity impalcatura e selezionare **Sostituisci tutti i file** .</span><span class="sxs-lookup"><span data-stu-id="fc141-277">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="fc141-278">Il codice evidenziato seguente mostra le modifiche per sostituire l' Identity interfaccia utente predefinita con Identity in un'app web ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="fc141-278">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="fc141-279">Questa operazione può essere eseguita per avere il controllo completo dell' Identity interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="fc141-279">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="fc141-280">Il valore predefinito Identity viene sostituito nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="fc141-280">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="fc141-281">Il codice seguente imposta [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="fc141-281">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="fc141-282">Registrare un' `IEmailSender` implementazione, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="fc141-282">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="fc141-283">Configurazione password</span><span class="sxs-lookup"><span data-stu-id="fc141-283">Password configuration</span></span>

<span data-ttu-id="fc141-284">Se <xref:Microsoft.AspNetCore.Identity.PasswordOptions> sono configurati in `Startup.ConfigureServices` , la configurazione degli [ `[StringLength]` attributi](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) potrebbe essere necessaria per la `Password` proprietà nelle pagine con impalcature Identity .</span><span class="sxs-lookup"><span data-stu-id="fc141-284">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="fc141-285">`InputModel``Password`le proprietà si trovano nei file seguenti:</span><span class="sxs-lookup"><span data-stu-id="fc141-285">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="fc141-286">Disabilita pagina Registro</span><span class="sxs-lookup"><span data-stu-id="fc141-286">Disable register page</span></span>

<span data-ttu-id="fc141-287">Per disabilitare la registrazione dell'utente:</span><span class="sxs-lookup"><span data-stu-id="fc141-287">To disable user registration:</span></span>

* <span data-ttu-id="fc141-288">Impalcature Identity .</span><span class="sxs-lookup"><span data-stu-id="fc141-288">Scaffold Identity.</span></span> <span data-ttu-id="fc141-289">Includere account. Register, account. login e account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="fc141-289">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="fc141-290">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="fc141-290">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="fc141-291">Aggiornare le *aree/ Identity /pages/account/Register.cshtml.cs* in modo che gli utenti non possano effettuare la registrazione da questo endpoint:</span><span class="sxs-lookup"><span data-stu-id="fc141-291">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="fc141-292">Aggiornare le *aree/ Identity /pages/account/Register.cshtml* in modo che siano coerenti con le modifiche precedenti:</span><span class="sxs-lookup"><span data-stu-id="fc141-292">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="fc141-293">Impostare come commento o rimuovere il collegamento di registrazione da *aree/ Identity /pages/account/login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="fc141-293">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="fc141-294">Aggiornare la pagina *aree/ Identity /pages/account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="fc141-294">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="fc141-295">Rimuovere il codice e i collegamenti dal file cshtml.</span><span class="sxs-lookup"><span data-stu-id="fc141-295">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="fc141-296">Rimuovere il codice di conferma da `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="fc141-296">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="fc141-297">Usare un'altra app per aggiungere utenti</span><span class="sxs-lookup"><span data-stu-id="fc141-297">Use another app to add users</span></span>

<span data-ttu-id="fc141-298">Fornire un meccanismo per aggiungere utenti all'esterno dell'app Web.</span><span class="sxs-lookup"><span data-stu-id="fc141-298">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="fc141-299">Le opzioni per aggiungere utenti includono:</span><span class="sxs-lookup"><span data-stu-id="fc141-299">Options to add users include:</span></span>

* <span data-ttu-id="fc141-300">Un'app Web amministrativa dedicata.</span><span class="sxs-lookup"><span data-stu-id="fc141-300">A dedicated admin web app.</span></span>
* <span data-ttu-id="fc141-301">Un'app console.</span><span class="sxs-lookup"><span data-stu-id="fc141-301">A console app.</span></span>

<span data-ttu-id="fc141-302">Il codice seguente illustra un approccio per l'aggiunta di utenti:</span><span class="sxs-lookup"><span data-stu-id="fc141-302">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="fc141-303">Un elenco di utenti viene letto in memoria.</span><span class="sxs-lookup"><span data-stu-id="fc141-303">A list of users is read into memory.</span></span>
* <span data-ttu-id="fc141-304">Per ogni utente viene generata una password univoca complessa.</span><span class="sxs-lookup"><span data-stu-id="fc141-304">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="fc141-305">L'utente viene aggiunto al Identity database.</span><span class="sxs-lookup"><span data-stu-id="fc141-305">The user is added to the Identity database.</span></span>
* <span data-ttu-id="fc141-306">Viene inviata una notifica all'utente e viene chiesto di modificare la password.</span><span class="sxs-lookup"><span data-stu-id="fc141-306">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="fc141-307">Il codice seguente illustra l'aggiunta di un utente:</span><span class="sxs-lookup"><span data-stu-id="fc141-307">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="fc141-308">Per gli scenari di produzione è possibile seguire un approccio simile.</span><span class="sxs-lookup"><span data-stu-id="fc141-308">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fc141-309">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="fc141-309">Additional resources</span></span>

* [<span data-ttu-id="fc141-310">Modifiche al codice di autenticazione in ASP.NET Core 2,1 e versioni successive</span><span class="sxs-lookup"><span data-stu-id="fc141-310">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
