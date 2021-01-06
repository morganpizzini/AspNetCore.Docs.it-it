---
title: Bundle di Hosting
author: rick-anderson
description: Informazioni su come configurare il bundle di hosting .NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: a580c70d3141177be2508a0513f612eee56dbbf9
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "93343637"
---
# <a name="the-net-core-hosting-bundle"></a><span data-ttu-id="d5656-103">Bundle di hosting .NET Core</span><span class="sxs-lookup"><span data-stu-id="d5656-103">The .NET Core Hosting Bundle</span></span>

<span data-ttu-id="d5656-104">Il bundle di hosting di .NET Core è un programma di installazione per il runtime di .NET Core e il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="d5656-104">The .NET Core Hosting bundle is an installer for the .NET Core Runtime and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="d5656-105">Il bundle consente l'esecuzione delle app ASP.NET Core con IIS.</span><span class="sxs-lookup"><span data-stu-id="d5656-105">The bundle allows ASP.NET Core apps to run with IIS.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="d5656-106">Installare il bundle di hosting .NET Core</span><span class="sxs-lookup"><span data-stu-id="d5656-106">Install the .NET Core Hosting Bundle</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d5656-107">Se il bundle di hosting viene installato prima di IIS, è necessario riparare l'installazione del bundle.</span><span class="sxs-lookup"><span data-stu-id="d5656-107">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="d5656-108">Eseguire di nuovo il programma di installazione del bundle di hosting dopo l'installazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="d5656-108">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="d5656-109">Se il bundle di hosting viene installato dopo l'installazione della versione a 64 bit (x64) di .NET Core, uno o più SDK potrebbero risultare mancanti ([Non sono stati rilevati .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="d5656-109">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="d5656-110">Per risolvere il problema, vedere <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span><span class="sxs-lookup"><span data-stu-id="d5656-110">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

## <a name="direct-download-current-version"></a><span data-ttu-id="d5656-111">Download diretto (versione corrente)</span><span class="sxs-lookup"><span data-stu-id="d5656-111">Direct download (current version)</span></span>

<span data-ttu-id="d5656-112">Scaricare il programma di installazione mediante il collegamento seguente:</span><span class="sxs-lookup"><span data-stu-id="d5656-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="d5656-113">Programma di installazione del bundle di hosting .NET Core corrente (download diretto)</span><span class="sxs-lookup"><span data-stu-id="d5656-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

## <a name="visual-c-redistributable-requirement"></a><span data-ttu-id="d5656-114">Visual C++ requisito ridistribuibile</span><span class="sxs-lookup"><span data-stu-id="d5656-114">Visual C++ Redistributable Requirement</span></span>

<span data-ttu-id="d5656-115">Nelle versioni precedenti di Windows, ad esempio Windows Server 2012 R2, installare Visual Studio C++ 2015, 2017, 2019 ridistribuibile.</span><span class="sxs-lookup"><span data-stu-id="d5656-115">On older versions of Windows, for example Windows Server 2012 R2, install the Visual Studio C++ 2015, 2017, 2019 Redistributable.</span></span> <span data-ttu-id="d5656-116">In caso contrario, un messaggio di errore confuso nel registro eventi di Windows segnala che `The data is the error.`</span><span class="sxs-lookup"><span data-stu-id="d5656-116">Otherwise, a confusing error message in the Windows Event Log reports that `The data is the error.`</span></span>

<span data-ttu-id="d5656-117">[Current x64 vs C++ Redistributable](https://aka.ms/vs/16/release/vc_redist.x64.exe) 
 [Ridistribuibile corrente x86 rispetto a C++](https://aka.ms/vs/16/release/vc_redist.x86.exe)</span><span class="sxs-lookup"><span data-stu-id="d5656-117">[Current x64 VS C++ redistributable](https://aka.ms/vs/16/release/vc_redist.x64.exe)
[Current x86 VS C++ redistributable](https://aka.ms/vs/16/release/vc_redist.x86.exe)</span></span>

## <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="d5656-118">Versioni precedenti del programma di installazione</span><span class="sxs-lookup"><span data-stu-id="d5656-118">Earlier versions of the installer</span></span>

<span data-ttu-id="d5656-119">Per ottenere una versione precedente del programma di installazione:</span><span class="sxs-lookup"><span data-stu-id="d5656-119">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="d5656-120">Passare alla pagina [download di .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .</span><span class="sxs-lookup"><span data-stu-id="d5656-120">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="d5656-121">Selezionare la versione desiderata di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d5656-121">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="d5656-122">Nella colonna **Run apps - Runtime** (App di esecuzione - Runtime), trovare la riga della versione di runtime di .NET Core desiderata.</span><span class="sxs-lookup"><span data-stu-id="d5656-122">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="d5656-123">Scaricare il programma di installazione usando il collegamento **bundle di hosting** .</span><span class="sxs-lookup"><span data-stu-id="d5656-123">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="d5656-124">Alcuni programmi di installazione contengono versioni che hanno terminato il loro ciclo di vita e non sono più supportate da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="d5656-124">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="d5656-125">Per altre informazioni, vedere i [criteri di supporto](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="d5656-125">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

## <a name="options"></a><span data-ttu-id="d5656-126">Opzioni</span><span class="sxs-lookup"><span data-stu-id="d5656-126">Options</span></span>

1. <span data-ttu-id="d5656-127">Quando si esegue il programma di installazione da una shell dei comandi di amministratore sono disponibili i parametri seguenti:</span><span class="sxs-lookup"><span data-stu-id="d5656-127">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="d5656-128">`OPT_NO_ANCM=1`: Ignorare l'installazione del modulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d5656-128">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="d5656-129">`OPT_NO_RUNTIME=1`: Ignorare l'installazione del runtime di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d5656-129">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="d5656-130">Utilizzato quando il server ospita solo le [distribuzioni autosufficienti (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="d5656-130">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="d5656-131">`OPT_NO_SHAREDFX=1`: Ignora l'installazione del Framework condiviso ASP.NET (runtime ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="d5656-131">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="d5656-132">Utilizzato quando il server ospita solo le [distribuzioni autosufficienti (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="d5656-132">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="d5656-133">`OPT_NO_X86=1`: Ignora l'installazione di Runtime x86.</span><span class="sxs-lookup"><span data-stu-id="d5656-133">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="d5656-134">Usare questo parametro se si è certi che non verrà eseguito l'hosting di app a 32 bit.</span><span class="sxs-lookup"><span data-stu-id="d5656-134">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="d5656-135">Se non esiste alcuna possibilità che in futuro venga eseguito l'hosting di app sia a 32 che a 64 bit, non usare questo parametro e installare entrambi i runtime.</span><span class="sxs-lookup"><span data-stu-id="d5656-135">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="d5656-136">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disabilitare il controllo per l'utilizzo di una configurazione condivisa di IIS quando la configurazione condivisa ( `applicationHost.config` ) si trova nello stesso computer dell'installazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="d5656-136">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="d5656-137">*Disponibile solo per i programmi di installazione di bundler di hosting ASP.NET Core 2.2 o versioni successive.*</span><span class="sxs-lookup"><span data-stu-id="d5656-137">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="d5656-138">Per altre informazioni, vedere <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d5656-138">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>

> [!NOTE]
> <span data-ttu-id="d5656-139">Per informazioni sulla configurazione condivisa di IIS, vedere [Modulo di ASP.NET Core con configurazione condivisa di IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="d5656-139">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="restart-iis"></a><span data-ttu-id="d5656-140">Riavviare IIS</span><span class="sxs-lookup"><span data-stu-id="d5656-140">Restart IIS</span></span>

<span data-ttu-id="d5656-141">Dopo l'installazione del bundle di hosting, potrebbe essere necessario un riavvio manuale di IIS.</span><span class="sxs-lookup"><span data-stu-id="d5656-141">After the Hosting Bundle is installed, a manual IIS restart may be required.</span></span> <span data-ttu-id="d5656-142">Gli strumenti dell'interfaccia della riga di comando, ad esempio, `dotnet` potrebbero non esistere nel percorso per l'esecuzione dei processi di lavoro IIS.</span><span class="sxs-lookup"><span data-stu-id="d5656-142">For example, the `dotnet` CLI tooling (command) might not exist on the PATH for running IIS worker processes.</span></span>

<span data-ttu-id="d5656-143">Per arrestare e avviare manualmente IIS, eseguire i comandi seguenti in una shell dei comandi con privilegi elevati:</span><span class="sxs-lookup"><span data-stu-id="d5656-143">To manually stop and start IIS, execute the following commands in an elevated command shell:</span></span>

```console
net stop was /y
net start w3svc
```

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="d5656-144">Versione del modulo e log del programma di installazione del bundle di hosting</span><span class="sxs-lookup"><span data-stu-id="d5656-144">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="d5656-145">Per determinare la versione del modulo ASP.NET Core installato:</span><span class="sxs-lookup"><span data-stu-id="d5656-145">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="d5656-146">Nel sistema di hosting passare a `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2` .</span><span class="sxs-lookup"><span data-stu-id="d5656-146">On the hosting system, navigate to `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span></span>
1. <span data-ttu-id="d5656-147">Individuare il `aspnetcorev2.dll` file.</span><span class="sxs-lookup"><span data-stu-id="d5656-147">Locate the `aspnetcorev2.dll` file.</span></span>
1. <span data-ttu-id="d5656-148">Fare clic con il pulsante destro del mouse sul file e scegliere **Proprietà** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="d5656-148">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="d5656-149">Selezionare la scheda **Dettagli** . La versione del **file** e la **versione del prodotto** rappresentano la versione installata del modulo.</span><span class="sxs-lookup"><span data-stu-id="d5656-149">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="d5656-150">I log del programma di installazione del bundle di hosting per il modulo sono disponibili in `C:\Users\%UserName%\AppData\Local\Temp` .</span><span class="sxs-lookup"><span data-stu-id="d5656-150">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="d5656-151">Il nome del file è `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log` , dove il segnaposto `{TIMESTAMP}` è il timestamp del file.</span><span class="sxs-lookup"><span data-stu-id="d5656-151">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp of the file.</span></span>
