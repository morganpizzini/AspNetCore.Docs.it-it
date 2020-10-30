---
title: Bundle di Hosting
author: rick-anderson
description: Informazioni su come configurare il bundle di hosting .NET Core.
monikerRange: '>= aspnetcore-5.0'
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: ecf3dd45575390eee263a275e7f1fb9ec50011bb
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058441"
---
# <a name="the-net-core-hosting-bundle"></a><span data-ttu-id="32d8e-103">Bundle di hosting .NET Core</span><span class="sxs-lookup"><span data-stu-id="32d8e-103">The .NET Core Hosting Bundle</span></span>

<span data-ttu-id="32d8e-104">Il bundle di hosting di .NET Core è un programma di installazione per il runtime di .NET Core e il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="32d8e-104">The .NET Core Hosting bundle is an installer for the .NET Core Runtime and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="32d8e-105">Il bundle consente l'esecuzione delle app ASP.NET Core con IIS.</span><span class="sxs-lookup"><span data-stu-id="32d8e-105">The bundle allows ASP.NET Core apps to run with IIS.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="32d8e-106">Installare il bundle di hosting .NET Core</span><span class="sxs-lookup"><span data-stu-id="32d8e-106">Install the .NET Core Hosting Bundle</span></span>

> [!IMPORTANT]
> <span data-ttu-id="32d8e-107">Se il bundle di hosting viene installato prima di IIS, è necessario riparare l'installazione del bundle.</span><span class="sxs-lookup"><span data-stu-id="32d8e-107">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="32d8e-108">Eseguire di nuovo il programma di installazione del bundle di hosting dopo l'installazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="32d8e-108">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="32d8e-109">Se il bundle di hosting viene installato dopo l'installazione della versione a 64 bit (x64) di .NET Core, uno o più SDK potrebbero risultare mancanti ([Non sono stati rilevati .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="32d8e-109">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="32d8e-110">Per risolvere il problema, vedere <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span><span class="sxs-lookup"><span data-stu-id="32d8e-110">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="32d8e-111">Download diretto (versione corrente)</span><span class="sxs-lookup"><span data-stu-id="32d8e-111">Direct download (current version)</span></span>

<span data-ttu-id="32d8e-112">Scaricare il programma di installazione mediante il collegamento seguente:</span><span class="sxs-lookup"><span data-stu-id="32d8e-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="32d8e-113">Programma di installazione del bundle di hosting .NET Core corrente (download diretto)</span><span class="sxs-lookup"><span data-stu-id="32d8e-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="32d8e-114">Versioni precedenti del programma di installazione</span><span class="sxs-lookup"><span data-stu-id="32d8e-114">Earlier versions of the installer</span></span>

