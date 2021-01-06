---
title: Supporto di IIS in fase di sviluppo in Visual Studio per ASP.NET Core
author: rick-anderson
description: Informazioni sul supporto del debug di app ASP.NET Core durante l'esecuzione con IIS in Windows Server.
monikerRange: '>= aspnetcore-2.1'
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
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: ab892b2cdfa61378ac7328c0380c8a6cffc6d376
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058454"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="3e43b-103">Supporto di IIS in fase di sviluppo in Visual Studio per ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3e43b-103">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="3e43b-104">Di [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="3e43b-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3e43b-105">Questo articolo descrive il supporto del debug di app ASP.NET Core in [Visual Studio](https://visualstudio.microsoft.com) durante l'esecuzione con IIS in Windows Server.</span><span class="sxs-lookup"><span data-stu-id="3e43b-105">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="3e43b-106">Questo argomento illustra nel dettaglio l'abilitazione di questo scenario e la configurazione di un progetto.</span><span class="sxs-lookup"><span data-stu-id="3e43b-106">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3e43b-107">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="3e43b-107">Prerequisites</span></span>

* [<span data-ttu-id="3e43b-108">Visual Studio per Windows</span><span class="sxs-lookup"><span data-stu-id="3e43b-108">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="3e43b-109">**ASP.NET e carico di lavoro di sviluppo Web**</span><span class="sxs-lookup"><span data-stu-id="3e43b-109">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="3e43b-110">Carico di lavoro di **sviluppo multipiattaforma .NET Core**</span><span class="sxs-lookup"><span data-stu-id="3e43b-110">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="3e43b-111">Certificato di sicurezza X.509 (per il supporto di HTTPS)</span><span class="sxs-lookup"><span data-stu-id="3e43b-111">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="3e43b-112">Abilitare IIS</span><span class="sxs-lookup"><span data-stu-id="3e43b-112">Enable IIS</span></span>

1. <span data-ttu-id="3e43b-113">In Windows passare a **Pannello di controllo** > **Programmi** > **Programmi e funzionalità** > **Attiva o disattiva funzionalità di Windows** (sul lato sinistro dello schermo).</span><span class="sxs-lookup"><span data-stu-id="3e43b-113">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="3e43b-114">Selezionare la casella di controllo **Internet Information Services**.</span><span class="sxs-lookup"><span data-stu-id="3e43b-114">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="3e43b-115">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="3e43b-115">Select **OK**.</span></span>

<span data-ttu-id="3e43b-116">L'installazione di IIS potrebbe richiedere un riavvio del sistema.</span><span class="sxs-lookup"><span data-stu-id="3e43b-116">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="3e43b-117">Configura IIS</span><span class="sxs-lookup"><span data-stu-id="3e43b-117">Configure IIS</span></span>

<span data-ttu-id="3e43b-118">IIS deve disporre di un sito Web configurato con gli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="3e43b-118">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="3e43b-119">**Nome host**: in genere, il **sito Web predefinito** viene utilizzato con il **nome host** `localhost` .</span><span class="sxs-lookup"><span data-stu-id="3e43b-119">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="3e43b-120">Tuttavia, è appropriato qualsiasi sito Web IIS valido con un nome host univoco.</span><span class="sxs-lookup"><span data-stu-id="3e43b-120">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="3e43b-121">**Binding del sito**</span><span class="sxs-lookup"><span data-stu-id="3e43b-121">**Site Binding**</span></span>
  * <span data-ttu-id="3e43b-122">Per le app che richiedono HTTPS, creare un binding alla porta 443 con un certificato.</span><span class="sxs-lookup"><span data-stu-id="3e43b-122">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="3e43b-123">Viene in genere usato il **certificato di sviluppo di IIS Express**, ma può essere usato qualsiasi certificato valido.</span><span class="sxs-lookup"><span data-stu-id="3e43b-123">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="3e43b-124">Per le app che usano HTTP, verificare l'esistenza di un binding alla porta 80 o creare un binding alla porta 80 per un nuovo sito.</span><span class="sxs-lookup"><span data-stu-id="3e43b-124">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="3e43b-125">Usare un singolo binding per HTTP o HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3e43b-125">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="3e43b-126">**Non è supportato il binding a porte HTTP e HTTPS in contemporanea.**</span><span class="sxs-lookup"><span data-stu-id="3e43b-126">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="3e43b-127">Abilitare il supporto di IIS in fase di sviluppo in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e43b-127">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="3e43b-128">Avviare il programma di installazione di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3e43b-128">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="3e43b-129">Selezionare **Modifica** per l'installazione di Visual Studio che si intende usare per il supporto di IIS in fase di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="3e43b-129">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="3e43b-130">Per il carico di lavoro **Sviluppo ASP.NET e Web**, individuare e installare il componente **Supporto IIS in fase di sviluppo**.</span><span class="sxs-lookup"><span data-stu-id="3e43b-130">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="3e43b-131">Il componente è elencato nella sezione **Facoltativo** in **Supporto IIS in fase di sviluppo** nel pannello **Dettagli di installazione** a destra dei carichi di lavoro.</span><span class="sxs-lookup"><span data-stu-id="3e43b-131">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="3e43b-132">Il componente installa il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), un modulo IIS nativo necessario per l'esecuzione delle applicazioni ASP.NET Core con IIS.</span><span class="sxs-lookup"><span data-stu-id="3e43b-132">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="3e43b-133">Configurare il progetto</span><span class="sxs-lookup"><span data-stu-id="3e43b-133">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="3e43b-134">Reindirizzamento HTTPS</span><span class="sxs-lookup"><span data-stu-id="3e43b-134">HTTPS redirection</span></span>

<span data-ttu-id="3e43b-135">Per un nuovo progetto che richiede HTTPS, selezionare la casella di controllo **Configura per HTTPS** nella finestra **Crea una nuova applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="3e43b-135">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="3e43b-136">Selezionando questa casella di controllo viene aggiunto il [middleware di reindirizzamento HTTPS e HSTS](xref:security/enforcing-ssl) all'app quando viene creata.</span><span class="sxs-lookup"><span data-stu-id="3e43b-136">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="3e43b-137">Per un progetto esistente che richiede HTTPS, usare il middleware di reindirizzamento HTTPS e HSTS in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="3e43b-137">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="3e43b-138">Per altre informazioni, vedere <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="3e43b-138">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="3e43b-139">Per un progetto che usa HTTP, il [middleware di reindirizzamento HTTPS e HSTS](xref:security/enforcing-ssl) non viene aggiunto all'app.</span><span class="sxs-lookup"><span data-stu-id="3e43b-139">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="3e43b-140">Non è richiesta alcuna configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="3e43b-140">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="3e43b-141">Profilo di avvio IIS</span><span class="sxs-lookup"><span data-stu-id="3e43b-141">IIS launch profile</span></span>

<span data-ttu-id="3e43b-142">Creare un nuovo profilo di avvio per aggiungere il supporto IIS in fase di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="3e43b-142">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="3e43b-143">Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni**.</span><span class="sxs-lookup"><span data-stu-id="3e43b-143">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="3e43b-144">Selezionare **Proprietà**.</span><span class="sxs-lookup"><span data-stu-id="3e43b-144">Select **Properties**.</span></span> <span data-ttu-id="3e43b-145">Aprire la scheda **debug** .</span><span class="sxs-lookup"><span data-stu-id="3e43b-145">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="3e43b-146">Per **Profilo** selezionare il pulsante **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="3e43b-146">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="3e43b-147">Denominare il profilo "IIS" nella finestra popup.</span><span class="sxs-lookup"><span data-stu-id="3e43b-147">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="3e43b-148">Selezionare **OK** per creare il profilo.</span><span class="sxs-lookup"><span data-stu-id="3e43b-148">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="3e43b-149">Per l'impostazione **Avvio** selezionare **IIS** dall'elenco.</span><span class="sxs-lookup"><span data-stu-id="3e43b-149">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="3e43b-150">Selezionare la casella di controllo **Avvia browser** e specificare l'URL dell'endpoint.</span><span class="sxs-lookup"><span data-stu-id="3e43b-150">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="3e43b-151">Quando l'app richiede HTTPS, usare un endpoint HTTPS (`https://`).</span><span class="sxs-lookup"><span data-stu-id="3e43b-151">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="3e43b-152">Per il protocollo HTTP, usare un endpoint HTTP (`http://`).</span><span class="sxs-lookup"><span data-stu-id="3e43b-152">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="3e43b-153">Specificare lo stesso nome host e la stessa porta usati nella [configurazione di IIS specificata in precedenza](#configure-iis), in genere `localhost`.</span><span class="sxs-lookup"><span data-stu-id="3e43b-153">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="3e43b-154">Specificare il nome dell'app alla fine dell'URL.</span><span class="sxs-lookup"><span data-stu-id="3e43b-154">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="3e43b-155">Ad esempio, `https://localhost/WebApplication1` (HTTPS) o `http://localhost/WebApplication1` (HTTP) sono URL di endpoint validi.</span><span class="sxs-lookup"><span data-stu-id="3e43b-155">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="3e43b-156">Nella sezione **Variabili di ambiente** selezionare il pulsante **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="3e43b-156">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="3e43b-157">Specificare una variabile di ambiente con **Nome**`ASPNETCORE_ENVIRONMENT` e **Valore**`Development`.</span><span class="sxs-lookup"><span data-stu-id="3e43b-157">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="3e43b-158">Nell'area **Impostazioni server Web** impostare **URL app** sullo stesso valore usato per l'URL dell'endpoint **Avvia browser**.</span><span class="sxs-lookup"><span data-stu-id="3e43b-158">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="3e43b-159">Per l'impostazione **Modello di hosting** in Visual Studio 2019 o versione successiva, selezionare **Predefinito** per usare il modello di hosting usato dal progetto.</span><span class="sxs-lookup"><span data-stu-id="3e43b-159">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="3e43b-160">Se il progetto imposta la proprietà `<AspNetCoreHostingModel>` nel file di progetto, viene usato il valore della proprietà (`InProcess` o `OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="3e43b-160">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="3e43b-161">Se la proprietà non è presente, viene usato il modello di hosting predefinito dell'app, ovvero in-process.</span><span class="sxs-lookup"><span data-stu-id="3e43b-161">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="3e43b-162">Se l'app richiede l'impostazione esplicita di un modello di hosting diverso dal modello di hosting normale dell'app, impostare **Modello di hosting** su `In Process` o `Out Of Process` in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="3e43b-162">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="3e43b-163">Salvare il profilo.</span><span class="sxs-lookup"><span data-stu-id="3e43b-163">Save the profile.</span></span>

<span data-ttu-id="3e43b-164">Se non si usa Visual Studio, aggiungere manualmente un profilo di avvio al file [launchSettings.json](https://json.schemastore.org/launchsettings) nella cartella *Proprietà*.</span><span class="sxs-lookup"><span data-stu-id="3e43b-164">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="3e43b-165">L'esempio seguente configura il profilo per usare il protocollo HTTPS:</span><span class="sxs-lookup"><span data-stu-id="3e43b-165">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="3e43b-166">Verificare che gli endpoint `applicationUrl` e `launchUrl` corrispondano e usare lo stesso protocollo della configurazione del binding IIS, ovvero HTTP o HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3e43b-166">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="3e43b-167">Eseguire il progetto</span><span class="sxs-lookup"><span data-stu-id="3e43b-167">Run the project</span></span>

<span data-ttu-id="3e43b-168">Eseguire Visual Studio come amministratore:</span><span class="sxs-lookup"><span data-stu-id="3e43b-168">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="3e43b-169">Verificare che l'elenco di riepilogo a discesa della configurazione della build sia impostato su **Debug**.</span><span class="sxs-lookup"><span data-stu-id="3e43b-169">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="3e43b-170">Impostare il [pulsante Avvia debug](/visualstudio/debugger/debugger-feature-tour) sul profilo **IIS** e selezionare il pulsante per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="3e43b-170">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="3e43b-171">Visual Studio potrebbe richiedere un riavvio, se non si è in modalità amministratore.</span><span class="sxs-lookup"><span data-stu-id="3e43b-171">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="3e43b-172">In tal caso riavviare Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3e43b-172">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="3e43b-173">Se viene usato un certificato di sviluppo non attendibile, il browser potrebbe richiedere di creare un'eccezione per il certificato non attendibile.</span><span class="sxs-lookup"><span data-stu-id="3e43b-173">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="3e43b-174">Il debug della configurazione della build Versione con [Just My Code](/visualstudio/debugger/just-my-code) e le ottimizzazioni del compilatore genera un'esperienza con funzionalità ridotta.</span><span class="sxs-lookup"><span data-stu-id="3e43b-174">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="3e43b-175">Ad esempio, i punti di interruzione non vengono raggiunti.</span><span class="sxs-lookup"><span data-stu-id="3e43b-175">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3e43b-176">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="3e43b-176">Additional resources</span></span>

* <span data-ttu-id="3e43b-177">[Getting Started with the IIS Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8) (Introduzione a Gestione IIS in IIS)</span><span class="sxs-lookup"><span data-stu-id="3e43b-177">[Getting Started with the IIS Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)</span></span>
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3e43b-178">Questo articolo descrive il supporto del debug di app ASP.NET Core in [Visual Studio](https://visualstudio.microsoft.com) durante l'esecuzione con IIS in Windows Server.</span><span class="sxs-lookup"><span data-stu-id="3e43b-178">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="3e43b-179">Questo argomento illustra nel dettaglio l'abilitazione di questo scenario e la configurazione di un progetto.</span><span class="sxs-lookup"><span data-stu-id="3e43b-179">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3e43b-180">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="3e43b-180">Prerequisites</span></span>

* [<span data-ttu-id="3e43b-181">Visual Studio per Windows</span><span class="sxs-lookup"><span data-stu-id="3e43b-181">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="3e43b-182">**ASP.NET e carico di lavoro di sviluppo Web**</span><span class="sxs-lookup"><span data-stu-id="3e43b-182">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="3e43b-183">Carico di lavoro di **sviluppo multipiattaforma .NET Core**</span><span class="sxs-lookup"><span data-stu-id="3e43b-183">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="3e43b-184">Certificato di sicurezza X.509 (per il supporto di HTTPS)</span><span class="sxs-lookup"><span data-stu-id="3e43b-184">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="3e43b-185">Abilitare IIS</span><span class="sxs-lookup"><span data-stu-id="3e43b-185">Enable IIS</span></span>

1. <span data-ttu-id="3e43b-186">In Windows passare a **Pannello di controllo** > **Programmi** > **Programmi e funzionalità** > **Attiva o disattiva funzionalità di Windows** (sul lato sinistro dello schermo).</span><span class="sxs-lookup"><span data-stu-id="3e43b-186">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="3e43b-187">Selezionare la casella di controllo **Internet Information Services**.</span><span class="sxs-lookup"><span data-stu-id="3e43b-187">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="3e43b-188">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="3e43b-188">Select **OK**.</span></span>

<span data-ttu-id="3e43b-189">L'installazione di IIS potrebbe richiedere un riavvio del sistema.</span><span class="sxs-lookup"><span data-stu-id="3e43b-189">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="3e43b-190">Configura IIS</span><span class="sxs-lookup"><span data-stu-id="3e43b-190">Configure IIS</span></span>

<span data-ttu-id="3e43b-191">IIS deve disporre di un sito Web configurato con gli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="3e43b-191">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="3e43b-192">**Nome host**: in genere, il **sito Web predefinito** viene utilizzato con il **nome host** `localhost` .</span><span class="sxs-lookup"><span data-stu-id="3e43b-192">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="3e43b-193">Tuttavia, è appropriato qualsiasi sito Web IIS valido con un nome host univoco.</span><span class="sxs-lookup"><span data-stu-id="3e43b-193">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="3e43b-194">**Binding del sito**</span><span class="sxs-lookup"><span data-stu-id="3e43b-194">**Site Binding**</span></span>
  * <span data-ttu-id="3e43b-195">Per le app che richiedono HTTPS, creare un binding alla porta 443 con un certificato.</span><span class="sxs-lookup"><span data-stu-id="3e43b-195">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="3e43b-196">Viene in genere usato il **certificato di sviluppo di IIS Express**, ma può essere usato qualsiasi certificato valido.</span><span class="sxs-lookup"><span data-stu-id="3e43b-196">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="3e43b-197">Per le app che usano HTTP, verificare l'esistenza di un binding alla porta 80 o creare un binding alla porta 80 per un nuovo sito.</span><span class="sxs-lookup"><span data-stu-id="3e43b-197">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="3e43b-198">Usare un singolo binding per HTTP o HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3e43b-198">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="3e43b-199">**Non è supportato il binding a porte HTTP e HTTPS in contemporanea.**</span><span class="sxs-lookup"><span data-stu-id="3e43b-199">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="3e43b-200">Abilitare il supporto di IIS in fase di sviluppo in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e43b-200">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="3e43b-201">Avviare il programma di installazione di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3e43b-201">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="3e43b-202">Selezionare **Modifica** per l'installazione di Visual Studio che si intende usare per il supporto di IIS in fase di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="3e43b-202">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="3e43b-203">Per il carico di lavoro **Sviluppo ASP.NET e Web**, individuare e installare il componente **Supporto IIS in fase di sviluppo**.</span><span class="sxs-lookup"><span data-stu-id="3e43b-203">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="3e43b-204">Il componente è elencato nella sezione **Facoltativo** in **Supporto IIS in fase di sviluppo** nel pannello **Dettagli di installazione** a destra dei carichi di lavoro.</span><span class="sxs-lookup"><span data-stu-id="3e43b-204">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="3e43b-205">Il componente installa il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), un modulo IIS nativo necessario per l'esecuzione delle applicazioni ASP.NET Core con IIS.</span><span class="sxs-lookup"><span data-stu-id="3e43b-205">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="3e43b-206">Configurare il progetto</span><span class="sxs-lookup"><span data-stu-id="3e43b-206">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="3e43b-207">Reindirizzamento HTTPS</span><span class="sxs-lookup"><span data-stu-id="3e43b-207">HTTPS redirection</span></span>

<span data-ttu-id="3e43b-208">Per un nuovo progetto che richiede HTTPS, selezionare la casella di controllo **Configura per HTTPS** nella finestra **Crea una nuova applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="3e43b-208">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="3e43b-209">Selezionando questa casella di controllo viene aggiunto il [middleware di reindirizzamento HTTPS e HSTS](xref:security/enforcing-ssl) all'app quando viene creata.</span><span class="sxs-lookup"><span data-stu-id="3e43b-209">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="3e43b-210">Per un progetto esistente che richiede HTTPS, usare il middleware di reindirizzamento HTTPS e HSTS in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="3e43b-210">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="3e43b-211">Per altre informazioni, vedere <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="3e43b-211">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="3e43b-212">Per un progetto che usa HTTP, il [middleware di reindirizzamento HTTPS e HSTS](xref:security/enforcing-ssl) non viene aggiunto all'app.</span><span class="sxs-lookup"><span data-stu-id="3e43b-212">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="3e43b-213">Non è richiesta alcuna configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="3e43b-213">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="3e43b-214">Profilo di avvio IIS</span><span class="sxs-lookup"><span data-stu-id="3e43b-214">IIS launch profile</span></span>

<span data-ttu-id="3e43b-215">Creare un nuovo profilo di avvio per aggiungere il supporto IIS in fase di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="3e43b-215">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="3e43b-216">Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni**.</span><span class="sxs-lookup"><span data-stu-id="3e43b-216">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="3e43b-217">Selezionare **Proprietà**.</span><span class="sxs-lookup"><span data-stu-id="3e43b-217">Select **Properties**.</span></span> <span data-ttu-id="3e43b-218">Aprire la scheda **debug** .</span><span class="sxs-lookup"><span data-stu-id="3e43b-218">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="3e43b-219">Per **Profilo** selezionare il pulsante **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="3e43b-219">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="3e43b-220">Denominare il profilo "IIS" nella finestra popup.</span><span class="sxs-lookup"><span data-stu-id="3e43b-220">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="3e43b-221">Selezionare **OK** per creare il profilo.</span><span class="sxs-lookup"><span data-stu-id="3e43b-221">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="3e43b-222">Per l'impostazione **Avvio** selezionare **IIS** dall'elenco.</span><span class="sxs-lookup"><span data-stu-id="3e43b-222">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="3e43b-223">Selezionare la casella di controllo **Avvia browser** e specificare l'URL dell'endpoint.</span><span class="sxs-lookup"><span data-stu-id="3e43b-223">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="3e43b-224">Quando l'app richiede HTTPS, usare un endpoint HTTPS (`https://`).</span><span class="sxs-lookup"><span data-stu-id="3e43b-224">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="3e43b-225">Per il protocollo HTTP, usare un endpoint HTTP (`http://`).</span><span class="sxs-lookup"><span data-stu-id="3e43b-225">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="3e43b-226">Specificare lo stesso nome host e la stessa porta usati nella [configurazione di IIS specificata in precedenza](#configure-iis), in genere `localhost`.</span><span class="sxs-lookup"><span data-stu-id="3e43b-226">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="3e43b-227">Specificare il nome dell'app alla fine dell'URL.</span><span class="sxs-lookup"><span data-stu-id="3e43b-227">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="3e43b-228">Ad esempio, `https://localhost/WebApplication1` (HTTPS) o `http://localhost/WebApplication1` (HTTP) sono URL di endpoint validi.</span><span class="sxs-lookup"><span data-stu-id="3e43b-228">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="3e43b-229">Nella sezione **Variabili di ambiente** selezionare il pulsante **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="3e43b-229">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="3e43b-230">Specificare una variabile di ambiente con **Nome**`ASPNETCORE_ENVIRONMENT` e **Valore**`Development`.</span><span class="sxs-lookup"><span data-stu-id="3e43b-230">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="3e43b-231">Nell'area **Impostazioni server Web** impostare **URL app** sullo stesso valore usato per l'URL dell'endpoint **Avvia browser**.</span><span class="sxs-lookup"><span data-stu-id="3e43b-231">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="3e43b-232">Per l'impostazione **Modello di hosting** in Visual Studio 2019 o versione successiva, selezionare **Predefinito** per usare il modello di hosting usato dal progetto.</span><span class="sxs-lookup"><span data-stu-id="3e43b-232">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="3e43b-233">Se il progetto imposta la proprietà `<AspNetCoreHostingModel>` nel file di progetto, viene usato il valore della proprietà (`InProcess` o `OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="3e43b-233">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="3e43b-234">Se la proprietà non è presente, viene usato il modello di hosting predefinito dell'app, ovvero out-of-process.</span><span class="sxs-lookup"><span data-stu-id="3e43b-234">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="3e43b-235">Se l'app richiede l'impostazione esplicita di un modello di hosting diverso dal modello di hosting normale dell'app, impostare **Modello di hosting** su `In Process` o `Out Of Process` in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="3e43b-235">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="3e43b-236">Salvare il profilo.</span><span class="sxs-lookup"><span data-stu-id="3e43b-236">Save the profile.</span></span>

<span data-ttu-id="3e43b-237">Se non si usa Visual Studio, aggiungere manualmente un profilo di avvio al file [launchSettings.json](https://json.schemastore.org/launchsettings) nella cartella *Proprietà*.</span><span class="sxs-lookup"><span data-stu-id="3e43b-237">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="3e43b-238">L'esempio seguente configura il profilo per usare il protocollo HTTPS:</span><span class="sxs-lookup"><span data-stu-id="3e43b-238">The following example configures the profile to use the HTTPS protocol:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="3e43b-239">Verificare che gli endpoint `applicationUrl` e `launchUrl` corrispondano e usare lo stesso protocollo della configurazione del binding IIS, ovvero HTTP o HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3e43b-239">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="3e43b-240">Eseguire il progetto</span><span class="sxs-lookup"><span data-stu-id="3e43b-240">Run the project</span></span>

<span data-ttu-id="3e43b-241">Eseguire Visual Studio come amministratore:</span><span class="sxs-lookup"><span data-stu-id="3e43b-241">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="3e43b-242">Verificare che l'elenco di riepilogo a discesa della configurazione della build sia impostato su **Debug**.</span><span class="sxs-lookup"><span data-stu-id="3e43b-242">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="3e43b-243">Impostare il [pulsante Avvia debug](/visualstudio/debugger/debugger-feature-tour) sul profilo **IIS** e selezionare il pulsante per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="3e43b-243">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="3e43b-244">Visual Studio potrebbe richiedere un riavvio, se non si è in modalità amministratore.</span><span class="sxs-lookup"><span data-stu-id="3e43b-244">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="3e43b-245">In tal caso riavviare Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3e43b-245">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="3e43b-246">Se viene usato un certificato di sviluppo non attendibile, il browser potrebbe richiedere di creare un'eccezione per il certificato non attendibile.</span><span class="sxs-lookup"><span data-stu-id="3e43b-246">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="3e43b-247">Il debug della configurazione della build Versione con [Just My Code](/visualstudio/debugger/just-my-code) e le ottimizzazioni del compilatore genera un'esperienza con funzionalità ridotta.</span><span class="sxs-lookup"><span data-stu-id="3e43b-247">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="3e43b-248">Ad esempio, i punti di interruzione non vengono raggiunti.</span><span class="sxs-lookup"><span data-stu-id="3e43b-248">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3e43b-249">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="3e43b-249">Additional resources</span></span>

* <span data-ttu-id="3e43b-250">[Getting Started with the IIS Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8) (Introduzione a Gestione IIS in IIS)</span><span class="sxs-lookup"><span data-stu-id="3e43b-250">[Getting Started with the IIS Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)</span></span>
* <xref:security/enforcing-ssl>

::: moniker-end
