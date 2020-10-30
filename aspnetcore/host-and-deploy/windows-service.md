---
title: Ospitare ASP.NET Core in un servizio Windows
author: rick-anderson
description: Informazioni su come ospitare un'app ASP.NET Core in un servizio Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/windows-service
ms.openlocfilehash: 31a738e7aa8779171dfa09a5678d7240b8f62343
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057232"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="1359c-103">Ospitare ASP.NET Core in un servizio Windows</span><span class="sxs-lookup"><span data-stu-id="1359c-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1359c-104">È possibile ospitare un'app ASP.NET Core in Windows come [servizio Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) senza usare IIS.</span><span class="sxs-lookup"><span data-stu-id="1359c-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="1359c-105">Quando è ospitata come servizio di Windows, l'app viene avviata automaticamente dopo il riavvio del server.</span><span class="sxs-lookup"><span data-stu-id="1359c-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="1359c-106">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1359c-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1359c-107">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="1359c-107">Prerequisites</span></span>

* [<span data-ttu-id="1359c-108">ASP.NET Core SDK 2.1 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="1359c-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="1359c-109">PowerShell 6,2 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="1359c-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="1359c-110">Modello di servizio di ruolo di lavoro</span><span class="sxs-lookup"><span data-stu-id="1359c-110">Worker Service template</span></span>

<span data-ttu-id="1359c-111">Il modello di servizio di ruolo di lavoro di ASP.NET Core rappresenta un punto di partenza per la scrittura di app di servizi a esecuzione prolungata.</span><span class="sxs-lookup"><span data-stu-id="1359c-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="1359c-112">Per usare il modello come base per un'app di servizio Windows:</span><span class="sxs-lookup"><span data-stu-id="1359c-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="1359c-113">Creare un'app di servizio di ruolo di lavoro dal modello .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1359c-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="1359c-114">Seguire le indicazioni nella sezione [Configurazione dell'app](#app-configuration) per aggiornare l'app di servizio di ruolo di lavoro affinché venga eseguita come servizio Windows.</span><span class="sxs-lookup"><span data-stu-id="1359c-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="1359c-115">Configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="1359c-115">App configuration</span></span>

<span data-ttu-id="1359c-116">L'app richiede un riferimento al pacchetto per [Microsoft. Extensions. Hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="1359c-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="1359c-117">`IHostBuilder.UseWindowsService` viene chiamato durante la compilazione dell'host.</span><span class="sxs-lookup"><span data-stu-id="1359c-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="1359c-118">Se l'app è in esecuzione come servizio di Windows, il metodo:</span><span class="sxs-lookup"><span data-stu-id="1359c-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="1359c-119">Imposta la durata dell'host su `WindowsServiceLifetime`.</span><span class="sxs-lookup"><span data-stu-id="1359c-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="1359c-120">Imposta la [radice del contenuto](xref:fundamentals/index#content-root) su [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="1359c-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="1359c-121">Per altre informazioni, vedere la sezione [Directory corrente e radice del contenuto](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="1359c-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="1359c-122">Abilita la registrazione nel registro eventi:</span><span class="sxs-lookup"><span data-stu-id="1359c-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="1359c-123">Il nome dell'applicazione viene usato come nome di origine predefinito.</span><span class="sxs-lookup"><span data-stu-id="1359c-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="1359c-124">Il livello di registrazione predefinito è *avviso* o superiore per un'app basata su un modello di ASP.NET Core che chiama `CreateDefaultBuilder` per compilare l'host.</span><span class="sxs-lookup"><span data-stu-id="1359c-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="1359c-125">Eseguire l'override del livello di registrazione predefinito con la `Logging:EventLog:LogLevel:Default` chiave in *:::no-loc(appsettings.json):::* / *appSettings. { Environment}. JSON* o un altro provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="1359c-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *:::no-loc(appsettings.json):::*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="1359c-126">Solo gli amministratori possono creare nuove origini eventi.</span><span class="sxs-lookup"><span data-stu-id="1359c-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="1359c-127">Quando non è possibile creare un'origine evento usando il nome dell'applicazione, viene registrato un avviso nell'origine *Applicazione* e i log eventi vengono disabilitati.</span><span class="sxs-lookup"><span data-stu-id="1359c-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="1359c-128">In `CreateHostBuilder` di *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="1359c-128">In `CreateHostBuilder` of *Program.cs* :</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="1359c-129">Questo argomento è accompagnato dalle app di esempio seguenti:</span><span class="sxs-lookup"><span data-stu-id="1359c-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="1359c-130">Esempio di servizio di lavoro in background: un esempio di app non Web basato sul [modello di servizio](#worker-service-template) del ruolo di lavoro che usa i [servizi ospitati](xref:fundamentals/host/hosted-services) per le attività in background.</span><span class="sxs-lookup"><span data-stu-id="1359c-130">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="1359c-131">Esempio di servizio app Web: un :::no-loc(Razor)::: esempio di app Web di pagine che viene eseguito come servizio Windows con [servizi ospitati](xref:fundamentals/host/hosted-services) per le attività in background.</span><span class="sxs-lookup"><span data-stu-id="1359c-131">Web App Service Sample: A :::no-loc(Razor)::: Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="1359c-132">Per informazioni aggiuntive su MVC, vedere gli articoli in <xref:mvc/overview> e <xref:migration/22-to-30> .</span><span class="sxs-lookup"><span data-stu-id="1359c-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="1359c-133">Tipo di distribuzione</span><span class="sxs-lookup"><span data-stu-id="1359c-133">Deployment type</span></span>

<span data-ttu-id="1359c-134">Per informazioni e consigli sugli scenari di distribuzione, vedere [Distribuzione di applicazioni .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="1359c-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="1359c-135">SDK</span><span class="sxs-lookup"><span data-stu-id="1359c-135">SDK</span></span>

<span data-ttu-id="1359c-136">Per un servizio basato su app Web che usa le :::no-loc(Razor)::: pagine o i framework MVC, specificare Web SDK nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="1359c-136">For a web app-based service that uses the :::no-loc(Razor)::: Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="1359c-137">Se il servizio esegue solo attività in background, ad esempio [servizi ospitati](xref:fundamentals/host/hosted-services), specificare l'SDK del ruolo di lavoro nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="1359c-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="1359c-138">Distribuzione dipendente dal framework</span><span class="sxs-lookup"><span data-stu-id="1359c-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="1359c-139">La distribuzione dipendente dal framework si basa sulla presenza di una versione condivisa a livello di sistema di .NET Core nel sistema di destinazione.</span><span class="sxs-lookup"><span data-stu-id="1359c-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="1359c-140">Quando lo scenario di distribuzione dipendente dal framework viene implementato in base alle indicazioni di questo articolo, l'SDK genera un file eseguibile (con estensione *exe* ) detto *eseguibile dipendente dal framework* .</span><span class="sxs-lookup"><span data-stu-id="1359c-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable ( *.exe* ), called a *framework-dependent executable* .</span></span>

<span data-ttu-id="1359c-141">Se si usa l' [SDK Web](#sdk), un file di *web.config* , che in genere viene prodotto durante la pubblicazione di un'app ASP.NET Core, non è necessario per un'app dei servizi Windows.</span><span class="sxs-lookup"><span data-stu-id="1359c-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="1359c-142">Per disabilitare la creazione del file *web.config* , aggiungere la proprietà `<IsTransformWebConfigDisabled>` impostata su `true`.</span><span class="sxs-lookup"><span data-stu-id="1359c-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="1359c-143">Distribuzione autonoma</span><span class="sxs-lookup"><span data-stu-id="1359c-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="1359c-144">Una distribuzione autonoma non si basa sulla presenza di un framework condiviso nel sistema host.</span><span class="sxs-lookup"><span data-stu-id="1359c-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="1359c-145">Il runtime e le dipendenze dell'app vengono distribuiti con l'app.</span><span class="sxs-lookup"><span data-stu-id="1359c-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="1359c-146">Un [identificatore di runtime (RID)](/dotnet/core/rid-catalog) di Windows viene incluso nel `<PropertyGroup>` che contiene il framework di destinazione:</span><span class="sxs-lookup"><span data-stu-id="1359c-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="1359c-147">Per eseguire la pubblicazione per più identificatori di runtime:</span><span class="sxs-lookup"><span data-stu-id="1359c-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="1359c-148">Specificare gli identificatori di runtime in un elenco delimitato da punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="1359c-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="1359c-149">Usare il nome della proprietà [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plurale).</span><span class="sxs-lookup"><span data-stu-id="1359c-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="1359c-150">Per altre informazioni, vedere il [Catalogo RID di .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="1359c-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="1359c-151">Account utente del servizio</span><span class="sxs-lookup"><span data-stu-id="1359c-151">Service user account</span></span>

<span data-ttu-id="1359c-152">Per creare un account utente per un servizio, usare il cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) da una shell dei comandi di PowerShell 6 amministrativa.</span><span class="sxs-lookup"><span data-stu-id="1359c-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="1359c-153">In Aggiornamento di Windows 10 (ottobre 2018) (versione 1809/build 10.0.17763) o versioni successive:</span><span class="sxs-lookup"><span data-stu-id="1359c-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="1359c-154">In un sistema operativo Windows precedente ad Aggiornamento di Windows 10 (ottobre 2018) (versione 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="1359c-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="1359c-155">Fornire una [password complessa](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando richiesto.</span><span class="sxs-lookup"><span data-stu-id="1359c-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="1359c-156">Se non viene specificato il parametro `-AccountExpires` per il cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) con un valore <xref:System.DateTime> di scadenza, l'account non scade.</span><span class="sxs-lookup"><span data-stu-id="1359c-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="1359c-157">Per altre informazioni, vedere [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Service User Accounts](/windows/desktop/services/service-user-accounts) (Account utente di servizio).</span><span class="sxs-lookup"><span data-stu-id="1359c-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="1359c-158">Un approccio alternativo per la gestione degli utenti quando si usa Active Directory consiste nell'usare account del servizio gestito.</span><span class="sxs-lookup"><span data-stu-id="1359c-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="1359c-159">Per altre informazioni, vedere [Panoramica degli account del servizio gestito del gruppo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="1359c-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="1359c-160">Diritti Accesso come servizio</span><span class="sxs-lookup"><span data-stu-id="1359c-160">Log on as a service rights</span></span>

<span data-ttu-id="1359c-161">Per stabilire i diritti *Accesso come servizio* per un account utente di servizio:</span><span class="sxs-lookup"><span data-stu-id="1359c-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="1359c-162">Aprire l'editor Criteri di sicurezza locali eseguendo *secpol.msc* .</span><span class="sxs-lookup"><span data-stu-id="1359c-162">Open the Local Security Policy editor by running *secpol.msc* .</span></span>
1. <span data-ttu-id="1359c-163">Espandere il nodo **Criteri locali** e selezionare **Assegnazione diritti utente** .</span><span class="sxs-lookup"><span data-stu-id="1359c-163">Expand the **Local Policies** node and select **User Rights Assignment** .</span></span>
1. <span data-ttu-id="1359c-164">Aprire il criterio **Accesso come servizio** .</span><span class="sxs-lookup"><span data-stu-id="1359c-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="1359c-165">Selezionare **Aggiungi utente o gruppo** .</span><span class="sxs-lookup"><span data-stu-id="1359c-165">Select **Add User or Group** .</span></span>
1. <span data-ttu-id="1359c-166">Specificare il nome oggetto (account utente) in uno dei modi seguenti:</span><span class="sxs-lookup"><span data-stu-id="1359c-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="1359c-167">Digitare l'account utente (`{DOMAIN OR COMPUTER NAME\USER}`) nel campo del nome oggetto e scegliere **OK** per aggiungere l'utente al criterio.</span><span class="sxs-lookup"><span data-stu-id="1359c-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="1359c-168">Fare clic su **Advanced** (Avanzate).</span><span class="sxs-lookup"><span data-stu-id="1359c-168">Select **Advanced** .</span></span> <span data-ttu-id="1359c-169">Selezionare **Trova** .</span><span class="sxs-lookup"><span data-stu-id="1359c-169">Select **Find Now** .</span></span> <span data-ttu-id="1359c-170">Selezionare l'account utente dall'elenco.</span><span class="sxs-lookup"><span data-stu-id="1359c-170">Select the user account from the list.</span></span> <span data-ttu-id="1359c-171">Selezionare **OK** .</span><span class="sxs-lookup"><span data-stu-id="1359c-171">Select **OK** .</span></span> <span data-ttu-id="1359c-172">Scegliere di nuovo **OK** per aggiungere l'utente al criterio.</span><span class="sxs-lookup"><span data-stu-id="1359c-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="1359c-173">Scegliere **OK** o **Applica** per accettare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="1359c-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="1359c-174">Creare e gestire il servizio di Windows</span><span class="sxs-lookup"><span data-stu-id="1359c-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="1359c-175">Creare un servizio</span><span class="sxs-lookup"><span data-stu-id="1359c-175">Create a service</span></span>

<span data-ttu-id="1359c-176">Usare i comandi di PowerShell per registrare un servizio.</span><span class="sxs-lookup"><span data-stu-id="1359c-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="1359c-177">Da una shell dei comandi di PowerShell 6 amministrativa eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="1359c-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = "{DOMAIN OR COMPUTER NAME\USER}", "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName "{EXE FILE PATH}" -Credential "{DOMAIN OR COMPUTER NAME\USER}" -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="1359c-178">`{EXE PATH}`: Percorso della cartella dell'app nell'host (ad esempio, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="1359c-178">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="1359c-179">Non includere l'eseguibile dell'app nel percorso.</span><span class="sxs-lookup"><span data-stu-id="1359c-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="1359c-180">Non è necessario aggiungere una barra finale.</span><span class="sxs-lookup"><span data-stu-id="1359c-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="1359c-181">`{DOMAIN OR COMPUTER NAME\USER}`: Account utente del servizio (ad esempio, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="1359c-181">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="1359c-182">`{SERVICE NAME}`: Nome del servizio (ad esempio, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="1359c-182">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="1359c-183">`{EXE FILE PATH}`: Percorso eseguibile dell'app (ad esempio, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="1359c-183">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="1359c-184">Includere il nome del file eseguibile con l'estensione.</span><span class="sxs-lookup"><span data-stu-id="1359c-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="1359c-185">`{DESCRIPTION}`: Descrizione del servizio (ad esempio, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="1359c-185">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="1359c-186">`{DISPLAY NAME}`: Nome visualizzato del servizio (ad esempio, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="1359c-186">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="1359c-187">Avviare un servizio</span><span class="sxs-lookup"><span data-stu-id="1359c-187">Start a service</span></span>

<span data-ttu-id="1359c-188">Per avviare un servizio, usare il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1359c-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="1359c-189">L'avvio del servizio richiede alcuni secondi.</span><span class="sxs-lookup"><span data-stu-id="1359c-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="1359c-190">Determinare lo stato di un servizio</span><span class="sxs-lookup"><span data-stu-id="1359c-190">Determine a service's status</span></span>

<span data-ttu-id="1359c-191">Per verificare lo stato di un servizio, usare il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1359c-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="1359c-192">Lo stato viene indicato con uno dei valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="1359c-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="1359c-193">Arrestare un servizio</span><span class="sxs-lookup"><span data-stu-id="1359c-193">Stop a service</span></span>

<span data-ttu-id="1359c-194">Per arrestare un servizio, usare il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1359c-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="1359c-195">Rimuovere un servizio</span><span class="sxs-lookup"><span data-stu-id="1359c-195">Remove a service</span></span>

<span data-ttu-id="1359c-196">Dopo il breve lasso di tempo necessario per arrestare un servizio, rimuovere il servizio con il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1359c-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="1359c-197">Scenari con server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="1359c-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="1359c-198">I servizi che interagiscono con le richieste da Internet o da una rete aziendale e si trovano dietro un proxy o un servizio di bilanciamento del carico potrebbero richiedere una configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="1359c-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="1359c-199">Per altre informazioni, vedere <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="1359c-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="1359c-200">Configurare gli endpoint</span><span class="sxs-lookup"><span data-stu-id="1359c-200">Configure endpoints</span></span>

<span data-ttu-id="1359c-201">Per impostazione predefinita, ASP.NET Core è associato a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="1359c-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="1359c-202">Configurare l'URL e la porta impostando la `ASPNETCORE_URLS` variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="1359c-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="1359c-203">Per ulteriori approcci alla configurazione di porte e URL, vedere l'articolo relativo al server pertinente:</span><span class="sxs-lookup"><span data-stu-id="1359c-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="1359c-204">Le linee guida precedenti riguardano il supporto per gli endpoint HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1359c-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="1359c-205">Ad esempio, configurare l'app per HTTPS quando si usa l'autenticazione con un servizio Windows.</span><span class="sxs-lookup"><span data-stu-id="1359c-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="1359c-206">L'uso del certificato di sviluppo ASP.NET Core HTTPS per proteggere un endpoint del servizio non è supportato.</span><span class="sxs-lookup"><span data-stu-id="1359c-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="1359c-207">Directory corrente e radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="1359c-207">Current directory and content root</span></span>

<span data-ttu-id="1359c-208">La directory di lavoro corrente restituita chiamando <xref:System.IO.Directory.GetCurrentDirectory*> per un servizio Windows è la cartella *C:\\WINDOWS\\system32* .</span><span class="sxs-lookup"><span data-stu-id="1359c-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="1359c-209">La cartella *system32* non è un percorso appropriato per archiviare i file di un servizio, ad esempio i file di impostazioni.</span><span class="sxs-lookup"><span data-stu-id="1359c-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="1359c-210">Usare uno degli approcci seguenti per gestire e accedere agli asset e ai file di impostazioni di un servizio.</span><span class="sxs-lookup"><span data-stu-id="1359c-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="1359c-211">Usare ContentRootPath o ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="1359c-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="1359c-212">Usare [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) o <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> per individuare le risorse di un'app.</span><span class="sxs-lookup"><span data-stu-id="1359c-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="1359c-213">Quando l'app viene eseguita come servizio, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> imposta <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> su [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="1359c-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="1359c-214">I file di impostazioni predefinite dell'app *:::no-loc(appsettings.json):::* e *appSettings. { Environment}. JSON* , viene caricato dalla radice del contenuto dell'app chiamando [CreateDefaultBuilder durante la costruzione dell'host](xref:fundamentals/host/generic-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="1359c-214">The app's default settings files, *:::no-loc(appsettings.json):::* and *appsettings.{Environment}.json* , are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="1359c-215">Per gli altri file di impostazioni caricati dal codice Developer in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> , non è necessario chiamare <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> .</span><span class="sxs-lookup"><span data-stu-id="1359c-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="1359c-216">Nell'esempio seguente, il *custom_settings.js* nel file è presente nella radice del contenuto dell'app e viene caricato senza impostare esplicitamente un percorso di base:</span><span class="sxs-lookup"><span data-stu-id="1359c-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="1359c-217">Non tentare di usare <xref:System.IO.Directory.GetCurrentDirectory*> per ottenere un percorso di risorsa perché un'app di servizio Windows restituisce la cartella *C: \\ Windows \\ system32* come directory corrente.</span><span class="sxs-lookup"><span data-stu-id="1359c-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="1359c-218">Archiviare i file di un servizio in un percorso appropriato nel disco</span><span class="sxs-lookup"><span data-stu-id="1359c-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="1359c-219">Specificare un percorso assoluto con <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando si usa un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> per la cartella contenente i file.</span><span class="sxs-lookup"><span data-stu-id="1359c-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="1359c-220">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="1359c-220">Troubleshoot</span></span>

<span data-ttu-id="1359c-221">Per risolvere i problemi relativi a un'app di servizio Windows, vedere <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="1359c-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="1359c-222">Errori comuni</span><span class="sxs-lookup"><span data-stu-id="1359c-222">Common errors</span></span>

* <span data-ttu-id="1359c-223">È in uso una versione precedente o provvisoria di PowerShell.</span><span class="sxs-lookup"><span data-stu-id="1359c-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="1359c-224">Il servizio registrato non usa l'output **pubblicato** dell'app dal comando [DotNet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="1359c-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="1359c-225">L'output del comando [DotNet Build](/dotnet/core/tools/dotnet-build) non è supportato per la distribuzione di app.</span><span class="sxs-lookup"><span data-stu-id="1359c-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="1359c-226">Gli asset pubblicati si trovano in una delle cartelle seguenti, a seconda del tipo di distribuzione:</span><span class="sxs-lookup"><span data-stu-id="1359c-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="1359c-227">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="1359c-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="1359c-228">*bin/Release/{Target Framework}/{Runtime Identifier}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="1359c-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="1359c-229">Il servizio non è nello stato in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="1359c-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="1359c-230">I percorsi delle risorse utilizzate dall'app, ad esempio i certificati, non sono corretti.</span><span class="sxs-lookup"><span data-stu-id="1359c-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="1359c-231">Il percorso di base di un servizio Windows è *c: \\ Windows \\ system32* .</span><span class="sxs-lookup"><span data-stu-id="1359c-231">The base path of a Windows Service is *c:\\Windows\\System32* .</span></span>
* <span data-ttu-id="1359c-232">L'utente non dispone *di diritti di accesso come servizio* .</span><span class="sxs-lookup"><span data-stu-id="1359c-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="1359c-233">La password dell'utente è scaduta o non è stata passata correttamente durante l'esecuzione del `New-Service` comando di PowerShell.</span><span class="sxs-lookup"><span data-stu-id="1359c-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="1359c-234">L'app richiede l'autenticazione ASP.NET Core ma non è configurata per le connessioni protette (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="1359c-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="1359c-235">La porta dell'URL della richiesta non è corretta o non è configurata correttamente nell'app.</span><span class="sxs-lookup"><span data-stu-id="1359c-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="1359c-236">Log eventi di sistema e dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="1359c-236">System and Application Event Logs</span></span>

<span data-ttu-id="1359c-237">Accedere ai registri eventi di sistema e dell'applicazione:</span><span class="sxs-lookup"><span data-stu-id="1359c-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="1359c-238">Aprire il menu Start, cercare *Visualizzatore eventi* e selezionare l'app **Visualizzatore eventi** .</span><span class="sxs-lookup"><span data-stu-id="1359c-238">Open the Start menu, search for *Event Viewer* , and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="1359c-239">In **Visualizzatore eventi** aprire il nodo **Registri di Windows** .</span><span class="sxs-lookup"><span data-stu-id="1359c-239">In **Event Viewer** , open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="1359c-240">Selezionare **sistema** per aprire il registro eventi di sistema.</span><span class="sxs-lookup"><span data-stu-id="1359c-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="1359c-241">Selezionare **Applicazione** per aprire il log eventi dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="1359c-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="1359c-242">Cercare gli errori associati all'app in cui si è verificato il problema.</span><span class="sxs-lookup"><span data-stu-id="1359c-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="1359c-243">Eseguire l'app da un prompt dei comandi</span><span class="sxs-lookup"><span data-stu-id="1359c-243">Run the app at a command prompt</span></span>

<span data-ttu-id="1359c-244">Molti errori di avvio non producono informazioni utili nei log eventi.</span><span class="sxs-lookup"><span data-stu-id="1359c-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="1359c-245">È possibile individuare la causa di alcuni errori eseguendo l'app da un prompt dei comandi nel sistema host.</span><span class="sxs-lookup"><span data-stu-id="1359c-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="1359c-246">Per registrare dettagli aggiuntivi dall'app, abbassare il [livello di registrazione](xref:fundamentals/logging/index#log-level) o eseguire l'app nell' [ambiente di sviluppo](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="1359c-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="1359c-247">Cancella cache di pacchetti</span><span class="sxs-lookup"><span data-stu-id="1359c-247">Clear package caches</span></span>

<span data-ttu-id="1359c-248">Un'app funzionante potrebbe non riuscire immediatamente dopo l'aggiornamento del .NET Core SDK nel computer di sviluppo o la modifica delle versioni del pacchetto all'interno dell'app.</span><span class="sxs-lookup"><span data-stu-id="1359c-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="1359c-249">In alcuni casi i pacchetti incoerenti possono interrompere un'app quando si eseguono aggiornamenti principali.</span><span class="sxs-lookup"><span data-stu-id="1359c-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="1359c-250">La maggior parte di questi problemi può essere risolta attenendosi alle istruzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="1359c-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="1359c-251">Eliminare le cartelle *bin* e *obj* .</span><span class="sxs-lookup"><span data-stu-id="1359c-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="1359c-252">Cancellare le cache dei pacchetti eseguendo [le impostazioni locali di DotNet NuGet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) da una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="1359c-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="1359c-253">La cancellazione delle cache dei pacchetti può essere eseguita anche con lo strumento [nuget.exe](https://www.nuget.org/downloads) e l'esecuzione del comando `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="1359c-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="1359c-254">*nuget.exe* non è un'installazione inclusa con il sistema operativo desktop Windows e deve essere ottenuta separatamente dal [sito Web NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="1359c-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="1359c-255">Ripristinare e ricompilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="1359c-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="1359c-256">Eliminare tutti i file nella cartella di distribuzione nel server prima di ridistribuire l'app.</span><span class="sxs-lookup"><span data-stu-id="1359c-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="1359c-257">App lenta o bloccata</span><span class="sxs-lookup"><span data-stu-id="1359c-257">Slow or hanging app</span></span>

<span data-ttu-id="1359c-258">Un *dump di arresto anomalo* del sistema è uno snapshot della memoria del sistema e può contribuire a determinare la provocazione di un arresto anomalo dell'app, dell'avvio o dell'applicazione lenta.</span><span class="sxs-lookup"><span data-stu-id="1359c-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="1359c-259">Arresto anomalo o eccezione di un'app</span><span class="sxs-lookup"><span data-stu-id="1359c-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="1359c-260">Ottenere e analizzare un dump da [Segnalazione errori Windows](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="1359c-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="1359c-261">Creare una cartella per i file dump di arresto anomalo del sistema in `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="1359c-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="1359c-262">Eseguire lo [script di PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) con il nome dell'eseguibile dell'applicazione:</span><span class="sxs-lookup"><span data-stu-id="1359c-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="1359c-263">Eseguire l'app nelle condizioni che causano l'arresto anomalo.</span><span class="sxs-lookup"><span data-stu-id="1359c-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="1359c-264">Dopo che si è verificato l'arresto anomalo, eseguire lo [script di PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="1359c-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="1359c-265">Dopo l'arresto anomalo di un'app e la raccolta dei dump, l'app può terminare normalmente.</span><span class="sxs-lookup"><span data-stu-id="1359c-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="1359c-266">Lo script di PowerShell configura Segnalazione errori Windows per raccogliere fino a cinque dump per ogni app.</span><span class="sxs-lookup"><span data-stu-id="1359c-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="1359c-267">I dump di arresto anomalo del sistema potrebbero richiedere una grande quantità di spazio su disco (fino a diversi gigabyte ognuno).</span><span class="sxs-lookup"><span data-stu-id="1359c-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="1359c-268">L'app si blocca, si verifica un errore durante l'avvio o viene eseguita normalmente</span><span class="sxs-lookup"><span data-stu-id="1359c-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="1359c-269">Quando un'app si *blocca* (smette di rispondere ma non si arresta in modo anomalo), si verifica un errore durante l'avvio o viene eseguita normalmente, vedere [file di dump in modalità utente: scegliere lo strumento migliore](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) per selezionare uno strumento appropriato per produrre il dump.</span><span class="sxs-lookup"><span data-stu-id="1359c-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="1359c-270">Analizzare il dump</span><span class="sxs-lookup"><span data-stu-id="1359c-270">Analyze the dump</span></span>

<span data-ttu-id="1359c-271">È possibile analizzare un dump usando diversi approcci.</span><span class="sxs-lookup"><span data-stu-id="1359c-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="1359c-272">Per altre informazioni, vedere [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analisi di un file dump in modalità utente).</span><span class="sxs-lookup"><span data-stu-id="1359c-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1359c-273">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="1359c-273">Additional resources</span></span>

* <span data-ttu-id="1359c-274">[Configurazione dell'endpoint Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (include la configurazione HTTPS e il supporto SNI)</span><span class="sxs-lookup"><span data-stu-id="1359c-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="1359c-275">È possibile ospitare un'app ASP.NET Core in Windows come [servizio Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) senza usare IIS.</span><span class="sxs-lookup"><span data-stu-id="1359c-275">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="1359c-276">Quando è ospitata come servizio di Windows, l'app viene avviata automaticamente dopo il riavvio del server.</span><span class="sxs-lookup"><span data-stu-id="1359c-276">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="1359c-277">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1359c-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1359c-278">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="1359c-278">Prerequisites</span></span>

* [<span data-ttu-id="1359c-279">ASP.NET Core SDK 2.1 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="1359c-279">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="1359c-280">PowerShell 6,2 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="1359c-280">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="1359c-281">Configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="1359c-281">App configuration</span></span>

<span data-ttu-id="1359c-282">L'app richiede i riferimenti ai pacchetti [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) e [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="1359c-282">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="1359c-283">Per eseguire test e debug durante l'esecuzione all'esterno di un servizio, aggiungere il codice per determinare se l'app è in esecuzione come servizio o è un'app console.</span><span class="sxs-lookup"><span data-stu-id="1359c-283">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="1359c-284">Controllare se il debugger è collegato o se è presente un'opzione `--console`.</span><span class="sxs-lookup"><span data-stu-id="1359c-284">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="1359c-285">Se una delle due condizioni è vera (l'app non è eseguita come servizio), chiamare <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="1359c-285">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="1359c-286">Se le condizioni sono false (l'app è eseguita come servizio):</span><span class="sxs-lookup"><span data-stu-id="1359c-286">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="1359c-287">Chiamare <xref:System.IO.Directory.SetCurrentDirectory*> e usare un percorso per la posizione di pubblicazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="1359c-287">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="1359c-288">Non chiamare <xref:System.IO.Directory.GetCurrentDirectory*> per ottenere il percorso perché un'app servizio Windows restituisce la cartella *C:\\WINDOWS\\system32* quando viene chiamato <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="1359c-288">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="1359c-289">Per altre informazioni, vedere la sezione [Directory corrente e radice del contenuto](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="1359c-289">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="1359c-290">Questo passaggio viene eseguito prima che l'app venga configurata in `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="1359c-290">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="1359c-291">Chiamare <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> per eseguire l'app come servizio.</span><span class="sxs-lookup"><span data-stu-id="1359c-291">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="1359c-292">Dato che il [provider di configurazione della riga di comando](xref:fundamentals/configuration/index#command-line-configuration-provider) richiede coppie nome-valore per gli argomenti della riga di comando, l'opzione `--console` viene rimossa dagli argomenti prima che <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> riceva gli argomenti.</span><span class="sxs-lookup"><span data-stu-id="1359c-292">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="1359c-293">Per scrivere nel registro eventi di Windows, aggiungere il provider EventLog a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="1359c-293">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="1359c-294">Impostare il livello di registrazione con la chiave `Logging:LogLevel:Default` nel file *appsettings.Production.json* .</span><span class="sxs-lookup"><span data-stu-id="1359c-294">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="1359c-295">Nel seguente esempio dell'app di esempio, viene chiamato `RunAsCustomService` invece di <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> per gestire gli eventi di durata all'interno dell'app.</span><span class="sxs-lookup"><span data-stu-id="1359c-295">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="1359c-296">Per altre informazioni, vedere la sezione [Gestire gli eventi di avvio e arresto](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="1359c-296">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="1359c-297">Tipo di distribuzione</span><span class="sxs-lookup"><span data-stu-id="1359c-297">Deployment type</span></span>

<span data-ttu-id="1359c-298">Per informazioni e consigli sugli scenari di distribuzione, vedere [Distribuzione di applicazioni .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="1359c-298">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="1359c-299">SDK</span><span class="sxs-lookup"><span data-stu-id="1359c-299">SDK</span></span>

<span data-ttu-id="1359c-300">Per un servizio basato su app Web che usa le :::no-loc(Razor)::: pagine o i framework MVC, specificare Web SDK nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="1359c-300">For a web app-based service that uses the :::no-loc(Razor)::: Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="1359c-301">Se il servizio esegue solo attività in background, ad esempio [servizi ospitati](xref:fundamentals/host/hosted-services), specificare l'SDK del ruolo di lavoro nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="1359c-301">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="1359c-302">Distribuzione dipendente dal framework</span><span class="sxs-lookup"><span data-stu-id="1359c-302">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="1359c-303">La distribuzione dipendente dal framework si basa sulla presenza di una versione condivisa a livello di sistema di .NET Core nel sistema di destinazione.</span><span class="sxs-lookup"><span data-stu-id="1359c-303">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="1359c-304">Quando lo scenario di distribuzione dipendente dal framework viene implementato in base alle indicazioni di questo articolo, l'SDK genera un file eseguibile (con estensione *exe* ) detto *eseguibile dipendente dal framework* .</span><span class="sxs-lookup"><span data-stu-id="1359c-304">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable ( *.exe* ), called a *framework-dependent executable* .</span></span>

<span data-ttu-id="1359c-305">L'identificatore di Windows [Runtime (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) contiene il Framework di destinazione.</span><span class="sxs-lookup"><span data-stu-id="1359c-305">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="1359c-306">Nell'esempio seguente il RID è impostato su `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="1359c-306">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="1359c-307">La proprietà `<SelfContained>` è impostata su `false`.</span><span class="sxs-lookup"><span data-stu-id="1359c-307">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="1359c-308">Queste proprietà indicano all'SDK di generare un file eseguibile (con estensione *exe* ) per Windows e un'app che dipende dal framework .NET Core condiviso.</span><span class="sxs-lookup"><span data-stu-id="1359c-308">These properties instruct the SDK to generate an executable ( *.exe* ) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="1359c-309">Un file *web.config* , che viene normalmente generato quando si pubblica un'app ASP.NET Core, non è necessario per un'app di servizi Windows.</span><span class="sxs-lookup"><span data-stu-id="1359c-309">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="1359c-310">Per disabilitare la creazione del file *web.config* , aggiungere la proprietà `<IsTransformWebConfigDisabled>` impostata su `true`.</span><span class="sxs-lookup"><span data-stu-id="1359c-310">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="1359c-311">Distribuzione autonoma</span><span class="sxs-lookup"><span data-stu-id="1359c-311">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="1359c-312">Una distribuzione autonoma non si basa sulla presenza di un framework condiviso nel sistema host.</span><span class="sxs-lookup"><span data-stu-id="1359c-312">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="1359c-313">Il runtime e le dipendenze dell'app vengono distribuiti con l'app.</span><span class="sxs-lookup"><span data-stu-id="1359c-313">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="1359c-314">Un [identificatore di runtime (RID)](/dotnet/core/rid-catalog) di Windows viene incluso nel `<PropertyGroup>` che contiene il framework di destinazione:</span><span class="sxs-lookup"><span data-stu-id="1359c-314">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="1359c-315">Per eseguire la pubblicazione per più identificatori di runtime:</span><span class="sxs-lookup"><span data-stu-id="1359c-315">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="1359c-316">Specificare gli identificatori di runtime in un elenco delimitato da punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="1359c-316">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="1359c-317">Usare il nome della proprietà [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plurale).</span><span class="sxs-lookup"><span data-stu-id="1359c-317">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="1359c-318">Per altre informazioni, vedere il [Catalogo RID di .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="1359c-318">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="1359c-319">Una proprietà `<SelfContained>` è impostata su `true`:</span><span class="sxs-lookup"><span data-stu-id="1359c-319">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="1359c-320">Account utente del servizio</span><span class="sxs-lookup"><span data-stu-id="1359c-320">Service user account</span></span>

<span data-ttu-id="1359c-321">Per creare un account utente per un servizio, usare il cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) da una shell dei comandi di PowerShell 6 amministrativa.</span><span class="sxs-lookup"><span data-stu-id="1359c-321">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="1359c-322">In Aggiornamento di Windows 10 (ottobre 2018) (versione 1809/build 10.0.17763) o versioni successive:</span><span class="sxs-lookup"><span data-stu-id="1359c-322">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="1359c-323">In un sistema operativo Windows precedente ad Aggiornamento di Windows 10 (ottobre 2018) (versione 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="1359c-323">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="1359c-324">Fornire una [password complessa](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando richiesto.</span><span class="sxs-lookup"><span data-stu-id="1359c-324">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="1359c-325">Se non viene specificato il parametro `-AccountExpires` per il cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) con un valore <xref:System.DateTime> di scadenza, l'account non scade.</span><span class="sxs-lookup"><span data-stu-id="1359c-325">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="1359c-326">Per altre informazioni, vedere [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Service User Accounts](/windows/desktop/services/service-user-accounts) (Account utente di servizio).</span><span class="sxs-lookup"><span data-stu-id="1359c-326">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="1359c-327">Un approccio alternativo per la gestione degli utenti quando si usa Active Directory consiste nell'usare account del servizio gestito.</span><span class="sxs-lookup"><span data-stu-id="1359c-327">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="1359c-328">Per altre informazioni, vedere [Panoramica degli account del servizio gestito del gruppo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="1359c-328">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="1359c-329">Diritti Accesso come servizio</span><span class="sxs-lookup"><span data-stu-id="1359c-329">Log on as a service rights</span></span>

<span data-ttu-id="1359c-330">Per stabilire i diritti *Accesso come servizio* per un account utente di servizio:</span><span class="sxs-lookup"><span data-stu-id="1359c-330">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="1359c-331">Aprire l'editor Criteri di sicurezza locali eseguendo *secpol.msc* .</span><span class="sxs-lookup"><span data-stu-id="1359c-331">Open the Local Security Policy editor by running *secpol.msc* .</span></span>
1. <span data-ttu-id="1359c-332">Espandere il nodo **Criteri locali** e selezionare **Assegnazione diritti utente** .</span><span class="sxs-lookup"><span data-stu-id="1359c-332">Expand the **Local Policies** node and select **User Rights Assignment** .</span></span>
1. <span data-ttu-id="1359c-333">Aprire il criterio **Accesso come servizio** .</span><span class="sxs-lookup"><span data-stu-id="1359c-333">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="1359c-334">Selezionare **Aggiungi utente o gruppo** .</span><span class="sxs-lookup"><span data-stu-id="1359c-334">Select **Add User or Group** .</span></span>
1. <span data-ttu-id="1359c-335">Specificare il nome oggetto (account utente) in uno dei modi seguenti:</span><span class="sxs-lookup"><span data-stu-id="1359c-335">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="1359c-336">Digitare l'account utente (`{DOMAIN OR COMPUTER NAME\USER}`) nel campo del nome oggetto e scegliere **OK** per aggiungere l'utente al criterio.</span><span class="sxs-lookup"><span data-stu-id="1359c-336">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="1359c-337">Fare clic su **Advanced** (Avanzate).</span><span class="sxs-lookup"><span data-stu-id="1359c-337">Select **Advanced** .</span></span> <span data-ttu-id="1359c-338">Selezionare **Trova** .</span><span class="sxs-lookup"><span data-stu-id="1359c-338">Select **Find Now** .</span></span> <span data-ttu-id="1359c-339">Selezionare l'account utente dall'elenco.</span><span class="sxs-lookup"><span data-stu-id="1359c-339">Select the user account from the list.</span></span> <span data-ttu-id="1359c-340">Selezionare **OK** .</span><span class="sxs-lookup"><span data-stu-id="1359c-340">Select **OK** .</span></span> <span data-ttu-id="1359c-341">Scegliere di nuovo **OK** per aggiungere l'utente al criterio.</span><span class="sxs-lookup"><span data-stu-id="1359c-341">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="1359c-342">Scegliere **OK** o **Applica** per accettare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="1359c-342">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="1359c-343">Creare e gestire il servizio di Windows</span><span class="sxs-lookup"><span data-stu-id="1359c-343">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="1359c-344">Creare un servizio</span><span class="sxs-lookup"><span data-stu-id="1359c-344">Create a service</span></span>

<span data-ttu-id="1359c-345">Usare i comandi di PowerShell per registrare un servizio.</span><span class="sxs-lookup"><span data-stu-id="1359c-345">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="1359c-346">Da una shell dei comandi di PowerShell 6 amministrativa eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="1359c-346">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="1359c-347">`{EXE PATH}`: Percorso della cartella dell'app nell'host (ad esempio, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="1359c-347">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="1359c-348">Non includere l'eseguibile dell'app nel percorso.</span><span class="sxs-lookup"><span data-stu-id="1359c-348">Don't include the app's executable in the path.</span></span> <span data-ttu-id="1359c-349">Non è necessario aggiungere una barra finale.</span><span class="sxs-lookup"><span data-stu-id="1359c-349">A trailing slash isn't required.</span></span>
* <span data-ttu-id="1359c-350">`{DOMAIN OR COMPUTER NAME\USER}`: Account utente del servizio (ad esempio, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="1359c-350">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="1359c-351">`{SERVICE NAME}`: Nome del servizio (ad esempio, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="1359c-351">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="1359c-352">`{EXE FILE PATH}`: Percorso eseguibile dell'app (ad esempio, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="1359c-352">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="1359c-353">Includere il nome del file eseguibile con l'estensione.</span><span class="sxs-lookup"><span data-stu-id="1359c-353">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="1359c-354">`{DESCRIPTION}`: Descrizione del servizio (ad esempio, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="1359c-354">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="1359c-355">`{DISPLAY NAME}`: Nome visualizzato del servizio (ad esempio, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="1359c-355">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="1359c-356">Avviare un servizio</span><span class="sxs-lookup"><span data-stu-id="1359c-356">Start a service</span></span>

<span data-ttu-id="1359c-357">Per avviare un servizio, usare il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1359c-357">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="1359c-358">L'avvio del servizio richiede alcuni secondi.</span><span class="sxs-lookup"><span data-stu-id="1359c-358">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="1359c-359">Determinare lo stato di un servizio</span><span class="sxs-lookup"><span data-stu-id="1359c-359">Determine a service's status</span></span>

<span data-ttu-id="1359c-360">Per verificare lo stato di un servizio, usare il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1359c-360">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="1359c-361">Lo stato viene indicato con uno dei valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="1359c-361">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="1359c-362">Arrestare un servizio</span><span class="sxs-lookup"><span data-stu-id="1359c-362">Stop a service</span></span>

<span data-ttu-id="1359c-363">Per arrestare un servizio, usare il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1359c-363">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="1359c-364">Rimuovere un servizio</span><span class="sxs-lookup"><span data-stu-id="1359c-364">Remove a service</span></span>

<span data-ttu-id="1359c-365">Dopo il breve lasso di tempo necessario per arrestare un servizio, rimuovere il servizio con il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1359c-365">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="1359c-366">Gestire gli eventi di avvio e arresto</span><span class="sxs-lookup"><span data-stu-id="1359c-366">Handle starting and stopping events</span></span>

<span data-ttu-id="1359c-367">Per gestire gli eventi <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:</span><span class="sxs-lookup"><span data-stu-id="1359c-367">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="1359c-368">Creare una classe che deriva da <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> con i metodi `OnStarting`, `OnStarted` e `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="1359c-368">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="1359c-369">Creare un metodo di estensione per <xref:Microsoft.AspNetCore.Hosting.IWebHost> che passa `CustomWebHostService` a <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="1359c-369">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="1359c-370">In `Program.Main` chiamare il metodo di estensione `RunAsCustomService` invece di <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="1359c-370">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="1359c-371">Per visualizzare il percorso di <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, fare riferimento all'esempio di codice illustrato nella sezione [Tipo di distribuzione](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="1359c-371">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="1359c-372">Scenari con server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="1359c-372">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="1359c-373">I servizi che interagiscono con le richieste da Internet o da una rete aziendale e si trovano dietro un proxy o un servizio di bilanciamento del carico potrebbero richiedere una configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="1359c-373">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="1359c-374">Per altre informazioni, vedere <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="1359c-374">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="1359c-375">Configurare gli endpoint</span><span class="sxs-lookup"><span data-stu-id="1359c-375">Configure endpoints</span></span>

<span data-ttu-id="1359c-376">Per impostazione predefinita, ASP.NET Core è associato a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="1359c-376">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="1359c-377">Configurare l'URL e la porta impostando la `ASPNETCORE_URLS` variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="1359c-377">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="1359c-378">Per ulteriori approcci alla configurazione di porte e URL, vedere l'articolo relativo al server pertinente:</span><span class="sxs-lookup"><span data-stu-id="1359c-378">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="1359c-379">Le linee guida precedenti riguardano il supporto per gli endpoint HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1359c-379">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="1359c-380">Ad esempio, configurare l'app per HTTPS quando si usa l'autenticazione con un servizio Windows.</span><span class="sxs-lookup"><span data-stu-id="1359c-380">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="1359c-381">L'uso del certificato di sviluppo ASP.NET Core HTTPS per proteggere un endpoint del servizio non è supportato.</span><span class="sxs-lookup"><span data-stu-id="1359c-381">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="1359c-382">Directory corrente e radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="1359c-382">Current directory and content root</span></span>

<span data-ttu-id="1359c-383">La directory di lavoro corrente restituita chiamando <xref:System.IO.Directory.GetCurrentDirectory*> per un servizio Windows è la cartella *C:\\WINDOWS\\system32* .</span><span class="sxs-lookup"><span data-stu-id="1359c-383">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="1359c-384">La cartella *system32* non è un percorso appropriato per archiviare i file di un servizio, ad esempio i file di impostazioni.</span><span class="sxs-lookup"><span data-stu-id="1359c-384">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="1359c-385">Usare uno degli approcci seguenti per gestire e accedere agli asset e ai file di impostazioni di un servizio.</span><span class="sxs-lookup"><span data-stu-id="1359c-385">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="1359c-386">Impostare il percorso radice del contenuto sulla cartella dell'app</span><span class="sxs-lookup"><span data-stu-id="1359c-386">Set the content root path to the app's folder</span></span>

<span data-ttu-id="1359c-387"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> è lo stesso percorso fornito all'argomento `binPath` durante la creazione di un servizio.</span><span class="sxs-lookup"><span data-stu-id="1359c-387">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="1359c-388">Anziché chiamare `GetCurrentDirectory` per creare percorsi per i file di impostazioni, chiamare <xref:System.IO.Directory.SetCurrentDirectory*> con il percorso della radice del [contenuto](xref:fundamentals/index#content-root)dell'app.</span><span class="sxs-lookup"><span data-stu-id="1359c-388">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="1359c-389">In `Program.Main`, determinare il percorso della cartella dell'eseguibile del servizio e usare il percorso per stabilire la radice del contenuto dell'app:</span><span class="sxs-lookup"><span data-stu-id="1359c-389">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="1359c-390">Archiviare i file di un servizio in un percorso appropriato nel disco</span><span class="sxs-lookup"><span data-stu-id="1359c-390">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="1359c-391">Specificare un percorso assoluto con <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando si usa un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> per la cartella contenente i file.</span><span class="sxs-lookup"><span data-stu-id="1359c-391">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="1359c-392">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="1359c-392">Troubleshoot</span></span>

<span data-ttu-id="1359c-393">Per risolvere i problemi relativi a un'app di servizio Windows, vedere <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="1359c-393">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="1359c-394">Errori comuni</span><span class="sxs-lookup"><span data-stu-id="1359c-394">Common errors</span></span>

* <span data-ttu-id="1359c-395">È in uso una versione precedente o provvisoria di PowerShell.</span><span class="sxs-lookup"><span data-stu-id="1359c-395">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="1359c-396">Il servizio registrato non usa l'output **pubblicato** dell'app dal comando [DotNet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="1359c-396">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="1359c-397">L'output del comando [DotNet Build](/dotnet/core/tools/dotnet-build) non è supportato per la distribuzione di app.</span><span class="sxs-lookup"><span data-stu-id="1359c-397">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="1359c-398">Gli asset pubblicati si trovano in una delle cartelle seguenti, a seconda del tipo di distribuzione:</span><span class="sxs-lookup"><span data-stu-id="1359c-398">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="1359c-399">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="1359c-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="1359c-400">*bin/Release/{Target Framework}/{Runtime Identifier}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="1359c-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="1359c-401">Il servizio non è nello stato in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="1359c-401">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="1359c-402">I percorsi delle risorse utilizzate dall'app, ad esempio i certificati, non sono corretti.</span><span class="sxs-lookup"><span data-stu-id="1359c-402">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="1359c-403">Il percorso di base di un servizio Windows è *c: \\ Windows \\ system32* .</span><span class="sxs-lookup"><span data-stu-id="1359c-403">The base path of a Windows Service is *c:\\Windows\\System32* .</span></span>
* <span data-ttu-id="1359c-404">L'utente non dispone *di diritti di accesso come servizio* .</span><span class="sxs-lookup"><span data-stu-id="1359c-404">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="1359c-405">La password dell'utente è scaduta o non è stata passata correttamente durante l'esecuzione del `New-Service` comando di PowerShell.</span><span class="sxs-lookup"><span data-stu-id="1359c-405">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="1359c-406">L'app richiede l'autenticazione ASP.NET Core ma non è configurata per le connessioni protette (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="1359c-406">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="1359c-407">La porta dell'URL della richiesta non è corretta o non è configurata correttamente nell'app.</span><span class="sxs-lookup"><span data-stu-id="1359c-407">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="1359c-408">Log eventi di sistema e dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="1359c-408">System and Application Event Logs</span></span>

<span data-ttu-id="1359c-409">Accedere ai registri eventi di sistema e dell'applicazione:</span><span class="sxs-lookup"><span data-stu-id="1359c-409">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="1359c-410">Aprire il menu Start, cercare *Visualizzatore eventi* e selezionare l'app **Visualizzatore eventi** .</span><span class="sxs-lookup"><span data-stu-id="1359c-410">Open the Start menu, search for *Event Viewer* , and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="1359c-411">In **Visualizzatore eventi** aprire il nodo **Registri di Windows** .</span><span class="sxs-lookup"><span data-stu-id="1359c-411">In **Event Viewer** , open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="1359c-412">Selezionare **sistema** per aprire il registro eventi di sistema.</span><span class="sxs-lookup"><span data-stu-id="1359c-412">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="1359c-413">Selezionare **Applicazione** per aprire il log eventi dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="1359c-413">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="1359c-414">Cercare gli errori associati all'app in cui si è verificato il problema.</span><span class="sxs-lookup"><span data-stu-id="1359c-414">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="1359c-415">Eseguire l'app da un prompt dei comandi</span><span class="sxs-lookup"><span data-stu-id="1359c-415">Run the app at a command prompt</span></span>

<span data-ttu-id="1359c-416">Molti errori di avvio non producono informazioni utili nei log eventi.</span><span class="sxs-lookup"><span data-stu-id="1359c-416">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="1359c-417">È possibile individuare la causa di alcuni errori eseguendo l'app da un prompt dei comandi nel sistema host.</span><span class="sxs-lookup"><span data-stu-id="1359c-417">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="1359c-418">Per registrare dettagli aggiuntivi dall'app, abbassare il [livello di registrazione](xref:fundamentals/logging/index#log-level) o eseguire l'app nell' [ambiente di sviluppo](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="1359c-418">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="1359c-419">Cancella cache di pacchetti</span><span class="sxs-lookup"><span data-stu-id="1359c-419">Clear package caches</span></span>

<span data-ttu-id="1359c-420">Un'app funzionante potrebbe non riuscire immediatamente dopo l'aggiornamento del .NET Core SDK nel computer di sviluppo o la modifica delle versioni del pacchetto all'interno dell'app.</span><span class="sxs-lookup"><span data-stu-id="1359c-420">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="1359c-421">In alcuni casi i pacchetti incoerenti possono interrompere un'app quando si eseguono aggiornamenti principali.</span><span class="sxs-lookup"><span data-stu-id="1359c-421">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="1359c-422">La maggior parte di questi problemi può essere risolta attenendosi alle istruzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="1359c-422">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="1359c-423">Eliminare le cartelle *bin* e *obj* .</span><span class="sxs-lookup"><span data-stu-id="1359c-423">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="1359c-424">Cancellare le cache dei pacchetti eseguendo [le impostazioni locali di DotNet NuGet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) da una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="1359c-424">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="1359c-425">La cancellazione delle cache dei pacchetti può essere eseguita anche con lo strumento [nuget.exe](https://www.nuget.org/downloads) e l'esecuzione del comando `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="1359c-425">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="1359c-426">*nuget.exe* non è un'installazione inclusa con il sistema operativo desktop Windows e deve essere ottenuta separatamente dal [sito Web NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="1359c-426">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="1359c-427">Ripristinare e ricompilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="1359c-427">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="1359c-428">Eliminare tutti i file nella cartella di distribuzione nel server prima di ridistribuire l'app.</span><span class="sxs-lookup"><span data-stu-id="1359c-428">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="1359c-429">App lenta o bloccata</span><span class="sxs-lookup"><span data-stu-id="1359c-429">Slow or hanging app</span></span>

<span data-ttu-id="1359c-430">Un *dump di arresto anomalo* del sistema è uno snapshot della memoria del sistema e può contribuire a determinare la provocazione di un arresto anomalo dell'app, dell'avvio o dell'applicazione lenta.</span><span class="sxs-lookup"><span data-stu-id="1359c-430">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="1359c-431">Arresto anomalo o eccezione di un'app</span><span class="sxs-lookup"><span data-stu-id="1359c-431">App crashes or encounters an exception</span></span>

<span data-ttu-id="1359c-432">Ottenere e analizzare un dump da [Segnalazione errori Windows](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="1359c-432">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="1359c-433">Creare una cartella per i file dump di arresto anomalo del sistema in `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="1359c-433">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="1359c-434">Eseguire lo [script di PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) con il nome dell'eseguibile dell'applicazione:</span><span class="sxs-lookup"><span data-stu-id="1359c-434">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="1359c-435">Eseguire l'app nelle condizioni che causano l'arresto anomalo.</span><span class="sxs-lookup"><span data-stu-id="1359c-435">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="1359c-436">Dopo che si è verificato l'arresto anomalo, eseguire lo [script di PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="1359c-436">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="1359c-437">Dopo l'arresto anomalo di un'app e la raccolta dei dump, l'app può terminare normalmente.</span><span class="sxs-lookup"><span data-stu-id="1359c-437">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="1359c-438">Lo script di PowerShell configura Segnalazione errori Windows per raccogliere fino a cinque dump per ogni app.</span><span class="sxs-lookup"><span data-stu-id="1359c-438">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="1359c-439">I dump di arresto anomalo del sistema potrebbero richiedere una grande quantità di spazio su disco (fino a diversi gigabyte ognuno).</span><span class="sxs-lookup"><span data-stu-id="1359c-439">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="1359c-440">L'app si blocca, si verifica un errore durante l'avvio o viene eseguita normalmente</span><span class="sxs-lookup"><span data-stu-id="1359c-440">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="1359c-441">Quando un'app si *blocca* (smette di rispondere ma non si arresta in modo anomalo), si verifica un errore durante l'avvio o viene eseguita normalmente, vedere [file di dump in modalità utente: scegliere lo strumento migliore](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) per selezionare uno strumento appropriato per produrre il dump.</span><span class="sxs-lookup"><span data-stu-id="1359c-441">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="1359c-442">Analizzare il dump</span><span class="sxs-lookup"><span data-stu-id="1359c-442">Analyze the dump</span></span>

<span data-ttu-id="1359c-443">È possibile analizzare un dump usando diversi approcci.</span><span class="sxs-lookup"><span data-stu-id="1359c-443">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="1359c-444">Per altre informazioni, vedere [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analisi di un file dump in modalità utente).</span><span class="sxs-lookup"><span data-stu-id="1359c-444">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1359c-445">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="1359c-445">Additional resources</span></span>

* <span data-ttu-id="1359c-446">[Configurazione dell'endpoint Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (include la configurazione HTTPS e il supporto SNI)</span><span class="sxs-lookup"><span data-stu-id="1359c-446">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="1359c-447">È possibile ospitare un'app ASP.NET Core in Windows come [servizio Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) senza usare IIS.</span><span class="sxs-lookup"><span data-stu-id="1359c-447">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="1359c-448">Quando è ospitata come servizio di Windows, l'app viene avviata automaticamente dopo il riavvio del server.</span><span class="sxs-lookup"><span data-stu-id="1359c-448">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="1359c-449">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1359c-449">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1359c-450">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="1359c-450">Prerequisites</span></span>

* [<span data-ttu-id="1359c-451">ASP.NET Core SDK 2.1 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="1359c-451">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="1359c-452">PowerShell 6,2 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="1359c-452">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="1359c-453">Configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="1359c-453">App configuration</span></span>

<span data-ttu-id="1359c-454">L'app richiede i riferimenti ai pacchetti [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) e [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="1359c-454">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="1359c-455">Per eseguire test e debug durante l'esecuzione all'esterno di un servizio, aggiungere il codice per determinare se l'app è in esecuzione come servizio o è un'app console.</span><span class="sxs-lookup"><span data-stu-id="1359c-455">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="1359c-456">Controllare se il debugger è collegato o se è presente un'opzione `--console`.</span><span class="sxs-lookup"><span data-stu-id="1359c-456">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="1359c-457">Se una delle due condizioni è vera (l'app non è eseguita come servizio), chiamare <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="1359c-457">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="1359c-458">Se le condizioni sono false (l'app è eseguita come servizio):</span><span class="sxs-lookup"><span data-stu-id="1359c-458">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="1359c-459">Chiamare <xref:System.IO.Directory.SetCurrentDirectory*> e usare un percorso per la posizione di pubblicazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="1359c-459">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="1359c-460">Non chiamare <xref:System.IO.Directory.GetCurrentDirectory*> per ottenere il percorso perché un'app servizio Windows restituisce la cartella *C:\\WINDOWS\\system32* quando viene chiamato <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="1359c-460">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="1359c-461">Per altre informazioni, vedere la sezione [Directory corrente e radice del contenuto](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="1359c-461">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="1359c-462">Questo passaggio viene eseguito prima che l'app venga configurata in `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="1359c-462">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="1359c-463">Chiamare <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> per eseguire l'app come servizio.</span><span class="sxs-lookup"><span data-stu-id="1359c-463">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="1359c-464">Dato che il [provider di configurazione della riga di comando](xref:fundamentals/configuration/index#command-line-configuration-provider) richiede coppie nome-valore per gli argomenti della riga di comando, l'opzione `--console` viene rimossa dagli argomenti prima che <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> riceva gli argomenti.</span><span class="sxs-lookup"><span data-stu-id="1359c-464">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="1359c-465">Per scrivere nel registro eventi di Windows, aggiungere il provider EventLog a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="1359c-465">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="1359c-466">Impostare il livello di registrazione con la chiave `Logging:LogLevel:Default` nel file *appsettings.Production.json* .</span><span class="sxs-lookup"><span data-stu-id="1359c-466">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="1359c-467">Nel seguente esempio dell'app di esempio, viene chiamato `RunAsCustomService` invece di <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> per gestire gli eventi di durata all'interno dell'app.</span><span class="sxs-lookup"><span data-stu-id="1359c-467">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="1359c-468">Per altre informazioni, vedere la sezione [Gestire gli eventi di avvio e arresto](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="1359c-468">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="1359c-469">Tipo di distribuzione</span><span class="sxs-lookup"><span data-stu-id="1359c-469">Deployment type</span></span>

<span data-ttu-id="1359c-470">Per informazioni e consigli sugli scenari di distribuzione, vedere [Distribuzione di applicazioni .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="1359c-470">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="1359c-471">SDK</span><span class="sxs-lookup"><span data-stu-id="1359c-471">SDK</span></span>

<span data-ttu-id="1359c-472">Per un servizio basato su app Web che usa le :::no-loc(Razor)::: pagine o i framework MVC, specificare Web SDK nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="1359c-472">For a web app-based service that uses the :::no-loc(Razor)::: Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="1359c-473">Se il servizio esegue solo attività in background, ad esempio [servizi ospitati](xref:fundamentals/host/hosted-services), specificare l'SDK del ruolo di lavoro nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="1359c-473">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="1359c-474">Distribuzione dipendente dal framework</span><span class="sxs-lookup"><span data-stu-id="1359c-474">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="1359c-475">La distribuzione dipendente dal framework si basa sulla presenza di una versione condivisa a livello di sistema di .NET Core nel sistema di destinazione.</span><span class="sxs-lookup"><span data-stu-id="1359c-475">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="1359c-476">Quando lo scenario di distribuzione dipendente dal framework viene implementato in base alle indicazioni di questo articolo, l'SDK genera un file eseguibile (con estensione *exe* ) detto *eseguibile dipendente dal framework* .</span><span class="sxs-lookup"><span data-stu-id="1359c-476">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable ( *.exe* ), called a *framework-dependent executable* .</span></span>

<span data-ttu-id="1359c-477">L'identificatore di Windows [Runtime (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) contiene il Framework di destinazione.</span><span class="sxs-lookup"><span data-stu-id="1359c-477">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="1359c-478">Nell'esempio seguente il RID è impostato su `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="1359c-478">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="1359c-479">La proprietà `<SelfContained>` è impostata su `false`.</span><span class="sxs-lookup"><span data-stu-id="1359c-479">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="1359c-480">Queste proprietà indicano all'SDK di generare un file eseguibile (con estensione *exe* ) per Windows e un'app che dipende dal framework .NET Core condiviso.</span><span class="sxs-lookup"><span data-stu-id="1359c-480">These properties instruct the SDK to generate an executable ( *.exe* ) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="1359c-481">La proprietà `<UseAppHost>` è impostata su `true`.</span><span class="sxs-lookup"><span data-stu-id="1359c-481">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="1359c-482">Questa proprietà fornisce il servizio con un percorso di attivazione (un file eseguibile, *.exe* ) per una distribuzione dipendente dal framework.</span><span class="sxs-lookup"><span data-stu-id="1359c-482">This property provides the service with an activation path (an executable, *.exe* ) for an FDD.</span></span>

<span data-ttu-id="1359c-483">Un file *web.config* , che viene normalmente generato quando si pubblica un'app ASP.NET Core, non è necessario per un'app di servizi Windows.</span><span class="sxs-lookup"><span data-stu-id="1359c-483">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="1359c-484">Per disabilitare la creazione del file *web.config* , aggiungere la proprietà `<IsTransformWebConfigDisabled>` impostata su `true`.</span><span class="sxs-lookup"><span data-stu-id="1359c-484">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="1359c-485">Distribuzione autonoma</span><span class="sxs-lookup"><span data-stu-id="1359c-485">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="1359c-486">Una distribuzione autonoma non si basa sulla presenza di un framework condiviso nel sistema host.</span><span class="sxs-lookup"><span data-stu-id="1359c-486">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="1359c-487">Il runtime e le dipendenze dell'app vengono distribuiti con l'app.</span><span class="sxs-lookup"><span data-stu-id="1359c-487">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="1359c-488">Un [identificatore di runtime (RID)](/dotnet/core/rid-catalog) di Windows viene incluso nel `<PropertyGroup>` che contiene il framework di destinazione:</span><span class="sxs-lookup"><span data-stu-id="1359c-488">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="1359c-489">Per eseguire la pubblicazione per più identificatori di runtime:</span><span class="sxs-lookup"><span data-stu-id="1359c-489">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="1359c-490">Specificare gli identificatori di runtime in un elenco delimitato da punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="1359c-490">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="1359c-491">Usare il nome della proprietà [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plurale).</span><span class="sxs-lookup"><span data-stu-id="1359c-491">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="1359c-492">Per altre informazioni, vedere il [Catalogo RID di .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="1359c-492">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="1359c-493">Una proprietà `<SelfContained>` è impostata su `true`:</span><span class="sxs-lookup"><span data-stu-id="1359c-493">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="1359c-494">Account utente del servizio</span><span class="sxs-lookup"><span data-stu-id="1359c-494">Service user account</span></span>

<span data-ttu-id="1359c-495">Per creare un account utente per un servizio, usare il cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) da una shell dei comandi di PowerShell 6 amministrativa.</span><span class="sxs-lookup"><span data-stu-id="1359c-495">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="1359c-496">In Aggiornamento di Windows 10 (ottobre 2018) (versione 1809/build 10.0.17763) o versioni successive:</span><span class="sxs-lookup"><span data-stu-id="1359c-496">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="1359c-497">In un sistema operativo Windows precedente ad Aggiornamento di Windows 10 (ottobre 2018) (versione 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="1359c-497">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="1359c-498">Fornire una [password complessa](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando richiesto.</span><span class="sxs-lookup"><span data-stu-id="1359c-498">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="1359c-499">Se non viene specificato il parametro `-AccountExpires` per il cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) con un valore <xref:System.DateTime> di scadenza, l'account non scade.</span><span class="sxs-lookup"><span data-stu-id="1359c-499">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="1359c-500">Per altre informazioni, vedere [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Service User Accounts](/windows/desktop/services/service-user-accounts) (Account utente di servizio).</span><span class="sxs-lookup"><span data-stu-id="1359c-500">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="1359c-501">Un approccio alternativo per la gestione degli utenti quando si usa Active Directory consiste nell'usare account del servizio gestito.</span><span class="sxs-lookup"><span data-stu-id="1359c-501">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="1359c-502">Per altre informazioni, vedere [Panoramica degli account del servizio gestito del gruppo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="1359c-502">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="1359c-503">Diritti Accesso come servizio</span><span class="sxs-lookup"><span data-stu-id="1359c-503">Log on as a service rights</span></span>

<span data-ttu-id="1359c-504">Per stabilire i diritti *Accesso come servizio* per un account utente di servizio:</span><span class="sxs-lookup"><span data-stu-id="1359c-504">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="1359c-505">Aprire l'editor Criteri di sicurezza locali eseguendo *secpol.msc* .</span><span class="sxs-lookup"><span data-stu-id="1359c-505">Open the Local Security Policy editor by running *secpol.msc* .</span></span>
1. <span data-ttu-id="1359c-506">Espandere il nodo **Criteri locali** e selezionare **Assegnazione diritti utente** .</span><span class="sxs-lookup"><span data-stu-id="1359c-506">Expand the **Local Policies** node and select **User Rights Assignment** .</span></span>
1. <span data-ttu-id="1359c-507">Aprire il criterio **Accesso come servizio** .</span><span class="sxs-lookup"><span data-stu-id="1359c-507">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="1359c-508">Selezionare **Aggiungi utente o gruppo** .</span><span class="sxs-lookup"><span data-stu-id="1359c-508">Select **Add User or Group** .</span></span>
1. <span data-ttu-id="1359c-509">Specificare il nome oggetto (account utente) in uno dei modi seguenti:</span><span class="sxs-lookup"><span data-stu-id="1359c-509">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="1359c-510">Digitare l'account utente (`{DOMAIN OR COMPUTER NAME\USER}`) nel campo del nome oggetto e scegliere **OK** per aggiungere l'utente al criterio.</span><span class="sxs-lookup"><span data-stu-id="1359c-510">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="1359c-511">Fare clic su **Advanced** (Avanzate).</span><span class="sxs-lookup"><span data-stu-id="1359c-511">Select **Advanced** .</span></span> <span data-ttu-id="1359c-512">Selezionare **Trova** .</span><span class="sxs-lookup"><span data-stu-id="1359c-512">Select **Find Now** .</span></span> <span data-ttu-id="1359c-513">Selezionare l'account utente dall'elenco.</span><span class="sxs-lookup"><span data-stu-id="1359c-513">Select the user account from the list.</span></span> <span data-ttu-id="1359c-514">Selezionare **OK** .</span><span class="sxs-lookup"><span data-stu-id="1359c-514">Select **OK** .</span></span> <span data-ttu-id="1359c-515">Scegliere di nuovo **OK** per aggiungere l'utente al criterio.</span><span class="sxs-lookup"><span data-stu-id="1359c-515">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="1359c-516">Scegliere **OK** o **Applica** per accettare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="1359c-516">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="1359c-517">Creare e gestire il servizio di Windows</span><span class="sxs-lookup"><span data-stu-id="1359c-517">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="1359c-518">Creare un servizio</span><span class="sxs-lookup"><span data-stu-id="1359c-518">Create a service</span></span>

<span data-ttu-id="1359c-519">Usare i comandi di PowerShell per registrare un servizio.</span><span class="sxs-lookup"><span data-stu-id="1359c-519">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="1359c-520">Da una shell dei comandi di PowerShell 6 amministrativa eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="1359c-520">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="1359c-521">`{EXE PATH}`: Percorso della cartella dell'app nell'host (ad esempio, `d:\myservice` ).</span><span class="sxs-lookup"><span data-stu-id="1359c-521">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="1359c-522">Non includere l'eseguibile dell'app nel percorso.</span><span class="sxs-lookup"><span data-stu-id="1359c-522">Don't include the app's executable in the path.</span></span> <span data-ttu-id="1359c-523">Non è necessario aggiungere una barra finale.</span><span class="sxs-lookup"><span data-stu-id="1359c-523">A trailing slash isn't required.</span></span>
* <span data-ttu-id="1359c-524">`{DOMAIN OR COMPUTER NAME\USER}`: Account utente del servizio (ad esempio, `Contoso\ServiceUser` ).</span><span class="sxs-lookup"><span data-stu-id="1359c-524">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="1359c-525">`{SERVICE NAME}`: Nome del servizio (ad esempio, `MyService` ).</span><span class="sxs-lookup"><span data-stu-id="1359c-525">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="1359c-526">`{EXE FILE PATH}`: Percorso eseguibile dell'app (ad esempio, `d:\myservice\myservice.exe` ).</span><span class="sxs-lookup"><span data-stu-id="1359c-526">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="1359c-527">Includere il nome del file eseguibile con l'estensione.</span><span class="sxs-lookup"><span data-stu-id="1359c-527">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="1359c-528">`{DESCRIPTION}`: Descrizione del servizio (ad esempio, `My sample service` ).</span><span class="sxs-lookup"><span data-stu-id="1359c-528">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="1359c-529">`{DISPLAY NAME}`: Nome visualizzato del servizio (ad esempio, `My Service` ).</span><span class="sxs-lookup"><span data-stu-id="1359c-529">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="1359c-530">Avviare un servizio</span><span class="sxs-lookup"><span data-stu-id="1359c-530">Start a service</span></span>

<span data-ttu-id="1359c-531">Per avviare un servizio, usare il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1359c-531">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="1359c-532">L'avvio del servizio richiede alcuni secondi.</span><span class="sxs-lookup"><span data-stu-id="1359c-532">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="1359c-533">Determinare lo stato di un servizio</span><span class="sxs-lookup"><span data-stu-id="1359c-533">Determine a service's status</span></span>

<span data-ttu-id="1359c-534">Per verificare lo stato di un servizio, usare il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1359c-534">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="1359c-535">Lo stato viene indicato con uno dei valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="1359c-535">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="1359c-536">Arrestare un servizio</span><span class="sxs-lookup"><span data-stu-id="1359c-536">Stop a service</span></span>

<span data-ttu-id="1359c-537">Per arrestare un servizio, usare il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1359c-537">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="1359c-538">Rimuovere un servizio</span><span class="sxs-lookup"><span data-stu-id="1359c-538">Remove a service</span></span>

<span data-ttu-id="1359c-539">Dopo il breve lasso di tempo necessario per arrestare un servizio, rimuovere il servizio con il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1359c-539">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="1359c-540">Gestire gli eventi di avvio e arresto</span><span class="sxs-lookup"><span data-stu-id="1359c-540">Handle starting and stopping events</span></span>

<span data-ttu-id="1359c-541">Per gestire gli eventi <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:</span><span class="sxs-lookup"><span data-stu-id="1359c-541">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="1359c-542">Creare una classe che deriva da <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> con i metodi `OnStarting`, `OnStarted` e `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="1359c-542">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="1359c-543">Creare un metodo di estensione per <xref:Microsoft.AspNetCore.Hosting.IWebHost> che passa `CustomWebHostService` a <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="1359c-543">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="1359c-544">In `Program.Main` chiamare il metodo di estensione `RunAsCustomService` invece di <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="1359c-544">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="1359c-545">Per visualizzare il percorso di <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, fare riferimento all'esempio di codice illustrato nella sezione [Tipo di distribuzione](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="1359c-545">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="1359c-546">Scenari con server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="1359c-546">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="1359c-547">I servizi che interagiscono con le richieste da Internet o da una rete aziendale e si trovano dietro un proxy o un servizio di bilanciamento del carico potrebbero richiedere una configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="1359c-547">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="1359c-548">Per altre informazioni, vedere <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="1359c-548">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="1359c-549">Configurare gli endpoint</span><span class="sxs-lookup"><span data-stu-id="1359c-549">Configure endpoints</span></span>

<span data-ttu-id="1359c-550">Per impostazione predefinita, ASP.NET Core è associato a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="1359c-550">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="1359c-551">Configurare l'URL e la porta impostando la `ASPNETCORE_URLS` variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="1359c-551">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="1359c-552">Per ulteriori approcci alla configurazione di porte e URL, vedere l'articolo relativo al server pertinente:</span><span class="sxs-lookup"><span data-stu-id="1359c-552">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="1359c-553">Le linee guida precedenti riguardano il supporto per gli endpoint HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1359c-553">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="1359c-554">Ad esempio, configurare l'app per HTTPS quando si usa l'autenticazione con un servizio Windows.</span><span class="sxs-lookup"><span data-stu-id="1359c-554">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="1359c-555">L'uso del certificato di sviluppo ASP.NET Core HTTPS per proteggere un endpoint del servizio non è supportato.</span><span class="sxs-lookup"><span data-stu-id="1359c-555">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="1359c-556">Directory corrente e radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="1359c-556">Current directory and content root</span></span>

<span data-ttu-id="1359c-557">La directory di lavoro corrente restituita chiamando <xref:System.IO.Directory.GetCurrentDirectory*> per un servizio Windows è la cartella *C:\\WINDOWS\\system32* .</span><span class="sxs-lookup"><span data-stu-id="1359c-557">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="1359c-558">La cartella *system32* non è un percorso appropriato per archiviare i file di un servizio, ad esempio i file di impostazioni.</span><span class="sxs-lookup"><span data-stu-id="1359c-558">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="1359c-559">Usare uno degli approcci seguenti per gestire e accedere agli asset e ai file di impostazioni di un servizio.</span><span class="sxs-lookup"><span data-stu-id="1359c-559">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="1359c-560">Impostare il percorso radice del contenuto sulla cartella dell'app</span><span class="sxs-lookup"><span data-stu-id="1359c-560">Set the content root path to the app's folder</span></span>

<span data-ttu-id="1359c-561"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> è lo stesso percorso fornito all'argomento `binPath` durante la creazione di un servizio.</span><span class="sxs-lookup"><span data-stu-id="1359c-561">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="1359c-562">Anziché chiamare `GetCurrentDirectory` per creare percorsi per i file di impostazioni, chiamare <xref:System.IO.Directory.SetCurrentDirectory*> con il percorso della radice del [contenuto](xref:fundamentals/index#content-root)dell'app.</span><span class="sxs-lookup"><span data-stu-id="1359c-562">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="1359c-563">In `Program.Main`, determinare il percorso della cartella dell'eseguibile del servizio e usare il percorso per stabilire la radice del contenuto dell'app:</span><span class="sxs-lookup"><span data-stu-id="1359c-563">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="1359c-564">Archiviare i file di un servizio in un percorso appropriato nel disco</span><span class="sxs-lookup"><span data-stu-id="1359c-564">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="1359c-565">Specificare un percorso assoluto con <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando si usa un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> per la cartella contenente i file.</span><span class="sxs-lookup"><span data-stu-id="1359c-565">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="1359c-566">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="1359c-566">Troubleshoot</span></span>

<span data-ttu-id="1359c-567">Per risolvere i problemi relativi a un'app di servizio Windows, vedere <xref:test/troubleshoot> .</span><span class="sxs-lookup"><span data-stu-id="1359c-567">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="1359c-568">Errori comuni</span><span class="sxs-lookup"><span data-stu-id="1359c-568">Common errors</span></span>

* <span data-ttu-id="1359c-569">È in uso una versione precedente o provvisoria di PowerShell.</span><span class="sxs-lookup"><span data-stu-id="1359c-569">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="1359c-570">Il servizio registrato non usa l'output **pubblicato** dell'app dal comando [DotNet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="1359c-570">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="1359c-571">L'output del comando [DotNet Build](/dotnet/core/tools/dotnet-build) non è supportato per la distribuzione di app.</span><span class="sxs-lookup"><span data-stu-id="1359c-571">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="1359c-572">Gli asset pubblicati si trovano in una delle cartelle seguenti, a seconda del tipo di distribuzione:</span><span class="sxs-lookup"><span data-stu-id="1359c-572">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="1359c-573">*bin/Release/{Target Framework}/Publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="1359c-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="1359c-574">*bin/Release/{Target Framework}/{Runtime Identifier}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="1359c-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="1359c-575">Il servizio non è nello stato in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="1359c-575">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="1359c-576">I percorsi delle risorse utilizzate dall'app, ad esempio i certificati, non sono corretti.</span><span class="sxs-lookup"><span data-stu-id="1359c-576">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="1359c-577">Il percorso di base di un servizio Windows è *c: \\ Windows \\ system32* .</span><span class="sxs-lookup"><span data-stu-id="1359c-577">The base path of a Windows Service is *c:\\Windows\\System32* .</span></span>
* <span data-ttu-id="1359c-578">L'utente non dispone *di diritti di accesso come servizio* .</span><span class="sxs-lookup"><span data-stu-id="1359c-578">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="1359c-579">La password dell'utente è scaduta o non è stata passata correttamente durante l'esecuzione del `New-Service` comando di PowerShell.</span><span class="sxs-lookup"><span data-stu-id="1359c-579">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="1359c-580">L'app richiede l'autenticazione ASP.NET Core ma non è configurata per le connessioni protette (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="1359c-580">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="1359c-581">La porta dell'URL della richiesta non è corretta o non è configurata correttamente nell'app.</span><span class="sxs-lookup"><span data-stu-id="1359c-581">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="1359c-582">Log eventi di sistema e dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="1359c-582">System and Application Event Logs</span></span>

<span data-ttu-id="1359c-583">Accedere ai registri eventi di sistema e dell'applicazione:</span><span class="sxs-lookup"><span data-stu-id="1359c-583">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="1359c-584">Aprire il menu Start, cercare *Visualizzatore eventi* e selezionare l'app **Visualizzatore eventi** .</span><span class="sxs-lookup"><span data-stu-id="1359c-584">Open the Start menu, search for *Event Viewer* , and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="1359c-585">In **Visualizzatore eventi** aprire il nodo **Registri di Windows** .</span><span class="sxs-lookup"><span data-stu-id="1359c-585">In **Event Viewer** , open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="1359c-586">Selezionare **sistema** per aprire il registro eventi di sistema.</span><span class="sxs-lookup"><span data-stu-id="1359c-586">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="1359c-587">Selezionare **Applicazione** per aprire il log eventi dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="1359c-587">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="1359c-588">Cercare gli errori associati all'app in cui si è verificato il problema.</span><span class="sxs-lookup"><span data-stu-id="1359c-588">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="1359c-589">Eseguire l'app da un prompt dei comandi</span><span class="sxs-lookup"><span data-stu-id="1359c-589">Run the app at a command prompt</span></span>

<span data-ttu-id="1359c-590">Molti errori di avvio non producono informazioni utili nei log eventi.</span><span class="sxs-lookup"><span data-stu-id="1359c-590">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="1359c-591">È possibile individuare la causa di alcuni errori eseguendo l'app da un prompt dei comandi nel sistema host.</span><span class="sxs-lookup"><span data-stu-id="1359c-591">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="1359c-592">Per registrare dettagli aggiuntivi dall'app, abbassare il [livello di registrazione](xref:fundamentals/logging/index#log-level) o eseguire l'app nell' [ambiente di sviluppo](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="1359c-592">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="1359c-593">Cancella cache di pacchetti</span><span class="sxs-lookup"><span data-stu-id="1359c-593">Clear package caches</span></span>

<span data-ttu-id="1359c-594">Un'app funzionante potrebbe non riuscire immediatamente dopo l'aggiornamento del .NET Core SDK nel computer di sviluppo o la modifica delle versioni del pacchetto all'interno dell'app.</span><span class="sxs-lookup"><span data-stu-id="1359c-594">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="1359c-595">In alcuni casi i pacchetti incoerenti possono interrompere un'app quando si eseguono aggiornamenti principali.</span><span class="sxs-lookup"><span data-stu-id="1359c-595">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="1359c-596">La maggior parte di questi problemi può essere risolta attenendosi alle istruzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="1359c-596">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="1359c-597">Eliminare le cartelle *bin* e *obj* .</span><span class="sxs-lookup"><span data-stu-id="1359c-597">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="1359c-598">Cancellare le cache dei pacchetti eseguendo [le impostazioni locali di DotNet NuGet All--Clear](/dotnet/core/tools/dotnet-nuget-locals) da una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="1359c-598">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="1359c-599">La cancellazione delle cache dei pacchetti può essere eseguita anche con lo strumento [nuget.exe](https://www.nuget.org/downloads) e l'esecuzione del comando `nuget locals all -clear` .</span><span class="sxs-lookup"><span data-stu-id="1359c-599">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="1359c-600">*nuget.exe* non è un'installazione inclusa con il sistema operativo desktop Windows e deve essere ottenuta separatamente dal [sito Web NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="1359c-600">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="1359c-601">Ripristinare e ricompilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="1359c-601">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="1359c-602">Eliminare tutti i file nella cartella di distribuzione nel server prima di ridistribuire l'app.</span><span class="sxs-lookup"><span data-stu-id="1359c-602">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="1359c-603">App lenta o bloccata</span><span class="sxs-lookup"><span data-stu-id="1359c-603">Slow or hanging app</span></span>

<span data-ttu-id="1359c-604">Un *dump di arresto anomalo* del sistema è uno snapshot della memoria del sistema e può contribuire a determinare la provocazione di un arresto anomalo dell'app, dell'avvio o dell'applicazione lenta.</span><span class="sxs-lookup"><span data-stu-id="1359c-604">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="1359c-605">Arresto anomalo o eccezione di un'app</span><span class="sxs-lookup"><span data-stu-id="1359c-605">App crashes or encounters an exception</span></span>

<span data-ttu-id="1359c-606">Ottenere e analizzare un dump da [Segnalazione errori Windows](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="1359c-606">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="1359c-607">Creare una cartella per i file dump di arresto anomalo del sistema in `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="1359c-607">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="1359c-608">Eseguire lo [script di PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) con il nome dell'eseguibile dell'applicazione:</span><span class="sxs-lookup"><span data-stu-id="1359c-608">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="1359c-609">Eseguire l'app nelle condizioni che causano l'arresto anomalo.</span><span class="sxs-lookup"><span data-stu-id="1359c-609">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="1359c-610">Dopo che si è verificato l'arresto anomalo, eseguire lo [script di PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="1359c-610">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="1359c-611">Dopo l'arresto anomalo di un'app e la raccolta dei dump, l'app può terminare normalmente.</span><span class="sxs-lookup"><span data-stu-id="1359c-611">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="1359c-612">Lo script di PowerShell configura Segnalazione errori Windows per raccogliere fino a cinque dump per ogni app.</span><span class="sxs-lookup"><span data-stu-id="1359c-612">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="1359c-613">I dump di arresto anomalo del sistema potrebbero richiedere una grande quantità di spazio su disco (fino a diversi gigabyte ognuno).</span><span class="sxs-lookup"><span data-stu-id="1359c-613">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="1359c-614">L'app si blocca, si verifica un errore durante l'avvio o viene eseguita normalmente</span><span class="sxs-lookup"><span data-stu-id="1359c-614">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="1359c-615">Quando un'app si *blocca* (smette di rispondere ma non si arresta in modo anomalo), si verifica un errore durante l'avvio o viene eseguita normalmente, vedere [file di dump in modalità utente: scegliere lo strumento migliore](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) per selezionare uno strumento appropriato per produrre il dump.</span><span class="sxs-lookup"><span data-stu-id="1359c-615">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="1359c-616">Analizzare il dump</span><span class="sxs-lookup"><span data-stu-id="1359c-616">Analyze the dump</span></span>

<span data-ttu-id="1359c-617">È possibile analizzare un dump usando diversi approcci.</span><span class="sxs-lookup"><span data-stu-id="1359c-617">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="1359c-618">Per altre informazioni, vedere [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analisi di un file dump in modalità utente).</span><span class="sxs-lookup"><span data-stu-id="1359c-618">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1359c-619">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="1359c-619">Additional resources</span></span>

* <span data-ttu-id="1359c-620">[Configurazione dell'endpoint Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (include la configurazione HTTPS e il supporto SNI)</span><span class="sxs-lookup"><span data-stu-id="1359c-620">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