<span data-ttu-id="32d8e-115">Per ottenere una versione precedente del programma di installazione:</span><span class="sxs-lookup"><span data-stu-id="32d8e-115">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="32d8e-116">Passare alla pagina [download di .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .</span><span class="sxs-lookup"><span data-stu-id="32d8e-116">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="32d8e-117">Selezionare la versione desiderata di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="32d8e-117">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="32d8e-118">Nella colonna **Run apps - Runtime** (App di esecuzione - Runtime), trovare la riga della versione di runtime di .NET Core desiderata.</span><span class="sxs-lookup"><span data-stu-id="32d8e-118">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="32d8e-119">Scaricare il programma di installazione usando il collegamento **bundle di hosting** .</span><span class="sxs-lookup"><span data-stu-id="32d8e-119">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="32d8e-120">Alcuni programmi di installazione contengono versioni che hanno terminato il loro ciclo di vita e non sono più supportate da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="32d8e-120">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="32d8e-121">Per altre informazioni, vedere i [criteri di supporto](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="32d8e-121">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="32d8e-122">Installare il bundle di hosting</span><span class="sxs-lookup"><span data-stu-id="32d8e-122">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="32d8e-123">Eseguire il programma di installazione nel server.</span><span class="sxs-lookup"><span data-stu-id="32d8e-123">Run the installer on the server.</span></span> <span data-ttu-id="32d8e-124">Quando si esegue il programma di installazione da una shell dei comandi di amministratore sono disponibili i parametri seguenti:</span><span class="sxs-lookup"><span data-stu-id="32d8e-124">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="32d8e-125">`OPT_NO_ANCM=1`: Ignorare l'installazione del modulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="32d8e-125">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="32d8e-126">`OPT_NO_RUNTIME=1`: Ignorare l'installazione del runtime di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="32d8e-126">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="32d8e-127">Utilizzato quando il server ospita solo le [distribuzioni autosufficienti (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="32d8e-127">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="32d8e-128">`OPT_NO_SHAREDFX=1`: Ignora l'installazione del Framework condiviso ASP.NET (runtime ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="32d8e-128">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="32d8e-129">Utilizzato quando il server ospita solo le [distribuzioni autosufficienti (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="32d8e-129">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="32d8e-130">`OPT_NO_X86=1`: Ignora l'installazione di Runtime x86.</span><span class="sxs-lookup"><span data-stu-id="32d8e-130">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="32d8e-131">Usare questo parametro se si è certi che non verrà eseguito l'hosting di app a 32 bit.</span><span class="sxs-lookup"><span data-stu-id="32d8e-131">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="32d8e-132">Se non esiste alcuna possibilità che in futuro venga eseguito l'hosting di app sia a 32 che a 64 bit, non usare questo parametro e installare entrambi i runtime.</span><span class="sxs-lookup"><span data-stu-id="32d8e-132">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="32d8e-133">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disabilitare il controllo per l'utilizzo di una configurazione condivisa di IIS quando la configurazione condivisa ( `applicationHost.config` ) si trova nello stesso computer dell'installazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="32d8e-133">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="32d8e-134">*Disponibile solo per i programmi di installazione di bundler di hosting ASP.NET Core 2.2 o versioni successive.*</span><span class="sxs-lookup"><span data-stu-id="32d8e-134">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="32d8e-135">Per altre informazioni, vedere <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="32d8e-135">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="32d8e-136">Riavviare il sistema o eseguire i comandi seguenti in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="32d8e-136">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="32d8e-137">Il riavvio di IIS rileva una modifica alla variabile di ambiente di sistema PATH apportata dal programma di installazione.</span><span class="sxs-lookup"><span data-stu-id="32d8e-137">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="32d8e-138">ASP.NET Core non adotta il comportamento di rollforward per le versioni di patch dei pacchetti di Framework condivisi.</span><span class="sxs-lookup"><span data-stu-id="32d8e-138">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="32d8e-139">Dopo l'aggiornamento del Framework condiviso mediante l'installazione di un nuovo bundle di hosting, riavviare il sistema o eseguire i comandi seguenti in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="32d8e-139">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="32d8e-140">Per informazioni sulla configurazione condivisa di IIS, vedere [Modulo di ASP.NET Core con configurazione condivisa di IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="32d8e-140">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="32d8e-141">Versione del modulo e log del programma di installazione del bundle di hosting</span><span class="sxs-lookup"><span data-stu-id="32d8e-141">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="32d8e-142">Per determinare la versione del modulo ASP.NET Core installato:</span><span class="sxs-lookup"><span data-stu-id="32d8e-142">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="32d8e-143">Nel sistema di hosting passare a `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2` .</span><span class="sxs-lookup"><span data-stu-id="32d8e-143">On the hosting system, navigate to `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span></span>
1. <span data-ttu-id="32d8e-144">Individuare il `aspnetcorev2.dll` file.</span><span class="sxs-lookup"><span data-stu-id="32d8e-144">Locate the `aspnetcorev2.dll` file.</span></span>
1. <span data-ttu-id="32d8e-145">Fare clic con il pulsante destro del mouse sul file e scegliere **Proprietà** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="32d8e-145">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="32d8e-146">Selezionare la scheda **Dettagli** . La versione del **file** e la **versione del prodotto** rappresentano la versione installata del modulo.</span><span class="sxs-lookup"><span data-stu-id="32d8e-146">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="32d8e-147">I log del programma di installazione del bundle di hosting per il modulo sono disponibili in `C:\Users\%UserName%\AppData\Local\Temp` .</span><span class="sxs-lookup"><span data-stu-id="32d8e-147">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="32d8e-148">Il nome del file è `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log` , dove il segnaposto `{TIMESTAMP}` è il timestamp del file.</span><span class="sxs-lookup"><span data-stu-id="32d8e-148">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp of the file.</span></span>
