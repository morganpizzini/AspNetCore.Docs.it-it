---
title: 'Impalcatura :::no-loc(Identity)::: nei progetti ASP.NET Core'
author: rick-anderson
description: "Informazioni su come eseguire :::no-loc(Identity)::: l'impalcatura in un progetto ASP.NET Core."
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/scaffold-identity
ms.openlocfilehash: c79dfc64d4311088c3f9ea03aad7570189000e2a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053319"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a><span data-ttu-id="78e75-103">Impalcatura :::no-loc(Identity)::: nei progetti ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="78e75-103">Scaffold :::no-loc(Identity)::: in ASP.NET Core projects</span></span>

<span data-ttu-id="78e75-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="78e75-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="78e75-105">ASP.NET Core fornisce [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) una [ :::no-loc(Razor)::: libreria di classi](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="78e75-105">ASP.NET Core provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="78e75-106">Le applicazioni che includono :::no-loc(Identity)::: possono applicare l'impalcatura per aggiungere selettivamente il codice sorgente contenuto nella :::no-loc(Identity)::: :::no-loc(Razor)::: libreria di classi (RCL).</span><span class="sxs-lookup"><span data-stu-id="78e75-106">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="78e75-107">Se si vuole generare un codice sorgente, è possibile modificare il codice e modificarne il comportamento.</span><span class="sxs-lookup"><span data-stu-id="78e75-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="78e75-108">Ad esempio, è possibile indicare allo scaffolder di generare il codice usato nella registrazione.</span><span class="sxs-lookup"><span data-stu-id="78e75-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="78e75-109">Il codice generato ha la precedenza sullo stesso codice in :::no-loc(Identity)::: RCL.</span><span class="sxs-lookup"><span data-stu-id="78e75-109">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="78e75-110">Per ottenere il controllo completo dell'interfaccia utente e non usare il RCL predefinito, vedere la sezione [creare un'origine completa dell' :::no-loc(Identity)::: interfaccia utente](#full).</span><span class="sxs-lookup"><span data-stu-id="78e75-110">To gain full control of the UI and not use the default RCL, see the section [Create full :::no-loc(Identity)::: UI source](#full).</span></span>

<span data-ttu-id="78e75-111">Le applicazioni che **non** includono l'autenticazione possono applicare l'impalcatura per aggiungere il :::no-loc(Identity)::: pacchetto RCL.</span><span class="sxs-lookup"><span data-stu-id="78e75-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="78e75-112">È possibile scegliere di selezionare il :::no-loc(Identity)::: codice da generare.</span><span class="sxs-lookup"><span data-stu-id="78e75-112">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="78e75-113">Sebbene l'impalcatura generi la maggior parte del codice necessario, è necessario aggiornare il progetto per completare il processo.</span><span class="sxs-lookup"><span data-stu-id="78e75-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="78e75-114">Questo documento illustra i passaggi necessari per completare un :::no-loc(Identity)::: aggiornamento di ponteggi.</span><span class="sxs-lookup"><span data-stu-id="78e75-114">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="78e75-115">È consigliabile usare un sistema di controllo del codice sorgente che mostra le differenze dei file e consente di eseguire il backup delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="78e75-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="78e75-116">Controllare le modifiche dopo l'esecuzione dell' :::no-loc(Identity)::: impalcatura.</span><span class="sxs-lookup"><span data-stu-id="78e75-116">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

<span data-ttu-id="78e75-117">I servizi sono necessari quando si usa [l'autenticazione a due fattori](xref:security/authentication/identity-enable-qrcodes), la [conferma dell'account e il recupero della password](xref:security/authentication/accconfirm)e altre funzionalità di sicurezza con :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="78e75-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="78e75-118">I servizi o gli stub di servizio non vengono generati durante l'impalcatura :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="78e75-118">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="78e75-119">I servizi per abilitare queste funzionalità devono essere aggiunti manualmente.</span><span class="sxs-lookup"><span data-stu-id="78e75-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="78e75-120">Vedere ad esempio [Richiedi conferma tramite posta elettronica](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="78e75-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="78e75-121">Quando si esegue il ponteggi :::no-loc(Identity)::: con un nuovo contesto dati in un progetto con account singoli esistenti:</span><span class="sxs-lookup"><span data-stu-id="78e75-121">When scaffolding :::no-loc(Identity)::: with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="78e75-122">In `Startup.ConfigureServices` rimuovere le chiamate a:</span><span class="sxs-lookup"><span data-stu-id="78e75-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefault:::no-loc(Identity):::`

<span data-ttu-id="78e75-123">Ad esempio, `AddDbContext` e `AddDefault:::no-loc(Identity):::` sono impostati come commento nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="78e75-123">For example, `AddDbContext` and `AddDefault:::no-loc(Identity):::` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="78e75-124">Il codice precedente commenta il codice duplicato in *aree/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs*</span><span class="sxs-lookup"><span data-stu-id="78e75-124">The preceeding code comments out the code that is duplicated in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs*</span></span>

<span data-ttu-id="78e75-125">In genere, le app create con singoli account devono \* **non** _ creare un nuovo contesto dati.</span><span class="sxs-lookup"><span data-stu-id="78e75-125">Typically, apps that were created with individual accounts should \* **not** _ create a new data context.</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="78e75-126">Impalcatura :::no-loc(Identity)::: in un progetto vuoto</span><span class="sxs-lookup"><span data-stu-id="78e75-126">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="78e75-127">Aggiornare la `Startup` classe con codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="78e75-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="78e75-128">Impalcatura :::no-loc(Identity)::: in un :::no-loc(Razor)::: progetto senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="78e75-128">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add Create:::no-loc(Identity):::Schema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="78e75-129">:::no-loc(Identity):::è configurato in _Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs \*.</span><span class="sxs-lookup"><span data-stu-id="78e75-129">:::no-loc(Identity)::: is configured in _Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs\*.</span></span> <span data-ttu-id="78e75-130">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="78e75-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="78e75-131">Migrazioni, UseAuthentication e layout</span><span class="sxs-lookup"><span data-stu-id="78e75-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="78e75-132">Abilita autenticazione</span><span class="sxs-lookup"><span data-stu-id="78e75-132">Enable authentication</span></span>

<span data-ttu-id="78e75-133">Aggiornare la `Startup` classe con codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="78e75-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="78e75-134">Modifiche del layout</span><span class="sxs-lookup"><span data-stu-id="78e75-134">Layout changes</span></span>

<span data-ttu-id="78e75-135">Facoltativo: aggiungere l'account di accesso parziale ( `_LoginPartial` ) al file di layout:</span><span class="sxs-lookup"><span data-stu-id="78e75-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="78e75-136">Impalcatura :::no-loc(Identity)::: in un :::no-loc(Razor)::: progetto con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="78e75-136">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

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

<span data-ttu-id="78e75-137">Alcune :::no-loc(Identity)::: opzioni sono configurate in *aree/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="78e75-137">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="78e75-138">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="78e75-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="78e75-139">Impalcatura :::no-loc(Identity)::: in un progetto MVC senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="78e75-139">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="78e75-140">Facoltativo: aggiungere l'account di accesso parziale ( `_LoginPartial` ) al file *Views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="78e75-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="78e75-141">Spostare il file *pages/Shared/_LoginPartial. cshtml* in *Views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="78e75-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="78e75-142">:::no-loc(Identity):::è configurato in *areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="78e75-142">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="78e75-143">Per ulteriori informazioni, vedere IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="78e75-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="78e75-144">Aggiornare la `Startup` classe con codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="78e75-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="78e75-145">Impalcatura :::no-loc(Identity)::: in un progetto MVC con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="78e75-145">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a><span data-ttu-id="78e75-146">Impalcatura :::no-loc(Identity)::: in un :::no-loc(Blazor Server)::: progetto senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="78e75-146">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="78e75-147">:::no-loc(Identity):::è configurato in *areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="78e75-147">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="78e75-148">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="78e75-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="78e75-149">Migrazioni</span><span class="sxs-lookup"><span data-stu-id="78e75-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="78e75-150">Passare un token XSRF all'app</span><span class="sxs-lookup"><span data-stu-id="78e75-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="78e75-151">I token possono essere passati ai componenti:</span><span class="sxs-lookup"><span data-stu-id="78e75-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="78e75-152">Quando i token di autenticazione vengono sottoposti a provisioning e salvati nell'autenticazione :::no-loc(cookie)::: , possono essere passati ai componenti di.</span><span class="sxs-lookup"><span data-stu-id="78e75-152">When authentication tokens are provisioned and saved to the authentication :::no-loc(cookie):::, they can be passed to components.</span></span>
* <span data-ttu-id="78e75-153">:::no-loc(Razor)::: i componenti non possono usare `HttpContext` direttamente, quindi non è possibile ottenere un [token anti-request falsificazione (XSRF)](xref:security/anti-request-forgery) per pubblicare l' :::no-loc(Identity)::: endpoint di disconnessione in `/:::no-loc(Identity):::/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="78e75-153">:::no-loc(Razor)::: components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to :::no-loc(Identity):::'s logout endpoint at `/:::no-loc(Identity):::/Account/Logout`.</span></span> <span data-ttu-id="78e75-154">Un token XSRF può essere passato ai componenti.</span><span class="sxs-lookup"><span data-stu-id="78e75-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="78e75-155">Per altre informazioni, vedere <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="78e75-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="78e75-156">Nel file *pages/_Host. cshtml* , stabilire il token dopo averlo aggiunto alle `InitialApplicationState` classi e `TokenProvider` :</span><span class="sxs-lookup"><span data-stu-id="78e75-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="78e75-157">Aggiornare il `App` componente ( *app. Razor* ) per assegnare `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="78e75-157">Update the `App` component ( *App.razor* ) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="78e75-158">Il `TokenProvider` servizio illustrato nell'argomento viene usato nel `LoginDisplay` componente nella sezione [modifiche del flusso di layout e autenticazione](#layout-and-authentication-flow-changes) seguente.</span><span class="sxs-lookup"><span data-stu-id="78e75-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="78e75-159">Abilita autenticazione</span><span class="sxs-lookup"><span data-stu-id="78e75-159">Enable authentication</span></span>

<span data-ttu-id="78e75-160">Nella `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="78e75-160">In the `Startup` class:</span></span>

* <span data-ttu-id="78e75-161">Verificare che :::no-loc(Razor)::: i servizi Pages siano stati aggiunti in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="78e75-161">Confirm that :::no-loc(Razor)::: Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="78e75-162">Se si usa [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), registrare il servizio.</span><span class="sxs-lookup"><span data-stu-id="78e75-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="78e75-163">Chiamare il `UseDatabaseErrorPage` Generatore di applicazioni in `Startup.Configure` per l'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="78e75-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="78e75-164">Chiamare `UseAuthentication` e `UseAuthorization` dopo `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="78e75-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="78e75-165">Aggiungere un endpoint per le :::no-loc(Razor)::: pagine.</span><span class="sxs-lookup"><span data-stu-id="78e75-165">Add an endpoint for :::no-loc(Razor)::: Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/Startup:::no-loc(Blazor):::.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="78e75-166">Modifiche al layout e al flusso di autenticazione</span><span class="sxs-lookup"><span data-stu-id="78e75-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="78e75-167">Aggiungere un `RedirectToLogin` componente ( *RedirectToLogin. Razor* ) alla cartella *condivisa* dell'app nella radice del progetto:</span><span class="sxs-lookup"><span data-stu-id="78e75-167">Add a `RedirectToLogin` component ( *RedirectToLogin.razor* ) to the app's *Shared* folder in the project root:</span></span>

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(":::no-loc(Identity):::/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

Aggiungere un `LoginDisplay` componente ( *LoginDisplay. Razor* ) alla cartella *condivisa* dell'app. <span data-ttu-id="78e75-169">Il [servizio TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) fornisce il token XSRF per il form HTML che invia un messaggio all' :::no-loc(Identity)::: endpoint di disconnessione:</span><span class="sxs-lookup"><span data-stu-id="78e75-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to :::no-loc(Identity):::'s logout endpoint:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href=":::no-loc(Identity):::/Account/Manage/Index">
            Hello, @context.User.:::no-loc(Identity):::.Name!
        </a>
        <form action="/:::no-loc(Identity):::/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href=":::no-loc(Identity):::/Account/Register">Register</a>
        <a href=":::no-loc(Identity):::/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="78e75-170">Nel `MainLayout` componente ( *Shared/MainLayout. Razor* ) aggiungere il `LoginDisplay` componente al contenuto dell'elemento della riga superiore `<div>` :</span><span class="sxs-lookup"><span data-stu-id="78e75-170">In the `MainLayout` component ( *Shared/MainLayout.razor* ), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="78e75-171">Endpoint di autenticazione dello stile</span><span class="sxs-lookup"><span data-stu-id="78e75-171">Style authentication endpoints</span></span>

<span data-ttu-id="78e75-172">Poiché :::no-loc(Blazor Server)::: utilizza :::no-loc(Razor)::: pagine :::no-loc(Identity)::: di pagine, lo stile dell'interfaccia utente cambia quando un visitatore si sposta tra le :::no-loc(Identity)::: pagine e i componenti.</span><span class="sxs-lookup"><span data-stu-id="78e75-172">Because :::no-loc(Blazor Server)::: uses :::no-loc(Razor)::: Pages :::no-loc(Identity)::: pages, the styling of the UI changes when a visitor navigates between :::no-loc(Identity)::: pages and components.</span></span> <span data-ttu-id="78e75-173">Sono disponibili due opzioni per risolvere gli stili incongruenti:</span><span class="sxs-lookup"><span data-stu-id="78e75-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-no-locidentity-components"></a><span data-ttu-id="78e75-174">Componenti di compilazione :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="78e75-174">Build :::no-loc(Identity)::: components</span></span>

<span data-ttu-id="78e75-175">Un approccio all'utilizzo di componenti per :::no-loc(Identity)::: invece di pagine consiste nel creare :::no-loc(Identity)::: componenti.</span><span class="sxs-lookup"><span data-stu-id="78e75-175">An approach to using components for :::no-loc(Identity)::: instead of pages is to build :::no-loc(Identity)::: components.</span></span> <span data-ttu-id="78e75-176">Poiché `SignInManager` e `UserManager` non sono supportati nei :::no-loc(Razor)::: componenti, usare gli endpoint API nell' :::no-loc(Blazor Server)::: app per elaborare le azioni dell'account utente.</span><span class="sxs-lookup"><span data-stu-id="78e75-176">Because `SignInManager` and `UserManager` aren't supported in :::no-loc(Razor)::: components, use API endpoints in the :::no-loc(Blazor Server)::: app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a><span data-ttu-id="78e75-177">Usare un layout personalizzato con :::no-loc(Blazor)::: stili di app</span><span class="sxs-lookup"><span data-stu-id="78e75-177">Use a custom layout with :::no-loc(Blazor)::: app styles</span></span>

<span data-ttu-id="78e75-178">Il :::no-loc(Identity)::: layout e gli stili delle pagine possono essere modificati per produrre pagine che usano il :::no-loc(Blazor)::: tema predefinito.</span><span class="sxs-lookup"><span data-stu-id="78e75-178">The :::no-loc(Identity)::: pages layout and styles can be modified to produce pages that use the default :::no-loc(Blazor)::: theme.</span></span>

> [!NOTE]
> <span data-ttu-id="78e75-179">L'esempio in questa sezione è semplicemente un punto di partenza per la personalizzazione.</span><span class="sxs-lookup"><span data-stu-id="78e75-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="78e75-180">Il lavoro aggiuntivo è probabilmente necessario per la migliore esperienza utente.</span><span class="sxs-lookup"><span data-stu-id="78e75-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="78e75-181">Creare un nuovo `NavMenu_:::no-loc(Identity):::Layout` componente ( *Shared/NavMenu_ :::no-loc(Identity)::: layout. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="78e75-181">Create a new `NavMenu_:::no-loc(Identity):::Layout` component ( *Shared/NavMenu_:::no-loc(Identity):::Layout.razor* ).</span></span> <span data-ttu-id="78e75-182">Per il markup e il codice del componente, usare lo stesso contenuto del componente dell'app `NavMenu` ( *Shared/NavMenu. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="78e75-182">For the markup and code of the component, use the same content of the app's `NavMenu` component ( *Shared/NavMenu.razor* ).</span></span> <span data-ttu-id="78e75-183">Rimuovere i `NavLink` componenti che non possono essere raggiunti in modo anonimo perché i reindirizzamenti automatici nel `RedirectToLogin` componente hanno esito negativo per i componenti che richiedono l'autenticazione o l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="78e75-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="78e75-184">Nel file *pages/Shared/layout. cshtml* apportare le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="78e75-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="78e75-185">Aggiungere le :::no-loc(Razor)::: direttive all'inizio del file per usare gli helper tag e i componenti dell'app nella cartella *condivisa* :</span><span class="sxs-lookup"><span data-stu-id="78e75-185">Add :::no-loc(Razor)::: directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="78e75-186">Sostituire `{APPLICATION ASSEMBLY}` con il nome dell'assembly dell'app.</span><span class="sxs-lookup"><span data-stu-id="78e75-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="78e75-187">Aggiungere un `<base>` tag e un :::no-loc(Blazor)::: foglio `<link>` di stile al `<head>` contenuto:</span><span class="sxs-lookup"><span data-stu-id="78e75-187">Add a `<base>` tag and :::no-loc(Blazor)::: stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="78e75-188">Modificare il contenuto del `<body>` tag come segue:</span><span class="sxs-lookup"><span data-stu-id="78e75-188">Change the content of the `<body>` tag to the following:</span></span>

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_:::no-loc(Identity):::Layout)" 
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
              throw new InvalidOperationException("The default :::no-loc(Identity)::: UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/:::no-loc(Identity):::/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/:::no-loc(Identity):::/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/:::no-loc(Identity):::/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a><span data-ttu-id="78e75-189">Impalcatura :::no-loc(Identity)::: in un :::no-loc(Blazor Server)::: progetto con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="78e75-189">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="78e75-190">Alcune :::no-loc(Identity)::: opzioni sono configurate in *aree/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="78e75-190">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="78e75-191">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="78e75-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="78e75-192">Crea :::no-loc(Identity)::: origine interfaccia utente completa</span><span class="sxs-lookup"><span data-stu-id="78e75-192">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="78e75-193">Per mantenere il controllo completo dell' :::no-loc(Identity)::: interfaccia utente, eseguire l' :::no-loc(Identity)::: impalcatura e selezionare **Sostituisci tutti i file** .</span><span class="sxs-lookup"><span data-stu-id="78e75-193">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="78e75-194">Il codice evidenziato seguente mostra le modifiche per sostituire l' :::no-loc(Identity)::: interfaccia utente predefinita con :::no-loc(Identity)::: in un'app web ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="78e75-194">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="78e75-195">Questa operazione può essere eseguita per avere il controllo completo dell' :::no-loc(Identity)::: interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="78e75-195">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="78e75-196">Il valore predefinito :::no-loc(Identity)::: viene sostituito nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="78e75-196">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="78e75-197">Il codice seguente imposta [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="78e75-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="78e75-198">Registrare un' `IEmailSender` implementazione, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="78e75-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="78e75-199">Configurazione password</span><span class="sxs-lookup"><span data-stu-id="78e75-199">Password configuration</span></span>

<span data-ttu-id="78e75-200">Se <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> sono configurati in `Startup.ConfigureServices` , la configurazione degli [ `[StringLength]` attributi](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) potrebbe essere necessaria per la `Password` proprietà nelle pagine con impalcature :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="78e75-200">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="78e75-201">`InputModel``Password`le proprietà si trovano nei file seguenti:</span><span class="sxs-lookup"><span data-stu-id="78e75-201">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="78e75-202">Disabilitare una pagina</span><span class="sxs-lookup"><span data-stu-id="78e75-202">Disable a page</span></span>

<span data-ttu-id="78e75-203">In questa sezione viene illustrato come disabilitare la pagina Register, ma è possibile utilizzare l'approccio per disabilitare qualsiasi pagina.</span><span class="sxs-lookup"><span data-stu-id="78e75-203">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="78e75-204">Per disabilitare la registrazione dell'utente:</span><span class="sxs-lookup"><span data-stu-id="78e75-204">To disable user registration:</span></span>

* <span data-ttu-id="78e75-205">Impalcature :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="78e75-205">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="78e75-206">Includere account. Register, account. login e account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="78e75-206">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="78e75-207">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="78e75-207">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="78e75-208">Aggiornare le *aree/ :::no-loc(Identity)::: /pages/account/Register.cshtml.cs* in modo che gli utenti non possano effettuare la registrazione da questo endpoint:</span><span class="sxs-lookup"><span data-stu-id="78e75-208">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="78e75-209">Aggiornare le *aree/ :::no-loc(Identity)::: /pages/account/Register.cshtml* in modo che siano coerenti con le modifiche precedenti:</span><span class="sxs-lookup"><span data-stu-id="78e75-209">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="78e75-210">Impostare come commento o rimuovere il collegamento di registrazione da *aree/ :::no-loc(Identity)::: /pages/account/login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="78e75-210">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="78e75-211">Aggiornare la pagina *aree/ :::no-loc(Identity)::: /pages/account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="78e75-211">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="78e75-212">Rimuovere il codice e i collegamenti dal file cshtml.</span><span class="sxs-lookup"><span data-stu-id="78e75-212">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="78e75-213">Rimuovere il codice di conferma da `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="78e75-213">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="78e75-214">Usare un'altra app per aggiungere utenti</span><span class="sxs-lookup"><span data-stu-id="78e75-214">Use another app to add users</span></span>

<span data-ttu-id="78e75-215">Fornire un meccanismo per aggiungere utenti all'esterno dell'app Web.</span><span class="sxs-lookup"><span data-stu-id="78e75-215">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="78e75-216">Le opzioni per aggiungere utenti includono:</span><span class="sxs-lookup"><span data-stu-id="78e75-216">Options to add users include:</span></span>

* <span data-ttu-id="78e75-217">Un'app Web amministrativa dedicata.</span><span class="sxs-lookup"><span data-stu-id="78e75-217">A dedicated admin web app.</span></span>
* <span data-ttu-id="78e75-218">Un'app console.</span><span class="sxs-lookup"><span data-stu-id="78e75-218">A console app.</span></span>

<span data-ttu-id="78e75-219">Il codice seguente illustra un approccio per l'aggiunta di utenti:</span><span class="sxs-lookup"><span data-stu-id="78e75-219">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="78e75-220">Un elenco di utenti viene letto in memoria.</span><span class="sxs-lookup"><span data-stu-id="78e75-220">A list of users is read into memory.</span></span>
* <span data-ttu-id="78e75-221">Per ogni utente viene generata una password univoca complessa.</span><span class="sxs-lookup"><span data-stu-id="78e75-221">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="78e75-222">L'utente viene aggiunto al :::no-loc(Identity)::: database.</span><span class="sxs-lookup"><span data-stu-id="78e75-222">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="78e75-223">Viene inviata una notifica all'utente e viene chiesto di modificare la password.</span><span class="sxs-lookup"><span data-stu-id="78e75-223">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="78e75-224">Il codice seguente illustra l'aggiunta di un utente:</span><span class="sxs-lookup"><span data-stu-id="78e75-224">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="78e75-225">Per gli scenari di produzione è possibile seguire un approccio simile.</span><span class="sxs-lookup"><span data-stu-id="78e75-225">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="78e75-226">Impedisci la pubblicazione di :::no-loc(Identity)::: Asset statici</span><span class="sxs-lookup"><span data-stu-id="78e75-226">Prevent publish of static :::no-loc(Identity)::: assets</span></span>

<span data-ttu-id="78e75-227">Per evitare la pubblicazione :::no-loc(Identity)::: di asset statici nella radice Web, vedere <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .</span><span class="sxs-lookup"><span data-stu-id="78e75-227">To prevent publishing static :::no-loc(Identity)::: assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78e75-228">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="78e75-228">Additional resources</span></span>

* [<span data-ttu-id="78e75-229">Modifiche al codice di autenticazione in ASP.NET Core 2,1 e versioni successive</span><span class="sxs-lookup"><span data-stu-id="78e75-229">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="78e75-230">ASP.NET Core 2,1 e versioni successive fornisce [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) una [ :::no-loc(Razor)::: libreria di classi](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="78e75-230">ASP.NET Core 2.1 and later provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="78e75-231">Le applicazioni che includono :::no-loc(Identity)::: possono applicare l'impalcatura per aggiungere selettivamente il codice sorgente contenuto nella :::no-loc(Identity)::: :::no-loc(Razor)::: libreria di classi (RCL).</span><span class="sxs-lookup"><span data-stu-id="78e75-231">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="78e75-232">Se si vuole generare un codice sorgente, è possibile modificare il codice e modificarne il comportamento.</span><span class="sxs-lookup"><span data-stu-id="78e75-232">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="78e75-233">Ad esempio, è possibile indicare allo scaffolder di generare il codice usato nella registrazione.</span><span class="sxs-lookup"><span data-stu-id="78e75-233">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="78e75-234">Il codice generato ha la precedenza sullo stesso codice in :::no-loc(Identity)::: RCL.</span><span class="sxs-lookup"><span data-stu-id="78e75-234">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="78e75-235">Per ottenere il controllo completo dell'interfaccia utente e non usare il RCL predefinito, vedere la sezione [creare l'origine dell'interfaccia utente Full Identity](#full).</span><span class="sxs-lookup"><span data-stu-id="78e75-235">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="78e75-236">Le applicazioni che **non** includono l'autenticazione possono applicare l'impalcatura per aggiungere il :::no-loc(Identity)::: pacchetto RCL.</span><span class="sxs-lookup"><span data-stu-id="78e75-236">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="78e75-237">È possibile scegliere di selezionare il :::no-loc(Identity)::: codice da generare.</span><span class="sxs-lookup"><span data-stu-id="78e75-237">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="78e75-238">Sebbene l'impalcatura generi la maggior parte del codice necessario, sarà necessario aggiornare il progetto per completare il processo.</span><span class="sxs-lookup"><span data-stu-id="78e75-238">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="78e75-239">Questo documento illustra i passaggi necessari per completare un :::no-loc(Identity)::: aggiornamento di ponteggi.</span><span class="sxs-lookup"><span data-stu-id="78e75-239">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="78e75-240">Quando :::no-loc(Identity)::: si esegue l'impalcatura, viene creato un file di *ScaffoldingReadme.txt* nella directory del progetto.</span><span class="sxs-lookup"><span data-stu-id="78e75-240">When the :::no-loc(Identity)::: scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="78e75-241">Il file di *ScaffoldingReadme.txt* contiene istruzioni generali sugli elementi necessari per completare l' :::no-loc(Identity)::: aggiornamento di ponteggi.</span><span class="sxs-lookup"><span data-stu-id="78e75-241">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the :::no-loc(Identity)::: scaffolding update.</span></span> <span data-ttu-id="78e75-242">Questo documento contiene istruzioni più complete rispetto al file *ScaffoldingReadme.txt* .</span><span class="sxs-lookup"><span data-stu-id="78e75-242">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="78e75-243">È consigliabile usare un sistema di controllo del codice sorgente che mostra le differenze dei file e consente di eseguire il backup delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="78e75-243">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="78e75-244">Controllare le modifiche dopo l'esecuzione dell' :::no-loc(Identity)::: impalcatura.</span><span class="sxs-lookup"><span data-stu-id="78e75-244">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="78e75-245">I servizi sono necessari quando si usa [l'autenticazione a due fattori](xref:security/authentication/identity-enable-qrcodes), la [conferma dell'account e il recupero della password](xref:security/authentication/accconfirm)e altre funzionalità di sicurezza con :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="78e75-245">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="78e75-246">I servizi o gli stub di servizio non vengono generati durante l'impalcatura :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="78e75-246">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="78e75-247">I servizi per abilitare queste funzionalità devono essere aggiunti manualmente.</span><span class="sxs-lookup"><span data-stu-id="78e75-247">Services to enable these features must be added manually.</span></span> <span data-ttu-id="78e75-248">Vedere ad esempio [Richiedi conferma tramite posta elettronica](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="78e75-248">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="78e75-249">Impalcatura :::no-loc(Identity)::: in un progetto vuoto</span><span class="sxs-lookup"><span data-stu-id="78e75-249">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="78e75-250">Aggiungere le seguenti chiamate evidenziate alla `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="78e75-250">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="78e75-251">Impalcatura :::no-loc(Identity)::: in un :::no-loc(Razor)::: progetto senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="78e75-251">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="78e75-252">:::no-loc(Identity):::è configurato in *areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="78e75-252">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="78e75-253">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="78e75-253">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="78e75-254">Migrazioni, UseAuthentication e layout</span><span class="sxs-lookup"><span data-stu-id="78e75-254">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="78e75-255">Abilita autenticazione</span><span class="sxs-lookup"><span data-stu-id="78e75-255">Enable authentication</span></span>

<span data-ttu-id="78e75-256">Nel `Configure` metodo della `Startup` classe, chiamare [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) dopo `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="78e75-256">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="78e75-257">Modifiche del layout</span><span class="sxs-lookup"><span data-stu-id="78e75-257">Layout changes</span></span>

<span data-ttu-id="78e75-258">Facoltativo: aggiungere l'account di accesso parziale ( `_LoginPartial` ) al file di layout:</span><span class="sxs-lookup"><span data-stu-id="78e75-258">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="78e75-259">Impalcatura :::no-loc(Identity)::: in un :::no-loc(Razor)::: progetto con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="78e75-259">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

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

<span data-ttu-id="78e75-260">Alcune :::no-loc(Identity)::: opzioni sono configurate in *aree/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="78e75-260">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="78e75-261">Per ulteriori informazioni, vedere [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="78e75-261">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="78e75-262">Impalcatura :::no-loc(Identity)::: in un progetto MVC senza autorizzazione esistente</span><span class="sxs-lookup"><span data-stu-id="78e75-262">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="78e75-263">Facoltativo: aggiungere l'account di accesso parziale ( `_LoginPartial` ) al file *Views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="78e75-263">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="78e75-264">Spostare il file *pages/Shared/_LoginPartial. cshtml* in *Views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="78e75-264">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="78e75-265">:::no-loc(Identity):::è configurato in *areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="78e75-265">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="78e75-266">Per ulteriori informazioni, vedere IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="78e75-266">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="78e75-267">Chiama [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) dopo `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="78e75-267">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="78e75-268">Impalcatura :::no-loc(Identity)::: in un progetto MVC con autorizzazione</span><span class="sxs-lookup"><span data-stu-id="78e75-268">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="78e75-269">Eliminare le *pagine o* la cartella condivisa e i file in tale cartella.</span><span class="sxs-lookup"><span data-stu-id="78e75-269">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="78e75-270">Crea :::no-loc(Identity)::: origine interfaccia utente completa</span><span class="sxs-lookup"><span data-stu-id="78e75-270">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="78e75-271">Per mantenere il controllo completo dell' :::no-loc(Identity)::: interfaccia utente, eseguire l' :::no-loc(Identity)::: impalcatura e selezionare **Sostituisci tutti i file** .</span><span class="sxs-lookup"><span data-stu-id="78e75-271">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="78e75-272">Il codice evidenziato seguente mostra le modifiche per sostituire l' :::no-loc(Identity)::: interfaccia utente predefinita con :::no-loc(Identity)::: in un'app web ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="78e75-272">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="78e75-273">Questa operazione può essere eseguita per avere il controllo completo dell' :::no-loc(Identity)::: interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="78e75-273">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="78e75-274">Il valore predefinito :::no-loc(Identity)::: viene sostituito nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="78e75-274">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="78e75-275">Il codice seguente imposta [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="78e75-275">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="78e75-276">Registrare un' `IEmailSender` implementazione, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="78e75-276">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="78e75-277">Configurazione password</span><span class="sxs-lookup"><span data-stu-id="78e75-277">Password configuration</span></span>

<span data-ttu-id="78e75-278">Se <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> sono configurati in `Startup.ConfigureServices` , la configurazione degli [ `[StringLength]` attributi](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) potrebbe essere necessaria per la `Password` proprietà nelle pagine con impalcature :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="78e75-278">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="78e75-279">`InputModel``Password`le proprietà si trovano nei file seguenti:</span><span class="sxs-lookup"><span data-stu-id="78e75-279">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="78e75-280">Disabilita pagina Registro</span><span class="sxs-lookup"><span data-stu-id="78e75-280">Disable register page</span></span>

<span data-ttu-id="78e75-281">Per disabilitare la registrazione dell'utente:</span><span class="sxs-lookup"><span data-stu-id="78e75-281">To disable user registration:</span></span>

* <span data-ttu-id="78e75-282">Impalcature :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="78e75-282">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="78e75-283">Includere account. Register, account. login e account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="78e75-283">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="78e75-284">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="78e75-284">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="78e75-285">Aggiornare le *aree/ :::no-loc(Identity)::: /pages/account/Register.cshtml.cs* in modo che gli utenti non possano effettuare la registrazione da questo endpoint:</span><span class="sxs-lookup"><span data-stu-id="78e75-285">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="78e75-286">Aggiornare le *aree/ :::no-loc(Identity)::: /pages/account/Register.cshtml* in modo che siano coerenti con le modifiche precedenti:</span><span class="sxs-lookup"><span data-stu-id="78e75-286">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="78e75-287">Impostare come commento o rimuovere il collegamento di registrazione da *aree/ :::no-loc(Identity)::: /pages/account/login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="78e75-287">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="78e75-288">Aggiornare la pagina *aree/ :::no-loc(Identity)::: /pages/account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="78e75-288">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="78e75-289">Rimuovere il codice e i collegamenti dal file cshtml.</span><span class="sxs-lookup"><span data-stu-id="78e75-289">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="78e75-290">Rimuovere il codice di conferma da `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="78e75-290">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="78e75-291">Usare un'altra app per aggiungere utenti</span><span class="sxs-lookup"><span data-stu-id="78e75-291">Use another app to add users</span></span>

<span data-ttu-id="78e75-292">Fornire un meccanismo per aggiungere utenti all'esterno dell'app Web.</span><span class="sxs-lookup"><span data-stu-id="78e75-292">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="78e75-293">Le opzioni per aggiungere utenti includono:</span><span class="sxs-lookup"><span data-stu-id="78e75-293">Options to add users include:</span></span>

* <span data-ttu-id="78e75-294">Un'app Web amministrativa dedicata.</span><span class="sxs-lookup"><span data-stu-id="78e75-294">A dedicated admin web app.</span></span>
* <span data-ttu-id="78e75-295">Un'app console.</span><span class="sxs-lookup"><span data-stu-id="78e75-295">A console app.</span></span>

<span data-ttu-id="78e75-296">Il codice seguente illustra un approccio per l'aggiunta di utenti:</span><span class="sxs-lookup"><span data-stu-id="78e75-296">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="78e75-297">Un elenco di utenti viene letto in memoria.</span><span class="sxs-lookup"><span data-stu-id="78e75-297">A list of users is read into memory.</span></span>
* <span data-ttu-id="78e75-298">Per ogni utente viene generata una password univoca complessa.</span><span class="sxs-lookup"><span data-stu-id="78e75-298">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="78e75-299">L'utente viene aggiunto al :::no-loc(Identity)::: database.</span><span class="sxs-lookup"><span data-stu-id="78e75-299">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="78e75-300">Viene inviata una notifica all'utente e viene chiesto di modificare la password.</span><span class="sxs-lookup"><span data-stu-id="78e75-300">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="78e75-301">Il codice seguente illustra l'aggiunta di un utente:</span><span class="sxs-lookup"><span data-stu-id="78e75-301">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="78e75-302">Per gli scenari di produzione è possibile seguire un approccio simile.</span><span class="sxs-lookup"><span data-stu-id="78e75-302">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78e75-303">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="78e75-303">Additional resources</span></span>

* [<span data-ttu-id="78e75-304">Modifiche al codice di autenticazione in ASP.NET Core 2,1 e versioni successive</span><span class="sxs-lookup"><span data-stu-id="78e75-304">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
