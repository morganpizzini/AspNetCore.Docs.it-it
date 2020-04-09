---
title: Ospitare ASP.NET Core in un servizio Windows
author: rick-anderson
description: Informazioni su come ospitare un'app ASP.NET Core in un servizio Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/windows-service
ms.openlocfilehash: 5cb61d330df7e15fbd54396207792596ae018fd3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417582"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="1226b-103">Ospitare ASP.NET Core in un servizio Windows</span><span class="sxs-lookup"><span data-stu-id="1226b-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1226b-104">È possibile ospitare un'app ASP.NET Core in Windows come [servizio Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) senza usare IIS.</span><span class="sxs-lookup"><span data-stu-id="1226b-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="1226b-105">Quando è ospitata come servizio di Windows, l'app viene avviata automaticamente dopo il riavvio del server.</span><span class="sxs-lookup"><span data-stu-id="1226b-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="1226b-106">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1226b-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1226b-107">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="1226b-107">Prerequisites</span></span>

* [<span data-ttu-id="1226b-108">ASP.NET Core SDK 2.1 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="1226b-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="1226b-109">PowerShell 6.2 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="1226b-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="1226b-110">Modello di servizio di ruolo di lavoro</span><span class="sxs-lookup"><span data-stu-id="1226b-110">Worker Service template</span></span>

<span data-ttu-id="1226b-111">Il modello di servizio di ruolo di lavoro di ASP.NET Core rappresenta un punto di partenza per la scrittura di app di servizi a esecuzione prolungata.</span><span class="sxs-lookup"><span data-stu-id="1226b-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="1226b-112">Per usare il modello come base per un'app di servizio Windows:</span><span class="sxs-lookup"><span data-stu-id="1226b-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="1226b-113">Creare un'app di servizio di ruolo di lavoro dal modello .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1226b-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="1226b-114">Seguire le indicazioni nella sezione [Configurazione dell'app](#app-configuration) per aggiornare l'app di servizio di ruolo di lavoro affinché venga eseguita come servizio Windows.</span><span class="sxs-lookup"><span data-stu-id="1226b-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="1226b-115">Configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="1226b-115">App configuration</span></span>

<span data-ttu-id="1226b-116">L'app richiede un riferimento al pacchetto per [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="1226b-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="1226b-117">`IHostBuilder.UseWindowsService`viene chiamato durante la creazione dell'host.</span><span class="sxs-lookup"><span data-stu-id="1226b-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="1226b-118">Se l'app è in esecuzione come servizio di Windows, il metodo:</span><span class="sxs-lookup"><span data-stu-id="1226b-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="1226b-119">Imposta la durata dell'host su `WindowsServiceLifetime`.</span><span class="sxs-lookup"><span data-stu-id="1226b-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="1226b-120">Imposta la radice del [contenuto su](xref:fundamentals/index#content-root) [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="1226b-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="1226b-121">Per altre informazioni, vedere la sezione [Directory corrente e radice del contenuto](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="1226b-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="1226b-122">Abilita la registrazione nel registro eventi:</span><span class="sxs-lookup"><span data-stu-id="1226b-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="1226b-123">Il nome dell'applicazione viene utilizzato come nome di origine predefinito.</span><span class="sxs-lookup"><span data-stu-id="1226b-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="1226b-124">Il livello di registrazione predefinito è *Avviso* o superiore per `CreateDefaultBuilder` un'app basata su un modello di ASP.NET Core che chiama per compilare l'host.</span><span class="sxs-lookup"><span data-stu-id="1226b-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="1226b-125">Eseguire l'override del `Logging:EventLog:LogLevel:Default` livello di log predefinito con la chiave in *appsettings.json*/*appsettings. Environment: .json* o un altro provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="1226b-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="1226b-126">Solo gli amministratori possono creare nuove origini eventi.</span><span class="sxs-lookup"><span data-stu-id="1226b-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="1226b-127">Quando non è possibile creare un'origine evento usando il nome dell'applicazione, viene registrato un avviso nell'origine *Applicazione* e i log eventi vengono disabilitati.</span><span class="sxs-lookup"><span data-stu-id="1226b-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="1226b-128">In `CreateHostBuilder` di *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1226b-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="1226b-129">Le app di esempio seguenti accompagnano questo argomento:The following sample apps accompany this topic:</span><span class="sxs-lookup"><span data-stu-id="1226b-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="1226b-130">Esempio &ndash; di servizio worker in background Esempio di app non Web basato sul [modello](#worker-service-template) Servizio di lavoro che usa i [servizi ospitati](xref:fundamentals/host/hosted-services) per le attività in background.</span><span class="sxs-lookup"><span data-stu-id="1226b-130">Background Worker Service Sample &ndash; A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="1226b-131">Esempio di &ndash; servizio app Web Un esempio di app Web Razor Pages che viene eseguito come servizio Windows con [servizi ospitati](xref:fundamentals/host/hosted-services) per le attività in background.</span><span class="sxs-lookup"><span data-stu-id="1226b-131">Web App Service Sample &ndash; A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="1226b-132">Per indicazioni su MVC, <xref:mvc/overview> <xref:migration/22-to-30>vedere gli articoli in e .</span><span class="sxs-lookup"><span data-stu-id="1226b-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="1226b-133">Tipo di distribuzione</span><span class="sxs-lookup"><span data-stu-id="1226b-133">Deployment type</span></span>

<span data-ttu-id="1226b-134">Per informazioni e consigli sugli scenari di distribuzione, vedere [Distribuzione di applicazioni .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="1226b-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="1226b-135">SDK</span><span class="sxs-lookup"><span data-stu-id="1226b-135">SDK</span></span>

<span data-ttu-id="1226b-136">Per un servizio basato su app Web che usa le pagine Razor o framework MVC, specificare il Web SDK nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="1226b-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="1226b-137">Se il servizio esegue solo attività in background (ad esempio, [servizi ospitati](xref:fundamentals/host/hosted-services)), specificare l'SDK di lavoro nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="1226b-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="1226b-138">Distribuzione dipendente dal framework</span><span class="sxs-lookup"><span data-stu-id="1226b-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="1226b-139">La distribuzione dipendente dal framework si basa sulla presenza di una versione condivisa a livello di sistema di .NET Core nel sistema di destinazione.</span><span class="sxs-lookup"><span data-stu-id="1226b-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="1226b-140">Quando lo scenario di distribuzione dipendente dal framework viene implementato in base alle indicazioni di questo articolo, l'SDK genera un file eseguibile (con estensione *exe*) detto *eseguibile dipendente dal framework*.</span><span class="sxs-lookup"><span data-stu-id="1226b-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="1226b-141">Se si usa [Web SDK](#sdk), un file *web.config,* che normalmente viene prodotto durante la pubblicazione di un'app ASP.NET Core, non è necessario per un'app di servizi Windows.</span><span class="sxs-lookup"><span data-stu-id="1226b-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="1226b-142">Per disabilitare la creazione del file *web.config*, aggiungere la proprietà `<IsTransformWebConfigDisabled>` impostata su `true`.</span><span class="sxs-lookup"><span data-stu-id="1226b-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="1226b-143">Distribuzione autonoma</span><span class="sxs-lookup"><span data-stu-id="1226b-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="1226b-144">Una distribuzione autonoma non si basa sulla presenza di un framework condiviso nel sistema host.</span><span class="sxs-lookup"><span data-stu-id="1226b-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="1226b-145">Il runtime e le dipendenze dell'app vengono distribuiti con l'app.</span><span class="sxs-lookup"><span data-stu-id="1226b-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="1226b-146">Un [identificatore di runtime (RID)](/dotnet/core/rid-catalog) di Windows viene incluso nel `<PropertyGroup>` che contiene il framework di destinazione:</span><span class="sxs-lookup"><span data-stu-id="1226b-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="1226b-147">Per eseguire la pubblicazione per più identificatori di runtime:</span><span class="sxs-lookup"><span data-stu-id="1226b-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="1226b-148">Specificare gli identificatori di runtime in un elenco delimitato da punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="1226b-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="1226b-149">Utilizzare il [ \<](/dotnet/core/tools/csproj#runtimeidentifiers) nome della proprietà RuntimeIdentifiers>(plurale).</span><span class="sxs-lookup"><span data-stu-id="1226b-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="1226b-150">Per altre informazioni, vedere il [Catalogo RID di .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="1226b-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="1226b-151">Account utente del servizio</span><span class="sxs-lookup"><span data-stu-id="1226b-151">Service user account</span></span>

<span data-ttu-id="1226b-152">Per creare un account utente per un servizio, usare il cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) da una shell dei comandi di PowerShell 6 amministrativa.</span><span class="sxs-lookup"><span data-stu-id="1226b-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="1226b-153">In Aggiornamento di Windows 10 (ottobre 2018) (versione 1809/build 10.0.17763) o versioni successive:</span><span class="sxs-lookup"><span data-stu-id="1226b-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="1226b-154">In un sistema operativo Windows precedente ad Aggiornamento di Windows 10 (ottobre 2018) (versione 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="1226b-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="1226b-155">Fornire una [password complessa](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando richiesto.</span><span class="sxs-lookup"><span data-stu-id="1226b-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="1226b-156">Se non viene specificato il parametro `-AccountExpires` per il cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) con un valore <xref:System.DateTime> di scadenza, l'account non scade.</span><span class="sxs-lookup"><span data-stu-id="1226b-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="1226b-157">Per altre informazioni, vedere [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Service User Accounts](/windows/desktop/services/service-user-accounts) (Account utente di servizio).</span><span class="sxs-lookup"><span data-stu-id="1226b-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="1226b-158">Un approccio alternativo per la gestione degli utenti quando si usa Active Directory consiste nell'usare account del servizio gestito.</span><span class="sxs-lookup"><span data-stu-id="1226b-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="1226b-159">Per altre informazioni, vedere [Panoramica degli account del servizio gestito del gruppo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="1226b-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="1226b-160">Diritti Accesso come servizio</span><span class="sxs-lookup"><span data-stu-id="1226b-160">Log on as a service rights</span></span>

<span data-ttu-id="1226b-161">Per stabilire i diritti *Accesso come servizio* per un account utente di servizio:</span><span class="sxs-lookup"><span data-stu-id="1226b-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="1226b-162">Aprire l'editor Criteri di sicurezza locali eseguendo *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="1226b-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="1226b-163">Espandere il nodo **Criteri locali** e selezionare **Assegnazione diritti utente**.</span><span class="sxs-lookup"><span data-stu-id="1226b-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="1226b-164">Aprire il criterio **Accesso come servizio**.</span><span class="sxs-lookup"><span data-stu-id="1226b-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="1226b-165">Selezionare **Aggiungi utente o gruppo**.</span><span class="sxs-lookup"><span data-stu-id="1226b-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="1226b-166">Specificare il nome oggetto (account utente) in uno dei modi seguenti:</span><span class="sxs-lookup"><span data-stu-id="1226b-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="1226b-167">Digitare l'account utente (`{DOMAIN OR COMPUTER NAME\USER}`) nel campo del nome oggetto e scegliere **OK** per aggiungere l'utente al criterio.</span><span class="sxs-lookup"><span data-stu-id="1226b-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="1226b-168">Fare clic su **Advanced** (Avanzate).</span><span class="sxs-lookup"><span data-stu-id="1226b-168">Select **Advanced**.</span></span> <span data-ttu-id="1226b-169">Selezionare **Trova**.</span><span class="sxs-lookup"><span data-stu-id="1226b-169">Select **Find Now**.</span></span> <span data-ttu-id="1226b-170">Selezionare l'account utente dall'elenco.</span><span class="sxs-lookup"><span data-stu-id="1226b-170">Select the user account from the list.</span></span> <span data-ttu-id="1226b-171">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="1226b-171">Select **OK**.</span></span> <span data-ttu-id="1226b-172">Scegliere di nuovo **OK** per aggiungere l'utente al criterio.</span><span class="sxs-lookup"><span data-stu-id="1226b-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="1226b-173">Scegliere **OK** o **Applica** per accettare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="1226b-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="1226b-174">Creare e gestire il servizio di Windows</span><span class="sxs-lookup"><span data-stu-id="1226b-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="1226b-175">Creare un servizio</span><span class="sxs-lookup"><span data-stu-id="1226b-175">Create a service</span></span>

<span data-ttu-id="1226b-176">Usare i comandi di PowerShell per registrare un servizio.</span><span class="sxs-lookup"><span data-stu-id="1226b-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="1226b-177">Da una shell dei comandi di PowerShell 6 amministrativa eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="1226b-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="1226b-178">`{EXE PATH}`&ndash; Percorso della cartella dell'app nell'host, `d:\myservice`ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="1226b-178">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="1226b-179">Non includere l'eseguibile dell'app nel percorso.</span><span class="sxs-lookup"><span data-stu-id="1226b-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="1226b-180">Non è necessario aggiungere una barra finale.</span><span class="sxs-lookup"><span data-stu-id="1226b-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="1226b-181">`{DOMAIN OR COMPUTER NAME\USER}`&ndash; Account utente del servizio `Contoso\ServiceUser`(ad esempio, ).</span><span class="sxs-lookup"><span data-stu-id="1226b-181">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="1226b-182">`{SERVICE NAME}`&ndash; Nome del servizio `MyService`(ad esempio, ).</span><span class="sxs-lookup"><span data-stu-id="1226b-182">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="1226b-183">`{EXE FILE PATH}`&ndash; Percorso eseguibile dell'app, `d:\myservice\myservice.exe`ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="1226b-183">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="1226b-184">Includere il nome del file eseguibile con l'estensione.</span><span class="sxs-lookup"><span data-stu-id="1226b-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="1226b-185">`{DESCRIPTION}`&ndash; Descrizione del servizio `My sample service`(ad esempio, ).</span><span class="sxs-lookup"><span data-stu-id="1226b-185">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="1226b-186">`{DISPLAY NAME}`&ndash; Nome visualizzato del servizio `My Service`(ad esempio, ).</span><span class="sxs-lookup"><span data-stu-id="1226b-186">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="1226b-187">Avviare un servizio</span><span class="sxs-lookup"><span data-stu-id="1226b-187">Start a service</span></span>

<span data-ttu-id="1226b-188">Per avviare un servizio, usare il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1226b-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="1226b-189">L'avvio del servizio richiede alcuni secondi.</span><span class="sxs-lookup"><span data-stu-id="1226b-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="1226b-190">Determinare lo stato di un servizio</span><span class="sxs-lookup"><span data-stu-id="1226b-190">Determine a service's status</span></span>

<span data-ttu-id="1226b-191">Per verificare lo stato di un servizio, usare il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1226b-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="1226b-192">Lo stato viene indicato con uno dei valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="1226b-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="1226b-193">Arrestare un servizio</span><span class="sxs-lookup"><span data-stu-id="1226b-193">Stop a service</span></span>

<span data-ttu-id="1226b-194">Per arrestare un servizio, usare il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1226b-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="1226b-195">Rimuovere un servizio</span><span class="sxs-lookup"><span data-stu-id="1226b-195">Remove a service</span></span>

<span data-ttu-id="1226b-196">Dopo il breve lasso di tempo necessario per arrestare un servizio, rimuovere il servizio con il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1226b-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="1226b-197">Scenari con server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="1226b-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="1226b-198">I servizi che interagiscono con le richieste da Internet o da una rete aziendale e si trovano dietro un proxy o un servizio di bilanciamento del carico potrebbero richiedere una configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="1226b-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="1226b-199">Per altre informazioni, vedere <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="1226b-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="1226b-200">Configurare gli endpoint</span><span class="sxs-lookup"><span data-stu-id="1226b-200">Configure endpoints</span></span>

<span data-ttu-id="1226b-201">Per impostazione predefinita, ASP.NET Core è associato a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="1226b-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="1226b-202">Configurare l'URL e `ASPNETCORE_URLS` la porta impostando la variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="1226b-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="1226b-203">Per ulteriori approcci di configurazione di URL e porte, vedere l'articolo relativo al server:</span><span class="sxs-lookup"><span data-stu-id="1226b-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="1226b-204">Le indicazioni precedenti riguardano il supporto per gli endpoint HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1226b-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="1226b-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span><span class="sxs-lookup"><span data-stu-id="1226b-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="1226b-206">L'uso del certificato di sviluppo ASP.NET Core HTTPS per proteggere un endpoint del servizio non è supportato.</span><span class="sxs-lookup"><span data-stu-id="1226b-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="1226b-207">Directory corrente e radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="1226b-207">Current directory and content root</span></span>

<span data-ttu-id="1226b-208">La directory di lavoro corrente restituita chiamando <xref:System.IO.Directory.GetCurrentDirectory*> per un servizio Windows è la cartella *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="1226b-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="1226b-209">La cartella *system32* non è un percorso appropriato per archiviare i file di un servizio, ad esempio i file di impostazioni.</span><span class="sxs-lookup"><span data-stu-id="1226b-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="1226b-210">Usare uno degli approcci seguenti per gestire e accedere agli asset e ai file di impostazioni di un servizio.</span><span class="sxs-lookup"><span data-stu-id="1226b-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="1226b-211">Usare ContentRootPath o ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="1226b-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="1226b-212">Usare [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) o <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> per individuare le risorse di un'app.</span><span class="sxs-lookup"><span data-stu-id="1226b-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="1226b-213">Quando l'app viene <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> eseguita <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> come servizio, imposta l'oggetto su [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="1226b-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="1226b-214">File di impostazioni predefinite dell'app, *appsettings.json* e *appsettings. Environment, .json*, vengono caricati dalla radice del contenuto dell'app chiamando [CreateDefaultBuilder durante](xref:fundamentals/host/generic-host#set-up-a-host)la costruzione dell'host.</span><span class="sxs-lookup"><span data-stu-id="1226b-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="1226b-215">Per altri file di impostazioni <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>caricati dal codice <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>dello sviluppatore in , non è necessario chiamare .</span><span class="sxs-lookup"><span data-stu-id="1226b-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="1226b-216">Nell'esempio seguente il file custom_settings.json è presente nella radice del contenuto dell'app e viene caricato senza impostare in modo esplicito un percorso di base:In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span><span class="sxs-lookup"><span data-stu-id="1226b-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="1226b-217">Non tentare di <xref:System.IO.Directory.GetCurrentDirectory*> usare per ottenere un percorso di risorsa perché un'app del servizio Windows restituisce la cartella *system32 di\\C: WINDOWS\\* come directory corrente.</span><span class="sxs-lookup"><span data-stu-id="1226b-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="1226b-218">Archiviare i file di un servizio in un percorso appropriato nel disco</span><span class="sxs-lookup"><span data-stu-id="1226b-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="1226b-219">Specificare un percorso assoluto con <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando si usa un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> per la cartella contenente i file.</span><span class="sxs-lookup"><span data-stu-id="1226b-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="1226b-220">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="1226b-220">Troubleshoot</span></span>

<span data-ttu-id="1226b-221">Per risolvere i problemi <xref:test/troubleshoot>relativi a un'app del servizio Windows, vedere .</span><span class="sxs-lookup"><span data-stu-id="1226b-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="1226b-222">Errori comuni</span><span class="sxs-lookup"><span data-stu-id="1226b-222">Common errors</span></span>

* <span data-ttu-id="1226b-223">È in uso una versione precedente o non definitiva di PowerShell.An old or pre-release version of PowerShell is in use.</span><span class="sxs-lookup"><span data-stu-id="1226b-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="1226b-224">Il servizio registrato non usa l'output **pubblicato** dell'app dal comando [dotnet publish.](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="1226b-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="1226b-225">L'output del comando [dotnet build](/dotnet/core/tools/dotnet-build) non è supportato per la distribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="1226b-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="1226b-226">Le risorse pubblicate si trovano in una delle seguenti cartelle a seconda del tipo di distribuzione:</span><span class="sxs-lookup"><span data-stu-id="1226b-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="1226b-227">*bin/Release/ TARGET FRAMEWORK/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="1226b-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="1226b-228">*bin/Release/*</span><span class="sxs-lookup"><span data-stu-id="1226b-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="1226b-229">Il servizio non è nello stato RUNNING.</span><span class="sxs-lookup"><span data-stu-id="1226b-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="1226b-230">I percorsi delle risorse utilizzate dall'app (ad esempio, i certificati) non sono corretti.</span><span class="sxs-lookup"><span data-stu-id="1226b-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="1226b-231">Il percorso di base di un servizio Windows è *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="1226b-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="1226b-232">L'utente non dispone dei diritti *di accesso come servizio.*</span><span class="sxs-lookup"><span data-stu-id="1226b-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="1226b-233">La password dell'utente è scaduta o `New-Service` passata in modo non corretto durante l'esecuzione del comando PowerShell.The user's password is expired or incorrectly passed when executing the PowerShell command.</span><span class="sxs-lookup"><span data-stu-id="1226b-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="1226b-234">L'app richiede ASP.NET'autenticazione Core, ma non è configurata per le connessioni protette (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="1226b-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="1226b-235">La porta dell'URL della richiesta non è corretta o non è configurata correttamente nell'app.</span><span class="sxs-lookup"><span data-stu-id="1226b-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="1226b-236">Registri eventi di sistema e applicazioni</span><span class="sxs-lookup"><span data-stu-id="1226b-236">System and Application Event Logs</span></span>

<span data-ttu-id="1226b-237">Accedere ai registri eventi di sistema e dell'applicazione:</span><span class="sxs-lookup"><span data-stu-id="1226b-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="1226b-238">Aprire il menu Start, cercare *Visualizzatore eventi*e selezionare l'app **Visualizzatore eventi.**</span><span class="sxs-lookup"><span data-stu-id="1226b-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="1226b-239">In **Visualizzatore eventi** aprire il nodo **Registri di Windows**.</span><span class="sxs-lookup"><span data-stu-id="1226b-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="1226b-240">Selezionare **Sistema** per aprire il registro eventi di sistema.</span><span class="sxs-lookup"><span data-stu-id="1226b-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="1226b-241">Selezionare **Applicazione** per aprire il log eventi dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="1226b-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="1226b-242">Cercare gli errori associati all'app in cui si è verificato il problema.</span><span class="sxs-lookup"><span data-stu-id="1226b-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="1226b-243">Eseguire l'app da un prompt dei comandi</span><span class="sxs-lookup"><span data-stu-id="1226b-243">Run the app at a command prompt</span></span>

<span data-ttu-id="1226b-244">Molti errori di avvio non producono informazioni utili nei registri eventi.</span><span class="sxs-lookup"><span data-stu-id="1226b-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="1226b-245">È possibile individuare la causa di alcuni errori eseguendo l'app da un prompt dei comandi nel sistema host.</span><span class="sxs-lookup"><span data-stu-id="1226b-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="1226b-246">Per registrare ulteriori dettagli dall'app, abbassare il livello di [log](xref:fundamentals/logging/index#log-level) o eseguire l'app nell'ambiente di [sviluppo](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="1226b-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="1226b-247">Cancellare le cache dei pacchetti</span><span class="sxs-lookup"><span data-stu-id="1226b-247">Clear package caches</span></span>

<span data-ttu-id="1226b-248">Un'app funzionante potrebbe non riuscire immediatamente dopo l'aggiornamento di .NET Core SDK nel computer di sviluppo o la modifica delle versioni del pacchetto all'interno dell'app.</span><span class="sxs-lookup"><span data-stu-id="1226b-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="1226b-249">In alcuni casi i pacchetti incoerenti possono interrompere un'app quando si eseguono aggiornamenti principali.</span><span class="sxs-lookup"><span data-stu-id="1226b-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="1226b-250">La maggior parte di questi problemi può essere risolta attenendosi alle istruzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="1226b-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="1226b-251">Eliminare le cartelle *bin* e *obj*.</span><span class="sxs-lookup"><span data-stu-id="1226b-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="1226b-252">Cancellare le cache dei pacchetti eseguendo [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) da una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="1226b-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="1226b-253">La cancellazione delle cache dei pacchetti può essere eseguita anche `nuget locals all -clear`con lo strumento [nuget.exe](https://www.nuget.org/downloads) e l'esecuzione del comando .</span><span class="sxs-lookup"><span data-stu-id="1226b-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="1226b-254">*nuget.exe* non è un'installazione inclusa con il sistema operativo desktop Windows e deve essere ottenuta separatamente dal [sito Web NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="1226b-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="1226b-255">Ripristinare e ricompilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="1226b-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="1226b-256">Eliminare tutti i file nella cartella di distribuzione nel server prima di ridistribuire l'app.</span><span class="sxs-lookup"><span data-stu-id="1226b-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="1226b-257">App lenta o bloccata</span><span class="sxs-lookup"><span data-stu-id="1226b-257">Slow or hanging app</span></span>

<span data-ttu-id="1226b-258">Un dump di *arresto anomalo* del sistema è uno snapshot della memoria del sistema e consente di determinare la causa di un arresto anomalo dell'app, un errore di avvio o un'app lenta.</span><span class="sxs-lookup"><span data-stu-id="1226b-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="1226b-259">Arresto anomalo o eccezione di un'app</span><span class="sxs-lookup"><span data-stu-id="1226b-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="1226b-260">Ottenere e analizzare un dump da [Segnalazione errori Windows](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="1226b-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="1226b-261">Creare una cartella per i file dump di arresto anomalo del sistema in `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="1226b-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="1226b-262">Eseguire lo [script di PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) con il nome dell'eseguibile dell'applicazione:Run the EnableDumps PowerShell script with the application executable name:</span><span class="sxs-lookup"><span data-stu-id="1226b-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="1226b-263">Eseguire l'app nelle condizioni che causano l'arresto anomalo.</span><span class="sxs-lookup"><span data-stu-id="1226b-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="1226b-264">Dopo che si è verificato l'arresto anomalo, eseguire lo [script di PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="1226b-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="1226b-265">Dopo l'arresto anomalo di un'app e la raccolta dei dump, l'app può terminare normalmente.</span><span class="sxs-lookup"><span data-stu-id="1226b-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="1226b-266">Lo script di PowerShell configura Segnalazione errori Windows per raccogliere fino a cinque dump per ogni app.</span><span class="sxs-lookup"><span data-stu-id="1226b-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="1226b-267">I dump di arresto anomalo del sistema potrebbero richiedere una grande quantità di spazio su disco (fino a diversi gigabyte ognuno).</span><span class="sxs-lookup"><span data-stu-id="1226b-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="1226b-268">L'app si blocca, si verifica un errore durante l'avvio o viene eseguita normalmente</span><span class="sxs-lookup"><span data-stu-id="1226b-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="1226b-269">Quando un'app *si blocca* (si blocca ma non si arresta in modo anomalo), non riesce durante l'avvio o viene eseguito normalmente, vedere File di dump in [modalità utente: Scelta dello strumento migliore](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) per selezionare uno strumento appropriato per produrre il dump.</span><span class="sxs-lookup"><span data-stu-id="1226b-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="1226b-270">Analizzare il dump</span><span class="sxs-lookup"><span data-stu-id="1226b-270">Analyze the dump</span></span>

<span data-ttu-id="1226b-271">È possibile analizzare un dump usando diversi approcci.</span><span class="sxs-lookup"><span data-stu-id="1226b-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="1226b-272">Per altre informazioni, vedere [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analisi di un file dump in modalità utente).</span><span class="sxs-lookup"><span data-stu-id="1226b-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1226b-273">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="1226b-273">Additional resources</span></span>

* <span data-ttu-id="1226b-274">[Configurazione dell'endpoint Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (include la configurazione HTTPS e il supporto SNI)</span><span class="sxs-lookup"><span data-stu-id="1226b-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="1226b-275">È possibile ospitare un'app ASP.NET Core in Windows come [servizio Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) senza usare IIS.</span><span class="sxs-lookup"><span data-stu-id="1226b-275">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="1226b-276">Quando è ospitata come servizio di Windows, l'app viene avviata automaticamente dopo il riavvio del server.</span><span class="sxs-lookup"><span data-stu-id="1226b-276">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="1226b-277">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1226b-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1226b-278">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="1226b-278">Prerequisites</span></span>

* [<span data-ttu-id="1226b-279">ASP.NET Core SDK 2.1 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="1226b-279">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="1226b-280">PowerShell 6.2 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="1226b-280">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="1226b-281">Configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="1226b-281">App configuration</span></span>

<span data-ttu-id="1226b-282">L'app richiede i riferimenti ai pacchetti [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) e [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="1226b-282">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="1226b-283">Per eseguire test e debug durante l'esecuzione all'esterno di un servizio, aggiungere il codice per determinare se l'app è in esecuzione come servizio o è un'app console.</span><span class="sxs-lookup"><span data-stu-id="1226b-283">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="1226b-284">Controllare se il debugger è collegato o se è presente un'opzione `--console`.</span><span class="sxs-lookup"><span data-stu-id="1226b-284">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="1226b-285">Se una delle due condizioni è vera (l'app non è eseguita come servizio), chiamare <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="1226b-285">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="1226b-286">Se le condizioni sono false (l'app è eseguita come servizio):</span><span class="sxs-lookup"><span data-stu-id="1226b-286">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="1226b-287">Chiamare <xref:System.IO.Directory.SetCurrentDirectory*> e usare un percorso per la posizione di pubblicazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="1226b-287">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="1226b-288">Non chiamare <xref:System.IO.Directory.GetCurrentDirectory*> per ottenere il percorso perché un'app servizio Windows restituisce la cartella *C:\\WINDOWS\\system32* quando viene chiamato <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="1226b-288">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="1226b-289">Per altre informazioni, vedere la sezione [Directory corrente e radice del contenuto](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="1226b-289">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="1226b-290">Questo passaggio viene eseguito prima che l'app venga configurata in `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="1226b-290">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="1226b-291">Chiamare <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> per eseguire l'app come servizio.</span><span class="sxs-lookup"><span data-stu-id="1226b-291">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="1226b-292">Dato che il [provider di configurazione della riga di comando](xref:fundamentals/configuration/index#command-line-configuration-provider) richiede coppie nome-valore per gli argomenti della riga di comando, l'opzione `--console` viene rimossa dagli argomenti prima che <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> riceva gli argomenti.</span><span class="sxs-lookup"><span data-stu-id="1226b-292">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="1226b-293">Per scrivere nel registro eventi di Windows, aggiungere il provider EventLog a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="1226b-293">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="1226b-294">Impostare il livello di registrazione con la chiave `Logging:LogLevel:Default` nel file *appsettings.Production.json*.</span><span class="sxs-lookup"><span data-stu-id="1226b-294">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="1226b-295">Nel seguente esempio dell'app di esempio, viene chiamato `RunAsCustomService` invece di <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> per gestire gli eventi di durata all'interno dell'app.</span><span class="sxs-lookup"><span data-stu-id="1226b-295">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="1226b-296">Per altre informazioni, vedere la sezione [Gestire gli eventi di avvio e arresto](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="1226b-296">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="1226b-297">Tipo di distribuzione</span><span class="sxs-lookup"><span data-stu-id="1226b-297">Deployment type</span></span>

<span data-ttu-id="1226b-298">Per informazioni e consigli sugli scenari di distribuzione, vedere [Distribuzione di applicazioni .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="1226b-298">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="1226b-299">SDK</span><span class="sxs-lookup"><span data-stu-id="1226b-299">SDK</span></span>

<span data-ttu-id="1226b-300">Per un servizio basato su app Web che usa le pagine Razor o framework MVC, specificare il Web SDK nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="1226b-300">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="1226b-301">Se il servizio esegue solo attività in background (ad esempio, [servizi ospitati](xref:fundamentals/host/hosted-services)), specificare l'SDK di lavoro nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="1226b-301">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="1226b-302">Distribuzione dipendente dal framework</span><span class="sxs-lookup"><span data-stu-id="1226b-302">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="1226b-303">La distribuzione dipendente dal framework si basa sulla presenza di una versione condivisa a livello di sistema di .NET Core nel sistema di destinazione.</span><span class="sxs-lookup"><span data-stu-id="1226b-303">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="1226b-304">Quando lo scenario di distribuzione dipendente dal framework viene implementato in base alle indicazioni di questo articolo, l'SDK genera un file eseguibile (con estensione *exe*) detto *eseguibile dipendente dal framework*.</span><span class="sxs-lookup"><span data-stu-id="1226b-304">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="1226b-305">L'identificatore di Windows [Runtime (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contiene il framework di destinazione.</span><span class="sxs-lookup"><span data-stu-id="1226b-305">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="1226b-306">Nell'esempio seguente il RID è impostato su `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="1226b-306">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="1226b-307">La proprietà `<SelfContained>` è impostata su `false`.</span><span class="sxs-lookup"><span data-stu-id="1226b-307">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="1226b-308">Queste proprietà indicano all'SDK di generare un file eseguibile (con estensione *exe*) per Windows e un'app che dipende dal framework .NET Core condiviso.</span><span class="sxs-lookup"><span data-stu-id="1226b-308">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="1226b-309">Un file *web.config*, che viene normalmente generato quando si pubblica un'app ASP.NET Core, non è necessario per un'app di servizi Windows.</span><span class="sxs-lookup"><span data-stu-id="1226b-309">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="1226b-310">Per disabilitare la creazione del file *web.config*, aggiungere la proprietà `<IsTransformWebConfigDisabled>` impostata su `true`.</span><span class="sxs-lookup"><span data-stu-id="1226b-310">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="1226b-311">Distribuzione autonoma</span><span class="sxs-lookup"><span data-stu-id="1226b-311">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="1226b-312">Una distribuzione autonoma non si basa sulla presenza di un framework condiviso nel sistema host.</span><span class="sxs-lookup"><span data-stu-id="1226b-312">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="1226b-313">Il runtime e le dipendenze dell'app vengono distribuiti con l'app.</span><span class="sxs-lookup"><span data-stu-id="1226b-313">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="1226b-314">Un [identificatore di runtime (RID)](/dotnet/core/rid-catalog) di Windows viene incluso nel `<PropertyGroup>` che contiene il framework di destinazione:</span><span class="sxs-lookup"><span data-stu-id="1226b-314">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="1226b-315">Per eseguire la pubblicazione per più identificatori di runtime:</span><span class="sxs-lookup"><span data-stu-id="1226b-315">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="1226b-316">Specificare gli identificatori di runtime in un elenco delimitato da punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="1226b-316">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="1226b-317">Utilizzare il [ \<](/dotnet/core/tools/csproj#runtimeidentifiers) nome della proprietà RuntimeIdentifiers>(plurale).</span><span class="sxs-lookup"><span data-stu-id="1226b-317">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="1226b-318">Per altre informazioni, vedere il [Catalogo RID di .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="1226b-318">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="1226b-319">Una proprietà `<SelfContained>` è impostata su `true`:</span><span class="sxs-lookup"><span data-stu-id="1226b-319">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="1226b-320">Account utente del servizio</span><span class="sxs-lookup"><span data-stu-id="1226b-320">Service user account</span></span>

<span data-ttu-id="1226b-321">Per creare un account utente per un servizio, usare il cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) da una shell dei comandi di PowerShell 6 amministrativa.</span><span class="sxs-lookup"><span data-stu-id="1226b-321">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="1226b-322">In Aggiornamento di Windows 10 (ottobre 2018) (versione 1809/build 10.0.17763) o versioni successive:</span><span class="sxs-lookup"><span data-stu-id="1226b-322">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="1226b-323">In un sistema operativo Windows precedente ad Aggiornamento di Windows 10 (ottobre 2018) (versione 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="1226b-323">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="1226b-324">Fornire una [password complessa](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando richiesto.</span><span class="sxs-lookup"><span data-stu-id="1226b-324">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="1226b-325">Se non viene specificato il parametro `-AccountExpires` per il cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) con un valore <xref:System.DateTime> di scadenza, l'account non scade.</span><span class="sxs-lookup"><span data-stu-id="1226b-325">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="1226b-326">Per altre informazioni, vedere [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Service User Accounts](/windows/desktop/services/service-user-accounts) (Account utente di servizio).</span><span class="sxs-lookup"><span data-stu-id="1226b-326">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="1226b-327">Un approccio alternativo per la gestione degli utenti quando si usa Active Directory consiste nell'usare account del servizio gestito.</span><span class="sxs-lookup"><span data-stu-id="1226b-327">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="1226b-328">Per altre informazioni, vedere [Panoramica degli account del servizio gestito del gruppo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="1226b-328">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="1226b-329">Diritti Accesso come servizio</span><span class="sxs-lookup"><span data-stu-id="1226b-329">Log on as a service rights</span></span>

<span data-ttu-id="1226b-330">Per stabilire i diritti *Accesso come servizio* per un account utente di servizio:</span><span class="sxs-lookup"><span data-stu-id="1226b-330">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="1226b-331">Aprire l'editor Criteri di sicurezza locali eseguendo *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="1226b-331">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="1226b-332">Espandere il nodo **Criteri locali** e selezionare **Assegnazione diritti utente**.</span><span class="sxs-lookup"><span data-stu-id="1226b-332">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="1226b-333">Aprire il criterio **Accesso come servizio**.</span><span class="sxs-lookup"><span data-stu-id="1226b-333">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="1226b-334">Selezionare **Aggiungi utente o gruppo**.</span><span class="sxs-lookup"><span data-stu-id="1226b-334">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="1226b-335">Specificare il nome oggetto (account utente) in uno dei modi seguenti:</span><span class="sxs-lookup"><span data-stu-id="1226b-335">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="1226b-336">Digitare l'account utente (`{DOMAIN OR COMPUTER NAME\USER}`) nel campo del nome oggetto e scegliere **OK** per aggiungere l'utente al criterio.</span><span class="sxs-lookup"><span data-stu-id="1226b-336">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="1226b-337">Fare clic su **Advanced** (Avanzate).</span><span class="sxs-lookup"><span data-stu-id="1226b-337">Select **Advanced**.</span></span> <span data-ttu-id="1226b-338">Selezionare **Trova**.</span><span class="sxs-lookup"><span data-stu-id="1226b-338">Select **Find Now**.</span></span> <span data-ttu-id="1226b-339">Selezionare l'account utente dall'elenco.</span><span class="sxs-lookup"><span data-stu-id="1226b-339">Select the user account from the list.</span></span> <span data-ttu-id="1226b-340">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="1226b-340">Select **OK**.</span></span> <span data-ttu-id="1226b-341">Scegliere di nuovo **OK** per aggiungere l'utente al criterio.</span><span class="sxs-lookup"><span data-stu-id="1226b-341">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="1226b-342">Scegliere **OK** o **Applica** per accettare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="1226b-342">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="1226b-343">Creare e gestire il servizio di Windows</span><span class="sxs-lookup"><span data-stu-id="1226b-343">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="1226b-344">Creare un servizio</span><span class="sxs-lookup"><span data-stu-id="1226b-344">Create a service</span></span>

<span data-ttu-id="1226b-345">Usare i comandi di PowerShell per registrare un servizio.</span><span class="sxs-lookup"><span data-stu-id="1226b-345">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="1226b-346">Da una shell dei comandi di PowerShell 6 amministrativa eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="1226b-346">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="1226b-347">`{EXE PATH}`&ndash; Percorso della cartella dell'app nell'host, `d:\myservice`ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="1226b-347">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="1226b-348">Non includere l'eseguibile dell'app nel percorso.</span><span class="sxs-lookup"><span data-stu-id="1226b-348">Don't include the app's executable in the path.</span></span> <span data-ttu-id="1226b-349">Non è necessario aggiungere una barra finale.</span><span class="sxs-lookup"><span data-stu-id="1226b-349">A trailing slash isn't required.</span></span>
* <span data-ttu-id="1226b-350">`{DOMAIN OR COMPUTER NAME\USER}`&ndash; Account utente del servizio `Contoso\ServiceUser`(ad esempio, ).</span><span class="sxs-lookup"><span data-stu-id="1226b-350">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="1226b-351">`{SERVICE NAME}`&ndash; Nome del servizio `MyService`(ad esempio, ).</span><span class="sxs-lookup"><span data-stu-id="1226b-351">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="1226b-352">`{EXE FILE PATH}`&ndash; Percorso eseguibile dell'app, `d:\myservice\myservice.exe`ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="1226b-352">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="1226b-353">Includere il nome del file eseguibile con l'estensione.</span><span class="sxs-lookup"><span data-stu-id="1226b-353">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="1226b-354">`{DESCRIPTION}`&ndash; Descrizione del servizio `My sample service`(ad esempio, ).</span><span class="sxs-lookup"><span data-stu-id="1226b-354">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="1226b-355">`{DISPLAY NAME}`&ndash; Nome visualizzato del servizio `My Service`(ad esempio, ).</span><span class="sxs-lookup"><span data-stu-id="1226b-355">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="1226b-356">Avviare un servizio</span><span class="sxs-lookup"><span data-stu-id="1226b-356">Start a service</span></span>

<span data-ttu-id="1226b-357">Per avviare un servizio, usare il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1226b-357">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="1226b-358">L'avvio del servizio richiede alcuni secondi.</span><span class="sxs-lookup"><span data-stu-id="1226b-358">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="1226b-359">Determinare lo stato di un servizio</span><span class="sxs-lookup"><span data-stu-id="1226b-359">Determine a service's status</span></span>

<span data-ttu-id="1226b-360">Per verificare lo stato di un servizio, usare il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1226b-360">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="1226b-361">Lo stato viene indicato con uno dei valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="1226b-361">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="1226b-362">Arrestare un servizio</span><span class="sxs-lookup"><span data-stu-id="1226b-362">Stop a service</span></span>

<span data-ttu-id="1226b-363">Per arrestare un servizio, usare il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1226b-363">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="1226b-364">Rimuovere un servizio</span><span class="sxs-lookup"><span data-stu-id="1226b-364">Remove a service</span></span>

<span data-ttu-id="1226b-365">Dopo il breve lasso di tempo necessario per arrestare un servizio, rimuovere il servizio con il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1226b-365">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="1226b-366">Gestire gli eventi di avvio e arresto</span><span class="sxs-lookup"><span data-stu-id="1226b-366">Handle starting and stopping events</span></span>

<span data-ttu-id="1226b-367">Per gestire gli eventi <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:</span><span class="sxs-lookup"><span data-stu-id="1226b-367">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="1226b-368">Creare una classe che deriva da <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> con i metodi `OnStarting`, `OnStarted` e `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="1226b-368">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="1226b-369">Creare un metodo di estensione per <xref:Microsoft.AspNetCore.Hosting.IWebHost> che passa `CustomWebHostService` a <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="1226b-369">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="1226b-370">In `Program.Main` chiamare il metodo di estensione `RunAsCustomService` invece di <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="1226b-370">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="1226b-371">Per visualizzare il percorso di <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, fare riferimento all'esempio di codice illustrato nella sezione [Tipo di distribuzione](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="1226b-371">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="1226b-372">Scenari con server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="1226b-372">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="1226b-373">I servizi che interagiscono con le richieste da Internet o da una rete aziendale e si trovano dietro un proxy o un servizio di bilanciamento del carico potrebbero richiedere una configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="1226b-373">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="1226b-374">Per altre informazioni, vedere <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="1226b-374">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="1226b-375">Configurare gli endpoint</span><span class="sxs-lookup"><span data-stu-id="1226b-375">Configure endpoints</span></span>

<span data-ttu-id="1226b-376">Per impostazione predefinita, ASP.NET Core è associato a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="1226b-376">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="1226b-377">Configurare l'URL e `ASPNETCORE_URLS` la porta impostando la variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="1226b-377">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="1226b-378">Per ulteriori approcci di configurazione di URL e porte, vedere l'articolo relativo al server:</span><span class="sxs-lookup"><span data-stu-id="1226b-378">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="1226b-379">Le indicazioni precedenti riguardano il supporto per gli endpoint HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1226b-379">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="1226b-380">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span><span class="sxs-lookup"><span data-stu-id="1226b-380">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="1226b-381">L'uso del certificato di sviluppo ASP.NET Core HTTPS per proteggere un endpoint del servizio non è supportato.</span><span class="sxs-lookup"><span data-stu-id="1226b-381">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="1226b-382">Directory corrente e radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="1226b-382">Current directory and content root</span></span>

<span data-ttu-id="1226b-383">La directory di lavoro corrente restituita chiamando <xref:System.IO.Directory.GetCurrentDirectory*> per un servizio Windows è la cartella *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="1226b-383">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="1226b-384">La cartella *system32* non è un percorso appropriato per archiviare i file di un servizio, ad esempio i file di impostazioni.</span><span class="sxs-lookup"><span data-stu-id="1226b-384">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="1226b-385">Usare uno degli approcci seguenti per gestire e accedere agli asset e ai file di impostazioni di un servizio.</span><span class="sxs-lookup"><span data-stu-id="1226b-385">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="1226b-386">Impostare il percorso radice del contenuto sulla cartella dell'app</span><span class="sxs-lookup"><span data-stu-id="1226b-386">Set the content root path to the app's folder</span></span>

<span data-ttu-id="1226b-387"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> è lo stesso percorso fornito all'argomento `binPath` durante la creazione di un servizio.</span><span class="sxs-lookup"><span data-stu-id="1226b-387">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="1226b-388">Invece `GetCurrentDirectory` di chiamare per creare <xref:System.IO.Directory.SetCurrentDirectory*> percorsi ai file di impostazioni, chiamare con il percorso della radice del [contenuto](xref:fundamentals/index#content-root)dell'app.</span><span class="sxs-lookup"><span data-stu-id="1226b-388">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="1226b-389">In `Program.Main`, determinare il percorso della cartella dell'eseguibile del servizio e usare il percorso per stabilire la radice del contenuto dell'app:</span><span class="sxs-lookup"><span data-stu-id="1226b-389">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="1226b-390">Archiviare i file di un servizio in un percorso appropriato nel disco</span><span class="sxs-lookup"><span data-stu-id="1226b-390">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="1226b-391">Specificare un percorso assoluto con <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando si usa un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> per la cartella contenente i file.</span><span class="sxs-lookup"><span data-stu-id="1226b-391">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="1226b-392">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="1226b-392">Troubleshoot</span></span>

<span data-ttu-id="1226b-393">Per risolvere i problemi <xref:test/troubleshoot>relativi a un'app del servizio Windows, vedere .</span><span class="sxs-lookup"><span data-stu-id="1226b-393">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="1226b-394">Errori comuni</span><span class="sxs-lookup"><span data-stu-id="1226b-394">Common errors</span></span>

* <span data-ttu-id="1226b-395">È in uso una versione precedente o non definitiva di PowerShell.An old or pre-release version of PowerShell is in use.</span><span class="sxs-lookup"><span data-stu-id="1226b-395">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="1226b-396">Il servizio registrato non usa l'output **pubblicato** dell'app dal comando [dotnet publish.](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="1226b-396">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="1226b-397">L'output del comando [dotnet build](/dotnet/core/tools/dotnet-build) non è supportato per la distribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="1226b-397">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="1226b-398">Le risorse pubblicate si trovano in una delle seguenti cartelle a seconda del tipo di distribuzione:</span><span class="sxs-lookup"><span data-stu-id="1226b-398">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="1226b-399">*bin/Release/ TARGET FRAMEWORK/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="1226b-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="1226b-400">*bin/Release/*</span><span class="sxs-lookup"><span data-stu-id="1226b-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="1226b-401">Il servizio non è nello stato RUNNING.</span><span class="sxs-lookup"><span data-stu-id="1226b-401">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="1226b-402">I percorsi delle risorse utilizzate dall'app (ad esempio, i certificati) non sono corretti.</span><span class="sxs-lookup"><span data-stu-id="1226b-402">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="1226b-403">Il percorso di base di un servizio Windows è *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="1226b-403">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="1226b-404">L'utente non dispone dei diritti *di accesso come servizio.*</span><span class="sxs-lookup"><span data-stu-id="1226b-404">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="1226b-405">La password dell'utente è scaduta o `New-Service` passata in modo non corretto durante l'esecuzione del comando PowerShell.The user's password is expired or incorrectly passed when executing the PowerShell command.</span><span class="sxs-lookup"><span data-stu-id="1226b-405">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="1226b-406">L'app richiede ASP.NET'autenticazione Core, ma non è configurata per le connessioni protette (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="1226b-406">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="1226b-407">La porta dell'URL della richiesta non è corretta o non è configurata correttamente nell'app.</span><span class="sxs-lookup"><span data-stu-id="1226b-407">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="1226b-408">Registri eventi di sistema e applicazioni</span><span class="sxs-lookup"><span data-stu-id="1226b-408">System and Application Event Logs</span></span>

<span data-ttu-id="1226b-409">Accedere ai registri eventi di sistema e dell'applicazione:</span><span class="sxs-lookup"><span data-stu-id="1226b-409">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="1226b-410">Aprire il menu Start, cercare *Visualizzatore eventi*e selezionare l'app **Visualizzatore eventi.**</span><span class="sxs-lookup"><span data-stu-id="1226b-410">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="1226b-411">In **Visualizzatore eventi** aprire il nodo **Registri di Windows**.</span><span class="sxs-lookup"><span data-stu-id="1226b-411">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="1226b-412">Selezionare **Sistema** per aprire il registro eventi di sistema.</span><span class="sxs-lookup"><span data-stu-id="1226b-412">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="1226b-413">Selezionare **Applicazione** per aprire il log eventi dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="1226b-413">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="1226b-414">Cercare gli errori associati all'app in cui si è verificato il problema.</span><span class="sxs-lookup"><span data-stu-id="1226b-414">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="1226b-415">Eseguire l'app da un prompt dei comandi</span><span class="sxs-lookup"><span data-stu-id="1226b-415">Run the app at a command prompt</span></span>

<span data-ttu-id="1226b-416">Molti errori di avvio non producono informazioni utili nei registri eventi.</span><span class="sxs-lookup"><span data-stu-id="1226b-416">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="1226b-417">È possibile individuare la causa di alcuni errori eseguendo l'app da un prompt dei comandi nel sistema host.</span><span class="sxs-lookup"><span data-stu-id="1226b-417">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="1226b-418">Per registrare ulteriori dettagli dall'app, abbassare il livello di [log](xref:fundamentals/logging/index#log-level) o eseguire l'app nell'ambiente di [sviluppo](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="1226b-418">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="1226b-419">Cancellare le cache dei pacchetti</span><span class="sxs-lookup"><span data-stu-id="1226b-419">Clear package caches</span></span>

<span data-ttu-id="1226b-420">Un'app funzionante potrebbe non riuscire immediatamente dopo l'aggiornamento di .NET Core SDK nel computer di sviluppo o la modifica delle versioni del pacchetto all'interno dell'app.</span><span class="sxs-lookup"><span data-stu-id="1226b-420">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="1226b-421">In alcuni casi i pacchetti incoerenti possono interrompere un'app quando si eseguono aggiornamenti principali.</span><span class="sxs-lookup"><span data-stu-id="1226b-421">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="1226b-422">La maggior parte di questi problemi può essere risolta attenendosi alle istruzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="1226b-422">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="1226b-423">Eliminare le cartelle *bin* e *obj*.</span><span class="sxs-lookup"><span data-stu-id="1226b-423">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="1226b-424">Cancellare le cache dei pacchetti eseguendo [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) da una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="1226b-424">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="1226b-425">La cancellazione delle cache dei pacchetti può essere eseguita anche `nuget locals all -clear`con lo strumento [nuget.exe](https://www.nuget.org/downloads) e l'esecuzione del comando .</span><span class="sxs-lookup"><span data-stu-id="1226b-425">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="1226b-426">*nuget.exe* non è un'installazione inclusa con il sistema operativo desktop Windows e deve essere ottenuta separatamente dal [sito Web NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="1226b-426">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="1226b-427">Ripristinare e ricompilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="1226b-427">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="1226b-428">Eliminare tutti i file nella cartella di distribuzione nel server prima di ridistribuire l'app.</span><span class="sxs-lookup"><span data-stu-id="1226b-428">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="1226b-429">App lenta o bloccata</span><span class="sxs-lookup"><span data-stu-id="1226b-429">Slow or hanging app</span></span>

<span data-ttu-id="1226b-430">Un dump di *arresto anomalo* del sistema è uno snapshot della memoria del sistema e consente di determinare la causa di un arresto anomalo dell'app, un errore di avvio o un'app lenta.</span><span class="sxs-lookup"><span data-stu-id="1226b-430">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="1226b-431">Arresto anomalo o eccezione di un'app</span><span class="sxs-lookup"><span data-stu-id="1226b-431">App crashes or encounters an exception</span></span>

<span data-ttu-id="1226b-432">Ottenere e analizzare un dump da [Segnalazione errori Windows](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="1226b-432">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="1226b-433">Creare una cartella per i file dump di arresto anomalo del sistema in `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="1226b-433">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="1226b-434">Eseguire lo [script di PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) con il nome dell'eseguibile dell'applicazione:Run the EnableDumps PowerShell script with the application executable name:</span><span class="sxs-lookup"><span data-stu-id="1226b-434">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="1226b-435">Eseguire l'app nelle condizioni che causano l'arresto anomalo.</span><span class="sxs-lookup"><span data-stu-id="1226b-435">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="1226b-436">Dopo che si è verificato l'arresto anomalo, eseguire lo [script di PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="1226b-436">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="1226b-437">Dopo l'arresto anomalo di un'app e la raccolta dei dump, l'app può terminare normalmente.</span><span class="sxs-lookup"><span data-stu-id="1226b-437">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="1226b-438">Lo script di PowerShell configura Segnalazione errori Windows per raccogliere fino a cinque dump per ogni app.</span><span class="sxs-lookup"><span data-stu-id="1226b-438">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="1226b-439">I dump di arresto anomalo del sistema potrebbero richiedere una grande quantità di spazio su disco (fino a diversi gigabyte ognuno).</span><span class="sxs-lookup"><span data-stu-id="1226b-439">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="1226b-440">L'app si blocca, si verifica un errore durante l'avvio o viene eseguita normalmente</span><span class="sxs-lookup"><span data-stu-id="1226b-440">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="1226b-441">Quando un'app *si blocca* (si blocca ma non si arresta in modo anomalo), non riesce durante l'avvio o viene eseguito normalmente, vedere File di dump in [modalità utente: Scelta dello strumento migliore](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) per selezionare uno strumento appropriato per produrre il dump.</span><span class="sxs-lookup"><span data-stu-id="1226b-441">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="1226b-442">Analizzare il dump</span><span class="sxs-lookup"><span data-stu-id="1226b-442">Analyze the dump</span></span>

<span data-ttu-id="1226b-443">È possibile analizzare un dump usando diversi approcci.</span><span class="sxs-lookup"><span data-stu-id="1226b-443">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="1226b-444">Per altre informazioni, vedere [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analisi di un file dump in modalità utente).</span><span class="sxs-lookup"><span data-stu-id="1226b-444">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1226b-445">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="1226b-445">Additional resources</span></span>

* <span data-ttu-id="1226b-446">[Configurazione dell'endpoint Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (include la configurazione HTTPS e il supporto SNI)</span><span class="sxs-lookup"><span data-stu-id="1226b-446">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="1226b-447">È possibile ospitare un'app ASP.NET Core in Windows come [servizio Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) senza usare IIS.</span><span class="sxs-lookup"><span data-stu-id="1226b-447">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="1226b-448">Quando è ospitata come servizio di Windows, l'app viene avviata automaticamente dopo il riavvio del server.</span><span class="sxs-lookup"><span data-stu-id="1226b-448">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="1226b-449">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ( come[scaricare](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1226b-449">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1226b-450">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="1226b-450">Prerequisites</span></span>

* [<span data-ttu-id="1226b-451">ASP.NET Core SDK 2.1 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="1226b-451">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="1226b-452">PowerShell 6.2 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="1226b-452">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="1226b-453">Configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="1226b-453">App configuration</span></span>

<span data-ttu-id="1226b-454">L'app richiede i riferimenti ai pacchetti [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) e [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="1226b-454">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="1226b-455">Per eseguire test e debug durante l'esecuzione all'esterno di un servizio, aggiungere il codice per determinare se l'app è in esecuzione come servizio o è un'app console.</span><span class="sxs-lookup"><span data-stu-id="1226b-455">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="1226b-456">Controllare se il debugger è collegato o se è presente un'opzione `--console`.</span><span class="sxs-lookup"><span data-stu-id="1226b-456">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="1226b-457">Se una delle due condizioni è vera (l'app non è eseguita come servizio), chiamare <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="1226b-457">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="1226b-458">Se le condizioni sono false (l'app è eseguita come servizio):</span><span class="sxs-lookup"><span data-stu-id="1226b-458">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="1226b-459">Chiamare <xref:System.IO.Directory.SetCurrentDirectory*> e usare un percorso per la posizione di pubblicazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="1226b-459">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="1226b-460">Non chiamare <xref:System.IO.Directory.GetCurrentDirectory*> per ottenere il percorso perché un'app servizio Windows restituisce la cartella *C:\\WINDOWS\\system32* quando viene chiamato <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="1226b-460">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="1226b-461">Per altre informazioni, vedere la sezione [Directory corrente e radice del contenuto](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="1226b-461">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="1226b-462">Questo passaggio viene eseguito prima che l'app venga configurata in `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="1226b-462">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="1226b-463">Chiamare <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> per eseguire l'app come servizio.</span><span class="sxs-lookup"><span data-stu-id="1226b-463">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="1226b-464">Dato che il [provider di configurazione della riga di comando](xref:fundamentals/configuration/index#command-line-configuration-provider) richiede coppie nome-valore per gli argomenti della riga di comando, l'opzione `--console` viene rimossa dagli argomenti prima che <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> riceva gli argomenti.</span><span class="sxs-lookup"><span data-stu-id="1226b-464">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="1226b-465">Per scrivere nel registro eventi di Windows, aggiungere il provider EventLog a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="1226b-465">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="1226b-466">Impostare il livello di registrazione con la chiave `Logging:LogLevel:Default` nel file *appsettings.Production.json*.</span><span class="sxs-lookup"><span data-stu-id="1226b-466">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="1226b-467">Nel seguente esempio dell'app di esempio, viene chiamato `RunAsCustomService` invece di <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> per gestire gli eventi di durata all'interno dell'app.</span><span class="sxs-lookup"><span data-stu-id="1226b-467">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="1226b-468">Per altre informazioni, vedere la sezione [Gestire gli eventi di avvio e arresto](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="1226b-468">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="1226b-469">Tipo di distribuzione</span><span class="sxs-lookup"><span data-stu-id="1226b-469">Deployment type</span></span>

<span data-ttu-id="1226b-470">Per informazioni e consigli sugli scenari di distribuzione, vedere [Distribuzione di applicazioni .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="1226b-470">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="1226b-471">SDK</span><span class="sxs-lookup"><span data-stu-id="1226b-471">SDK</span></span>

<span data-ttu-id="1226b-472">Per un servizio basato su app Web che usa le pagine Razor o framework MVC, specificare il Web SDK nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="1226b-472">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="1226b-473">Se il servizio esegue solo attività in background (ad esempio, [servizi ospitati](xref:fundamentals/host/hosted-services)), specificare l'SDK di lavoro nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="1226b-473">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="1226b-474">Distribuzione dipendente dal framework</span><span class="sxs-lookup"><span data-stu-id="1226b-474">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="1226b-475">La distribuzione dipendente dal framework si basa sulla presenza di una versione condivisa a livello di sistema di .NET Core nel sistema di destinazione.</span><span class="sxs-lookup"><span data-stu-id="1226b-475">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="1226b-476">Quando lo scenario di distribuzione dipendente dal framework viene implementato in base alle indicazioni di questo articolo, l'SDK genera un file eseguibile (con estensione *exe*) detto *eseguibile dipendente dal framework*.</span><span class="sxs-lookup"><span data-stu-id="1226b-476">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="1226b-477">L'identificatore di Windows [Runtime (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contiene il framework di destinazione.</span><span class="sxs-lookup"><span data-stu-id="1226b-477">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="1226b-478">Nell'esempio seguente il RID è impostato su `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="1226b-478">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="1226b-479">La proprietà `<SelfContained>` è impostata su `false`.</span><span class="sxs-lookup"><span data-stu-id="1226b-479">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="1226b-480">Queste proprietà indicano all'SDK di generare un file eseguibile (con estensione *exe*) per Windows e un'app che dipende dal framework .NET Core condiviso.</span><span class="sxs-lookup"><span data-stu-id="1226b-480">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="1226b-481">La proprietà `<UseAppHost>` è impostata su `true`.</span><span class="sxs-lookup"><span data-stu-id="1226b-481">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="1226b-482">Questa proprietà fornisce il servizio con un percorso di attivazione (un file eseguibile, *.exe*) per una distribuzione dipendente dal framework.</span><span class="sxs-lookup"><span data-stu-id="1226b-482">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="1226b-483">Un file *web.config*, che viene normalmente generato quando si pubblica un'app ASP.NET Core, non è necessario per un'app di servizi Windows.</span><span class="sxs-lookup"><span data-stu-id="1226b-483">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="1226b-484">Per disabilitare la creazione del file *web.config*, aggiungere la proprietà `<IsTransformWebConfigDisabled>` impostata su `true`.</span><span class="sxs-lookup"><span data-stu-id="1226b-484">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="1226b-485">Distribuzione autonoma</span><span class="sxs-lookup"><span data-stu-id="1226b-485">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="1226b-486">Una distribuzione autonoma non si basa sulla presenza di un framework condiviso nel sistema host.</span><span class="sxs-lookup"><span data-stu-id="1226b-486">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="1226b-487">Il runtime e le dipendenze dell'app vengono distribuiti con l'app.</span><span class="sxs-lookup"><span data-stu-id="1226b-487">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="1226b-488">Un [identificatore di runtime (RID)](/dotnet/core/rid-catalog) di Windows viene incluso nel `<PropertyGroup>` che contiene il framework di destinazione:</span><span class="sxs-lookup"><span data-stu-id="1226b-488">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="1226b-489">Per eseguire la pubblicazione per più identificatori di runtime:</span><span class="sxs-lookup"><span data-stu-id="1226b-489">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="1226b-490">Specificare gli identificatori di runtime in un elenco delimitato da punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="1226b-490">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="1226b-491">Utilizzare il [ \<](/dotnet/core/tools/csproj#runtimeidentifiers) nome della proprietà RuntimeIdentifiers>(plurale).</span><span class="sxs-lookup"><span data-stu-id="1226b-491">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="1226b-492">Per altre informazioni, vedere il [Catalogo RID di .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="1226b-492">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="1226b-493">Una proprietà `<SelfContained>` è impostata su `true`:</span><span class="sxs-lookup"><span data-stu-id="1226b-493">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="1226b-494">Account utente del servizio</span><span class="sxs-lookup"><span data-stu-id="1226b-494">Service user account</span></span>

<span data-ttu-id="1226b-495">Per creare un account utente per un servizio, usare il cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) da una shell dei comandi di PowerShell 6 amministrativa.</span><span class="sxs-lookup"><span data-stu-id="1226b-495">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="1226b-496">In Aggiornamento di Windows 10 (ottobre 2018) (versione 1809/build 10.0.17763) o versioni successive:</span><span class="sxs-lookup"><span data-stu-id="1226b-496">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="1226b-497">In un sistema operativo Windows precedente ad Aggiornamento di Windows 10 (ottobre 2018) (versione 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="1226b-497">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="1226b-498">Fornire una [password complessa](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando richiesto.</span><span class="sxs-lookup"><span data-stu-id="1226b-498">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="1226b-499">Se non viene specificato il parametro `-AccountExpires` per il cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) con un valore <xref:System.DateTime> di scadenza, l'account non scade.</span><span class="sxs-lookup"><span data-stu-id="1226b-499">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="1226b-500">Per altre informazioni, vedere [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Service User Accounts](/windows/desktop/services/service-user-accounts) (Account utente di servizio).</span><span class="sxs-lookup"><span data-stu-id="1226b-500">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="1226b-501">Un approccio alternativo per la gestione degli utenti quando si usa Active Directory consiste nell'usare account del servizio gestito.</span><span class="sxs-lookup"><span data-stu-id="1226b-501">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="1226b-502">Per altre informazioni, vedere [Panoramica degli account del servizio gestito del gruppo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="1226b-502">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="1226b-503">Diritti Accesso come servizio</span><span class="sxs-lookup"><span data-stu-id="1226b-503">Log on as a service rights</span></span>

<span data-ttu-id="1226b-504">Per stabilire i diritti *Accesso come servizio* per un account utente di servizio:</span><span class="sxs-lookup"><span data-stu-id="1226b-504">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="1226b-505">Aprire l'editor Criteri di sicurezza locali eseguendo *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="1226b-505">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="1226b-506">Espandere il nodo **Criteri locali** e selezionare **Assegnazione diritti utente**.</span><span class="sxs-lookup"><span data-stu-id="1226b-506">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="1226b-507">Aprire il criterio **Accesso come servizio**.</span><span class="sxs-lookup"><span data-stu-id="1226b-507">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="1226b-508">Selezionare **Aggiungi utente o gruppo**.</span><span class="sxs-lookup"><span data-stu-id="1226b-508">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="1226b-509">Specificare il nome oggetto (account utente) in uno dei modi seguenti:</span><span class="sxs-lookup"><span data-stu-id="1226b-509">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="1226b-510">Digitare l'account utente (`{DOMAIN OR COMPUTER NAME\USER}`) nel campo del nome oggetto e scegliere **OK** per aggiungere l'utente al criterio.</span><span class="sxs-lookup"><span data-stu-id="1226b-510">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="1226b-511">Fare clic su **Advanced** (Avanzate).</span><span class="sxs-lookup"><span data-stu-id="1226b-511">Select **Advanced**.</span></span> <span data-ttu-id="1226b-512">Selezionare **Trova**.</span><span class="sxs-lookup"><span data-stu-id="1226b-512">Select **Find Now**.</span></span> <span data-ttu-id="1226b-513">Selezionare l'account utente dall'elenco.</span><span class="sxs-lookup"><span data-stu-id="1226b-513">Select the user account from the list.</span></span> <span data-ttu-id="1226b-514">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="1226b-514">Select **OK**.</span></span> <span data-ttu-id="1226b-515">Scegliere di nuovo **OK** per aggiungere l'utente al criterio.</span><span class="sxs-lookup"><span data-stu-id="1226b-515">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="1226b-516">Scegliere **OK** o **Applica** per accettare le modifiche.</span><span class="sxs-lookup"><span data-stu-id="1226b-516">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="1226b-517">Creare e gestire il servizio di Windows</span><span class="sxs-lookup"><span data-stu-id="1226b-517">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="1226b-518">Creare un servizio</span><span class="sxs-lookup"><span data-stu-id="1226b-518">Create a service</span></span>

<span data-ttu-id="1226b-519">Usare i comandi di PowerShell per registrare un servizio.</span><span class="sxs-lookup"><span data-stu-id="1226b-519">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="1226b-520">Da una shell dei comandi di PowerShell 6 amministrativa eseguire i comandi seguenti:</span><span class="sxs-lookup"><span data-stu-id="1226b-520">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="1226b-521">`{EXE PATH}`&ndash; Percorso della cartella dell'app nell'host, `d:\myservice`ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="1226b-521">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="1226b-522">Non includere l'eseguibile dell'app nel percorso.</span><span class="sxs-lookup"><span data-stu-id="1226b-522">Don't include the app's executable in the path.</span></span> <span data-ttu-id="1226b-523">Non è necessario aggiungere una barra finale.</span><span class="sxs-lookup"><span data-stu-id="1226b-523">A trailing slash isn't required.</span></span>
* <span data-ttu-id="1226b-524">`{DOMAIN OR COMPUTER NAME\USER}`&ndash; Account utente del servizio `Contoso\ServiceUser`(ad esempio, ).</span><span class="sxs-lookup"><span data-stu-id="1226b-524">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="1226b-525">`{SERVICE NAME}`&ndash; Nome del servizio `MyService`(ad esempio, ).</span><span class="sxs-lookup"><span data-stu-id="1226b-525">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="1226b-526">`{EXE FILE PATH}`&ndash; Percorso eseguibile dell'app, `d:\myservice\myservice.exe`ad esempio ).</span><span class="sxs-lookup"><span data-stu-id="1226b-526">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="1226b-527">Includere il nome del file eseguibile con l'estensione.</span><span class="sxs-lookup"><span data-stu-id="1226b-527">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="1226b-528">`{DESCRIPTION}`&ndash; Descrizione del servizio `My sample service`(ad esempio, ).</span><span class="sxs-lookup"><span data-stu-id="1226b-528">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="1226b-529">`{DISPLAY NAME}`&ndash; Nome visualizzato del servizio `My Service`(ad esempio, ).</span><span class="sxs-lookup"><span data-stu-id="1226b-529">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="1226b-530">Avviare un servizio</span><span class="sxs-lookup"><span data-stu-id="1226b-530">Start a service</span></span>

<span data-ttu-id="1226b-531">Per avviare un servizio, usare il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1226b-531">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="1226b-532">L'avvio del servizio richiede alcuni secondi.</span><span class="sxs-lookup"><span data-stu-id="1226b-532">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="1226b-533">Determinare lo stato di un servizio</span><span class="sxs-lookup"><span data-stu-id="1226b-533">Determine a service's status</span></span>

<span data-ttu-id="1226b-534">Per verificare lo stato di un servizio, usare il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1226b-534">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="1226b-535">Lo stato viene indicato con uno dei valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="1226b-535">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="1226b-536">Arrestare un servizio</span><span class="sxs-lookup"><span data-stu-id="1226b-536">Stop a service</span></span>

<span data-ttu-id="1226b-537">Per arrestare un servizio, usare il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1226b-537">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="1226b-538">Rimuovere un servizio</span><span class="sxs-lookup"><span data-stu-id="1226b-538">Remove a service</span></span>

<span data-ttu-id="1226b-539">Dopo il breve lasso di tempo necessario per arrestare un servizio, rimuovere il servizio con il comando di PowerShell 6 seguente:</span><span class="sxs-lookup"><span data-stu-id="1226b-539">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="1226b-540">Gestire gli eventi di avvio e arresto</span><span class="sxs-lookup"><span data-stu-id="1226b-540">Handle starting and stopping events</span></span>

<span data-ttu-id="1226b-541">Per gestire gli eventi <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:</span><span class="sxs-lookup"><span data-stu-id="1226b-541">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="1226b-542">Creare una classe che deriva da <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> con i metodi `OnStarting`, `OnStarted` e `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="1226b-542">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="1226b-543">Creare un metodo di estensione per <xref:Microsoft.AspNetCore.Hosting.IWebHost> che passa `CustomWebHostService` a <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="1226b-543">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="1226b-544">In `Program.Main` chiamare il metodo di estensione `RunAsCustomService` invece di <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="1226b-544">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="1226b-545">Per visualizzare il percorso di <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, fare riferimento all'esempio di codice illustrato nella sezione [Tipo di distribuzione](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="1226b-545">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="1226b-546">Scenari con server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="1226b-546">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="1226b-547">I servizi che interagiscono con le richieste da Internet o da una rete aziendale e si trovano dietro un proxy o un servizio di bilanciamento del carico potrebbero richiedere una configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="1226b-547">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="1226b-548">Per altre informazioni, vedere <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="1226b-548">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="1226b-549">Configurare gli endpoint</span><span class="sxs-lookup"><span data-stu-id="1226b-549">Configure endpoints</span></span>

<span data-ttu-id="1226b-550">Per impostazione predefinita, ASP.NET Core è associato a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="1226b-550">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="1226b-551">Configurare l'URL e `ASPNETCORE_URLS` la porta impostando la variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="1226b-551">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="1226b-552">Per ulteriori approcci di configurazione di URL e porte, vedere l'articolo relativo al server:</span><span class="sxs-lookup"><span data-stu-id="1226b-552">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="1226b-553">Le indicazioni precedenti riguardano il supporto per gli endpoint HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1226b-553">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="1226b-554">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span><span class="sxs-lookup"><span data-stu-id="1226b-554">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="1226b-555">L'uso del certificato di sviluppo ASP.NET Core HTTPS per proteggere un endpoint del servizio non è supportato.</span><span class="sxs-lookup"><span data-stu-id="1226b-555">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="1226b-556">Directory corrente e radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="1226b-556">Current directory and content root</span></span>

<span data-ttu-id="1226b-557">La directory di lavoro corrente restituita chiamando <xref:System.IO.Directory.GetCurrentDirectory*> per un servizio Windows è la cartella *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="1226b-557">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="1226b-558">La cartella *system32* non è un percorso appropriato per archiviare i file di un servizio, ad esempio i file di impostazioni.</span><span class="sxs-lookup"><span data-stu-id="1226b-558">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="1226b-559">Usare uno degli approcci seguenti per gestire e accedere agli asset e ai file di impostazioni di un servizio.</span><span class="sxs-lookup"><span data-stu-id="1226b-559">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="1226b-560">Impostare il percorso radice del contenuto sulla cartella dell'app</span><span class="sxs-lookup"><span data-stu-id="1226b-560">Set the content root path to the app's folder</span></span>

<span data-ttu-id="1226b-561"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> è lo stesso percorso fornito all'argomento `binPath` durante la creazione di un servizio.</span><span class="sxs-lookup"><span data-stu-id="1226b-561">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="1226b-562">Invece `GetCurrentDirectory` di chiamare per creare <xref:System.IO.Directory.SetCurrentDirectory*> percorsi ai file di impostazioni, chiamare con il percorso della radice del [contenuto](xref:fundamentals/index#content-root)dell'app.</span><span class="sxs-lookup"><span data-stu-id="1226b-562">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="1226b-563">In `Program.Main`, determinare il percorso della cartella dell'eseguibile del servizio e usare il percorso per stabilire la radice del contenuto dell'app:</span><span class="sxs-lookup"><span data-stu-id="1226b-563">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="1226b-564">Archiviare i file di un servizio in un percorso appropriato nel disco</span><span class="sxs-lookup"><span data-stu-id="1226b-564">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="1226b-565">Specificare un percorso assoluto con <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando si usa un <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> per la cartella contenente i file.</span><span class="sxs-lookup"><span data-stu-id="1226b-565">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="1226b-566">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="1226b-566">Troubleshoot</span></span>

<span data-ttu-id="1226b-567">Per risolvere i problemi <xref:test/troubleshoot>relativi a un'app del servizio Windows, vedere .</span><span class="sxs-lookup"><span data-stu-id="1226b-567">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="1226b-568">Errori comuni</span><span class="sxs-lookup"><span data-stu-id="1226b-568">Common errors</span></span>

* <span data-ttu-id="1226b-569">È in uso una versione precedente o non definitiva di PowerShell.An old or pre-release version of PowerShell is in use.</span><span class="sxs-lookup"><span data-stu-id="1226b-569">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="1226b-570">Il servizio registrato non usa l'output **pubblicato** dell'app dal comando [dotnet publish.](/dotnet/core/tools/dotnet-publish)</span><span class="sxs-lookup"><span data-stu-id="1226b-570">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="1226b-571">L'output del comando [dotnet build](/dotnet/core/tools/dotnet-build) non è supportato per la distribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="1226b-571">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="1226b-572">Le risorse pubblicate si trovano in una delle seguenti cartelle a seconda del tipo di distribuzione:</span><span class="sxs-lookup"><span data-stu-id="1226b-572">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="1226b-573">*bin/Release/ TARGET FRAMEWORK/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="1226b-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="1226b-574">*bin/Release/*</span><span class="sxs-lookup"><span data-stu-id="1226b-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="1226b-575">Il servizio non è nello stato RUNNING.</span><span class="sxs-lookup"><span data-stu-id="1226b-575">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="1226b-576">I percorsi delle risorse utilizzate dall'app (ad esempio, i certificati) non sono corretti.</span><span class="sxs-lookup"><span data-stu-id="1226b-576">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="1226b-577">Il percorso di base di un servizio Windows è *c:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="1226b-577">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="1226b-578">L'utente non dispone dei diritti *di accesso come servizio.*</span><span class="sxs-lookup"><span data-stu-id="1226b-578">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="1226b-579">La password dell'utente è scaduta o `New-Service` passata in modo non corretto durante l'esecuzione del comando PowerShell.The user's password is expired or incorrectly passed when executing the PowerShell command.</span><span class="sxs-lookup"><span data-stu-id="1226b-579">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="1226b-580">L'app richiede ASP.NET'autenticazione Core, ma non è configurata per le connessioni protette (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="1226b-580">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="1226b-581">La porta dell'URL della richiesta non è corretta o non è configurata correttamente nell'app.</span><span class="sxs-lookup"><span data-stu-id="1226b-581">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="1226b-582">Registri eventi di sistema e applicazioni</span><span class="sxs-lookup"><span data-stu-id="1226b-582">System and Application Event Logs</span></span>

<span data-ttu-id="1226b-583">Accedere ai registri eventi di sistema e dell'applicazione:</span><span class="sxs-lookup"><span data-stu-id="1226b-583">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="1226b-584">Aprire il menu Start, cercare *Visualizzatore eventi*e selezionare l'app **Visualizzatore eventi.**</span><span class="sxs-lookup"><span data-stu-id="1226b-584">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="1226b-585">In **Visualizzatore eventi** aprire il nodo **Registri di Windows**.</span><span class="sxs-lookup"><span data-stu-id="1226b-585">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="1226b-586">Selezionare **Sistema** per aprire il registro eventi di sistema.</span><span class="sxs-lookup"><span data-stu-id="1226b-586">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="1226b-587">Selezionare **Applicazione** per aprire il log eventi dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="1226b-587">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="1226b-588">Cercare gli errori associati all'app in cui si è verificato il problema.</span><span class="sxs-lookup"><span data-stu-id="1226b-588">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="1226b-589">Eseguire l'app da un prompt dei comandi</span><span class="sxs-lookup"><span data-stu-id="1226b-589">Run the app at a command prompt</span></span>

<span data-ttu-id="1226b-590">Molti errori di avvio non producono informazioni utili nei registri eventi.</span><span class="sxs-lookup"><span data-stu-id="1226b-590">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="1226b-591">È possibile individuare la causa di alcuni errori eseguendo l'app da un prompt dei comandi nel sistema host.</span><span class="sxs-lookup"><span data-stu-id="1226b-591">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="1226b-592">Per registrare ulteriori dettagli dall'app, abbassare il livello di [log](xref:fundamentals/logging/index#log-level) o eseguire l'app nell'ambiente di [sviluppo](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="1226b-592">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="1226b-593">Cancellare le cache dei pacchetti</span><span class="sxs-lookup"><span data-stu-id="1226b-593">Clear package caches</span></span>

<span data-ttu-id="1226b-594">Un'app funzionante potrebbe non riuscire immediatamente dopo l'aggiornamento di .NET Core SDK nel computer di sviluppo o la modifica delle versioni del pacchetto all'interno dell'app.</span><span class="sxs-lookup"><span data-stu-id="1226b-594">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="1226b-595">In alcuni casi i pacchetti incoerenti possono interrompere un'app quando si eseguono aggiornamenti principali.</span><span class="sxs-lookup"><span data-stu-id="1226b-595">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="1226b-596">La maggior parte di questi problemi può essere risolta attenendosi alle istruzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="1226b-596">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="1226b-597">Eliminare le cartelle *bin* e *obj*.</span><span class="sxs-lookup"><span data-stu-id="1226b-597">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="1226b-598">Cancellare le cache dei pacchetti eseguendo [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) da una shell dei comandi.</span><span class="sxs-lookup"><span data-stu-id="1226b-598">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="1226b-599">La cancellazione delle cache dei pacchetti può essere eseguita anche `nuget locals all -clear`con lo strumento [nuget.exe](https://www.nuget.org/downloads) e l'esecuzione del comando .</span><span class="sxs-lookup"><span data-stu-id="1226b-599">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="1226b-600">*nuget.exe* non è un'installazione inclusa con il sistema operativo desktop Windows e deve essere ottenuta separatamente dal [sito Web NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="1226b-600">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="1226b-601">Ripristinare e ricompilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="1226b-601">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="1226b-602">Eliminare tutti i file nella cartella di distribuzione nel server prima di ridistribuire l'app.</span><span class="sxs-lookup"><span data-stu-id="1226b-602">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="1226b-603">App lenta o bloccata</span><span class="sxs-lookup"><span data-stu-id="1226b-603">Slow or hanging app</span></span>

<span data-ttu-id="1226b-604">Un dump di *arresto anomalo* del sistema è uno snapshot della memoria del sistema e consente di determinare la causa di un arresto anomalo dell'app, un errore di avvio o un'app lenta.</span><span class="sxs-lookup"><span data-stu-id="1226b-604">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="1226b-605">Arresto anomalo o eccezione di un'app</span><span class="sxs-lookup"><span data-stu-id="1226b-605">App crashes or encounters an exception</span></span>

<span data-ttu-id="1226b-606">Ottenere e analizzare un dump da [Segnalazione errori Windows](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="1226b-606">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="1226b-607">Creare una cartella per i file dump di arresto anomalo del sistema in `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="1226b-607">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="1226b-608">Eseguire lo [script di PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) con il nome dell'eseguibile dell'applicazione:Run the EnableDumps PowerShell script with the application executable name:</span><span class="sxs-lookup"><span data-stu-id="1226b-608">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="1226b-609">Eseguire l'app nelle condizioni che causano l'arresto anomalo.</span><span class="sxs-lookup"><span data-stu-id="1226b-609">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="1226b-610">Dopo che si è verificato l'arresto anomalo, eseguire lo [script di PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="1226b-610">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="1226b-611">Dopo l'arresto anomalo di un'app e la raccolta dei dump, l'app può terminare normalmente.</span><span class="sxs-lookup"><span data-stu-id="1226b-611">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="1226b-612">Lo script di PowerShell configura Segnalazione errori Windows per raccogliere fino a cinque dump per ogni app.</span><span class="sxs-lookup"><span data-stu-id="1226b-612">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="1226b-613">I dump di arresto anomalo del sistema potrebbero richiedere una grande quantità di spazio su disco (fino a diversi gigabyte ognuno).</span><span class="sxs-lookup"><span data-stu-id="1226b-613">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="1226b-614">L'app si blocca, si verifica un errore durante l'avvio o viene eseguita normalmente</span><span class="sxs-lookup"><span data-stu-id="1226b-614">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="1226b-615">Quando un'app *si blocca* (si blocca ma non si arresta in modo anomalo), non riesce durante l'avvio o viene eseguito normalmente, vedere File di dump in [modalità utente: Scelta dello strumento migliore](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) per selezionare uno strumento appropriato per produrre il dump.</span><span class="sxs-lookup"><span data-stu-id="1226b-615">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="1226b-616">Analizzare il dump</span><span class="sxs-lookup"><span data-stu-id="1226b-616">Analyze the dump</span></span>

<span data-ttu-id="1226b-617">È possibile analizzare un dump usando diversi approcci.</span><span class="sxs-lookup"><span data-stu-id="1226b-617">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="1226b-618">Per altre informazioni, vedere [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analisi di un file dump in modalità utente).</span><span class="sxs-lookup"><span data-stu-id="1226b-618">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1226b-619">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="1226b-619">Additional resources</span></span>

* <span data-ttu-id="1226b-620">[Configurazione dell'endpoint Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (include la configurazione HTTPS e il supporto SNI)</span><span class="sxs-lookup"><span data-stu-id="1226b-620">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
