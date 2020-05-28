---
<span data-ttu-id="9e3bf-101">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="9e3bf-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9e3bf-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9e3bf-102">'Blazor'</span></span>
- <span data-ttu-id="9e3bf-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9e3bf-103">'Identity'</span></span>
- <span data-ttu-id="9e3bf-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9e3bf-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="9e3bf-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9e3bf-105">'Razor'</span></span>
- <span data-ttu-id="9e3bf-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="9e3bf-106">'SignalR' uid:</span></span> 

---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="9e3bf-107">Supporto di IIS in fase di sviluppo in Visual Studio per ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9e3bf-107">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="9e3bf-108">Di [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="9e3bf-108">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9e3bf-109">Questo articolo descrive il supporto del debug di app ASP.NET Core in [Visual Studio](https://visualstudio.microsoft.com) durante l'esecuzione con IIS in Windows Server.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-109">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="9e3bf-110">Questo argomento illustra nel dettaglio l'abilitazione di questo scenario e la configurazione di un progetto.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-110">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9e3bf-111">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="9e3bf-111">Prerequisites</span></span>

* [<span data-ttu-id="9e3bf-112">Visual Studio per Windows</span><span class="sxs-lookup"><span data-stu-id="9e3bf-112">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="9e3bf-113">**ASP.NET e carico di lavoro di sviluppo Web**</span><span class="sxs-lookup"><span data-stu-id="9e3bf-113">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="9e3bf-114">Carico di lavoro di **sviluppo multipiattaforma .NET Core**</span><span class="sxs-lookup"><span data-stu-id="9e3bf-114">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="9e3bf-115">Certificato di sicurezza X.509 (per il supporto di HTTPS)</span><span class="sxs-lookup"><span data-stu-id="9e3bf-115">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="9e3bf-116">Abilitare IIS</span><span class="sxs-lookup"><span data-stu-id="9e3bf-116">Enable IIS</span></span>

1. <span data-ttu-id="9e3bf-117">In Windows passare a **Pannello di controllo** > **Programmi** > **Programmi e funzionalità** > **Attiva o disattiva funzionalità di Windows** (sul lato sinistro dello schermo).</span><span class="sxs-lookup"><span data-stu-id="9e3bf-117">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="9e3bf-118">Selezionare la casella di controllo **Internet Information Services**.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-118">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="9e3bf-119">Seleziona **OK**.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-119">Select **OK**.</span></span>

<span data-ttu-id="9e3bf-120">L'installazione di IIS potrebbe richiedere un riavvio del sistema.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-120">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="9e3bf-121">Configura IIS</span><span class="sxs-lookup"><span data-stu-id="9e3bf-121">Configure IIS</span></span>

<span data-ttu-id="9e3bf-122">IIS deve disporre di un sito Web configurato con gli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="9e3bf-122">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="9e3bf-123">**Nome host**: in genere, il **sito Web predefinito** viene utilizzato con il **nome host** `localhost` .</span><span class="sxs-lookup"><span data-stu-id="9e3bf-123">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="9e3bf-124">Tuttavia, è appropriato qualsiasi sito Web IIS valido con un nome host univoco.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-124">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="9e3bf-125">**Binding del sito**</span><span class="sxs-lookup"><span data-stu-id="9e3bf-125">**Site Binding**</span></span>
  * <span data-ttu-id="9e3bf-126">Per le app che richiedono HTTPS, creare un binding alla porta 443 con un certificato.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-126">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="9e3bf-127">Viene in genere usato il **certificato di sviluppo di IIS Express**, ma può essere usato qualsiasi certificato valido.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-127">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="9e3bf-128">Per le app che usano HTTP, verificare l'esistenza di un binding alla porta 80 o creare un binding alla porta 80 per un nuovo sito.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-128">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="9e3bf-129">Usare un singolo binding per HTTP o HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-129">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="9e3bf-130">**Non è supportato il binding a porte HTTP e HTTPS in contemporanea.**</span><span class="sxs-lookup"><span data-stu-id="9e3bf-130">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="9e3bf-131">Abilitare il supporto di IIS in fase di sviluppo in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9e3bf-131">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="9e3bf-132">Avviare il programma di installazione di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-132">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="9e3bf-133">Selezionare **Modifica** per l'installazione di Visual Studio che si intende usare per il supporto di IIS in fase di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-133">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="9e3bf-134">Per il carico di lavoro **Sviluppo ASP.NET e Web**, individuare e installare il componente **Supporto IIS in fase di sviluppo**.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-134">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="9e3bf-135">Il componente è elencato nella sezione **Facoltativo** in **Supporto IIS in fase di sviluppo** nel pannello **Dettagli di installazione** a destra dei carichi di lavoro.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-135">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="9e3bf-136">Il componente installa il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), un modulo IIS nativo necessario per l'esecuzione delle applicazioni ASP.NET Core con IIS.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-136">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="9e3bf-137">Configurare il progetto</span><span class="sxs-lookup"><span data-stu-id="9e3bf-137">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="9e3bf-138">Reindirizzamento HTTPS</span><span class="sxs-lookup"><span data-stu-id="9e3bf-138">HTTPS redirection</span></span>

<span data-ttu-id="9e3bf-139">Per un nuovo progetto che richiede HTTPS, selezionare la casella di controllo **Configura per HTTPS** nella finestra **Crea una nuova applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-139">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="9e3bf-140">Selezionando questa casella di controllo viene aggiunto il [middleware di reindirizzamento HTTPS e HSTS](xref:security/enforcing-ssl) all'app quando viene creata.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-140">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="9e3bf-141">Per un progetto esistente che richiede HTTPS, usare il middleware di reindirizzamento HTTPS e HSTS in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-141">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="9e3bf-142">Per altre informazioni, vedere <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-142">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="9e3bf-143">Per un progetto che usa HTTP, il [middleware di reindirizzamento HTTPS e HSTS](xref:security/enforcing-ssl) non viene aggiunto all'app.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-143">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="9e3bf-144">Non è richiesta alcuna configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-144">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="9e3bf-145">Profilo di avvio IIS</span><span class="sxs-lookup"><span data-stu-id="9e3bf-145">IIS launch profile</span></span>

<span data-ttu-id="9e3bf-146">Creare un nuovo profilo di avvio per aggiungere il supporto IIS in fase di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="9e3bf-146">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="9e3bf-147">Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni**.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-147">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="9e3bf-148">Selezionare **Proprietà**.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-148">Select **Properties**.</span></span> <span data-ttu-id="9e3bf-149">Aprire la scheda **debug** .</span><span class="sxs-lookup"><span data-stu-id="9e3bf-149">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="9e3bf-150">Per **Profilo** selezionare il pulsante **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-150">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="9e3bf-151">Denominare il profilo "IIS" nella finestra popup.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-151">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="9e3bf-152">Selezionare **OK** per creare il profilo.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-152">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="9e3bf-153">Per l'impostazione **Avvio** selezionare **IIS** dall'elenco.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-153">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="9e3bf-154">Selezionare la casella di controllo **Avvia browser** e specificare l'URL dell'endpoint.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-154">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="9e3bf-155">Quando l'app richiede HTTPS, usare un endpoint HTTPS (`https://`).</span><span class="sxs-lookup"><span data-stu-id="9e3bf-155">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="9e3bf-156">Per il protocollo HTTP, usare un endpoint HTTP (`http://`).</span><span class="sxs-lookup"><span data-stu-id="9e3bf-156">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="9e3bf-157">Specificare lo stesso nome host e la stessa porta usati nella [configurazione di IIS specificata in precedenza](#configure-iis), in genere `localhost`.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-157">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="9e3bf-158">Specificare il nome dell'app alla fine dell'URL.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-158">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="9e3bf-159">Ad esempio, `https://localhost/WebApplication1` (HTTPS) o `http://localhost/WebApplication1` (HTTP) sono URL di endpoint validi.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-159">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="9e3bf-160">Nella sezione **Variabili di ambiente** selezionare il pulsante **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-160">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="9e3bf-161">Specificare una variabile di ambiente con **Nome**`ASPNETCORE_ENVIRONMENT` e **Valore**`Development`.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-161">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="9e3bf-162">Nell'area **Impostazioni server Web** impostare **URL app** sullo stesso valore usato per l'URL dell'endpoint **Avvia browser**.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-162">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="9e3bf-163">Per l'impostazione **Modello di hosting** in Visual Studio 2019 o versione successiva, selezionare **Predefinito** per usare il modello di hosting usato dal progetto.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-163">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="9e3bf-164">Se il progetto imposta la proprietà `<AspNetCoreHostingModel>` nel file di progetto, viene usato il valore della proprietà (`InProcess` o `OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="9e3bf-164">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="9e3bf-165">Se la proprietà non è presente, viene usato il modello di hosting predefinito dell'app, ovvero in-process.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-165">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="9e3bf-166">Se l'app richiede l'impostazione esplicita di un modello di hosting diverso dal modello di hosting normale dell'app, impostare **Modello di hosting** su `In Process` o `Out Of Process` in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-166">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="9e3bf-167">Salvare il profilo.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-167">Save the profile.</span></span>

<span data-ttu-id="9e3bf-168">Se non si usa Visual Studio, aggiungere manualmente un profilo di avvio al file [launchSettings.json](https://json.schemastore.org/launchsettings) nella cartella *Proprietà*.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-168">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="9e3bf-169">L'esempio seguente configura il profilo per usare il protocollo HTTPS:</span><span class="sxs-lookup"><span data-stu-id="9e3bf-169">The following example configures the profile to use the HTTPS protocol:</span></span>

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

<span data-ttu-id="9e3bf-170">Verificare che gli endpoint `applicationUrl` e `launchUrl` corrispondano e usare lo stesso protocollo della configurazione del binding IIS, ovvero HTTP o HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-170">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="9e3bf-171">Eseguire il progetto</span><span class="sxs-lookup"><span data-stu-id="9e3bf-171">Run the project</span></span>

<span data-ttu-id="9e3bf-172">Eseguire Visual Studio come amministratore:</span><span class="sxs-lookup"><span data-stu-id="9e3bf-172">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="9e3bf-173">Verificare che l'elenco di riepilogo a discesa della configurazione della build sia impostato su **Debug**.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-173">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="9e3bf-174">Impostare il [pulsante Avvia debug](/visualstudio/debugger/debugger-feature-tour) sul profilo **IIS** e selezionare il pulsante per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-174">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="9e3bf-175">Visual Studio potrebbe richiedere un riavvio, se non si è in modalità amministratore.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-175">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="9e3bf-176">In tal caso riavviare Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-176">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="9e3bf-177">Se viene usato un certificato di sviluppo non attendibile, il browser potrebbe richiedere di creare un'eccezione per il certificato non attendibile.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-177">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="9e3bf-178">Il debug della configurazione della build Versione con [Just My Code](/visualstudio/debugger/just-my-code) e le ottimizzazioni del compilatore genera un'esperienza con funzionalità ridotta.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-178">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="9e3bf-179">Ad esempio, i punti di interruzione non vengono raggiunti.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-179">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e3bf-180">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="9e3bf-180">Additional resources</span></span>

* <span data-ttu-id="9e3bf-181">[Getting Started with the IIS Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8) (Introduzione a Gestione IIS in IIS)</span><span class="sxs-lookup"><span data-stu-id="9e3bf-181">[Getting Started with the IIS Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)</span></span>
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9e3bf-182">Questo articolo descrive il supporto del debug di app ASP.NET Core in [Visual Studio](https://visualstudio.microsoft.com) durante l'esecuzione con IIS in Windows Server.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-182">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="9e3bf-183">Questo argomento illustra nel dettaglio l'abilitazione di questo scenario e la configurazione di un progetto.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-183">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9e3bf-184">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="9e3bf-184">Prerequisites</span></span>

* [<span data-ttu-id="9e3bf-185">Visual Studio per Windows</span><span class="sxs-lookup"><span data-stu-id="9e3bf-185">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="9e3bf-186">**ASP.NET e carico di lavoro di sviluppo Web**</span><span class="sxs-lookup"><span data-stu-id="9e3bf-186">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="9e3bf-187">Carico di lavoro di **sviluppo multipiattaforma .NET Core**</span><span class="sxs-lookup"><span data-stu-id="9e3bf-187">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="9e3bf-188">Certificato di sicurezza X.509 (per il supporto di HTTPS)</span><span class="sxs-lookup"><span data-stu-id="9e3bf-188">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="9e3bf-189">Abilitare IIS</span><span class="sxs-lookup"><span data-stu-id="9e3bf-189">Enable IIS</span></span>

1. <span data-ttu-id="9e3bf-190">In Windows passare a **Pannello di controllo** > **Programmi** > **Programmi e funzionalità** > **Attiva o disattiva funzionalità di Windows** (sul lato sinistro dello schermo).</span><span class="sxs-lookup"><span data-stu-id="9e3bf-190">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="9e3bf-191">Selezionare la casella di controllo **Internet Information Services**.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-191">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="9e3bf-192">Seleziona **OK**.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-192">Select **OK**.</span></span>

<span data-ttu-id="9e3bf-193">L'installazione di IIS potrebbe richiedere un riavvio del sistema.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-193">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="9e3bf-194">Configura IIS</span><span class="sxs-lookup"><span data-stu-id="9e3bf-194">Configure IIS</span></span>

<span data-ttu-id="9e3bf-195">IIS deve disporre di un sito Web configurato con gli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="9e3bf-195">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="9e3bf-196">**Nome host**: in genere, il **sito Web predefinito** viene utilizzato con il **nome host** `localhost` .</span><span class="sxs-lookup"><span data-stu-id="9e3bf-196">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="9e3bf-197">Tuttavia, è appropriato qualsiasi sito Web IIS valido con un nome host univoco.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-197">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="9e3bf-198">**Binding del sito**</span><span class="sxs-lookup"><span data-stu-id="9e3bf-198">**Site Binding**</span></span>
  * <span data-ttu-id="9e3bf-199">Per le app che richiedono HTTPS, creare un binding alla porta 443 con un certificato.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-199">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="9e3bf-200">Viene in genere usato il **certificato di sviluppo di IIS Express**, ma può essere usato qualsiasi certificato valido.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-200">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="9e3bf-201">Per le app che usano HTTP, verificare l'esistenza di un binding alla porta 80 o creare un binding alla porta 80 per un nuovo sito.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-201">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="9e3bf-202">Usare un singolo binding per HTTP o HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-202">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="9e3bf-203">**Non è supportato il binding a porte HTTP e HTTPS in contemporanea.**</span><span class="sxs-lookup"><span data-stu-id="9e3bf-203">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="9e3bf-204">Abilitare il supporto di IIS in fase di sviluppo in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9e3bf-204">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="9e3bf-205">Avviare il programma di installazione di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-205">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="9e3bf-206">Selezionare **Modifica** per l'installazione di Visual Studio che si intende usare per il supporto di IIS in fase di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-206">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="9e3bf-207">Per il carico di lavoro **Sviluppo ASP.NET e Web**, individuare e installare il componente **Supporto IIS in fase di sviluppo**.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-207">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="9e3bf-208">Il componente è elencato nella sezione **Facoltativo** in **Supporto IIS in fase di sviluppo** nel pannello **Dettagli di installazione** a destra dei carichi di lavoro.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-208">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="9e3bf-209">Il componente installa il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module), un modulo IIS nativo necessario per l'esecuzione delle applicazioni ASP.NET Core con IIS.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-209">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="9e3bf-210">Configurare il progetto</span><span class="sxs-lookup"><span data-stu-id="9e3bf-210">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="9e3bf-211">Reindirizzamento HTTPS</span><span class="sxs-lookup"><span data-stu-id="9e3bf-211">HTTPS redirection</span></span>

<span data-ttu-id="9e3bf-212">Per un nuovo progetto che richiede HTTPS, selezionare la casella di controllo **Configura per HTTPS** nella finestra **Crea una nuova applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-212">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="9e3bf-213">Selezionando questa casella di controllo viene aggiunto il [middleware di reindirizzamento HTTPS e HSTS](xref:security/enforcing-ssl) all'app quando viene creata.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-213">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="9e3bf-214">Per un progetto esistente che richiede HTTPS, usare il middleware di reindirizzamento HTTPS e HSTS in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-214">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="9e3bf-215">Per altre informazioni, vedere <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-215">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="9e3bf-216">Per un progetto che usa HTTP, il [middleware di reindirizzamento HTTPS e HSTS](xref:security/enforcing-ssl) non viene aggiunto all'app.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-216">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="9e3bf-217">Non è richiesta alcuna configurazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-217">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="9e3bf-218">Profilo di avvio IIS</span><span class="sxs-lookup"><span data-stu-id="9e3bf-218">IIS launch profile</span></span>

<span data-ttu-id="9e3bf-219">Creare un nuovo profilo di avvio per aggiungere il supporto IIS in fase di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="9e3bf-219">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="9e3bf-220">Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni**.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-220">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="9e3bf-221">Selezionare **Proprietà**.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-221">Select **Properties**.</span></span> <span data-ttu-id="9e3bf-222">Aprire la scheda **debug** .</span><span class="sxs-lookup"><span data-stu-id="9e3bf-222">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="9e3bf-223">Per **Profilo** selezionare il pulsante **Nuovo**.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-223">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="9e3bf-224">Denominare il profilo "IIS" nella finestra popup.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-224">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="9e3bf-225">Selezionare **OK** per creare il profilo.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-225">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="9e3bf-226">Per l'impostazione **Avvio** selezionare **IIS** dall'elenco.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-226">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="9e3bf-227">Selezionare la casella di controllo **Avvia browser** e specificare l'URL dell'endpoint.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-227">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="9e3bf-228">Quando l'app richiede HTTPS, usare un endpoint HTTPS (`https://`).</span><span class="sxs-lookup"><span data-stu-id="9e3bf-228">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="9e3bf-229">Per il protocollo HTTP, usare un endpoint HTTP (`http://`).</span><span class="sxs-lookup"><span data-stu-id="9e3bf-229">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="9e3bf-230">Specificare lo stesso nome host e la stessa porta usati nella [configurazione di IIS specificata in precedenza](#configure-iis), in genere `localhost`.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-230">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="9e3bf-231">Specificare il nome dell'app alla fine dell'URL.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-231">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="9e3bf-232">Ad esempio, `https://localhost/WebApplication1` (HTTPS) o `http://localhost/WebApplication1` (HTTP) sono URL di endpoint validi.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-232">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="9e3bf-233">Nella sezione **Variabili di ambiente** selezionare il pulsante **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-233">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="9e3bf-234">Specificare una variabile di ambiente con **Nome**`ASPNETCORE_ENVIRONMENT` e **Valore**`Development`.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-234">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="9e3bf-235">Nell'area **Impostazioni server Web** impostare **URL app** sullo stesso valore usato per l'URL dell'endpoint **Avvia browser**.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-235">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="9e3bf-236">Per l'impostazione **Modello di hosting** in Visual Studio 2019 o versione successiva, selezionare **Predefinito** per usare il modello di hosting usato dal progetto.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-236">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="9e3bf-237">Se il progetto imposta la proprietà `<AspNetCoreHostingModel>` nel file di progetto, viene usato il valore della proprietà (`InProcess` o `OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="9e3bf-237">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="9e3bf-238">Se la proprietà non è presente, viene usato il modello di hosting predefinito dell'app, ovvero out-of-process.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-238">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="9e3bf-239">Se l'app richiede l'impostazione esplicita di un modello di hosting diverso dal modello di hosting normale dell'app, impostare **Modello di hosting** su `In Process` o `Out Of Process` in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-239">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="9e3bf-240">Salvare il profilo.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-240">Save the profile.</span></span>

<span data-ttu-id="9e3bf-241">Se non si usa Visual Studio, aggiungere manualmente un profilo di avvio al file [launchSettings.json](https://json.schemastore.org/launchsettings) nella cartella *Proprietà*.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-241">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="9e3bf-242">L'esempio seguente configura il profilo per usare il protocollo HTTPS:</span><span class="sxs-lookup"><span data-stu-id="9e3bf-242">The following example configures the profile to use the HTTPS protocol:</span></span>

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

<span data-ttu-id="9e3bf-243">Verificare che gli endpoint `applicationUrl` e `launchUrl` corrispondano e usare lo stesso protocollo della configurazione del binding IIS, ovvero HTTP o HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-243">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="9e3bf-244">Eseguire il progetto</span><span class="sxs-lookup"><span data-stu-id="9e3bf-244">Run the project</span></span>

<span data-ttu-id="9e3bf-245">Eseguire Visual Studio come amministratore:</span><span class="sxs-lookup"><span data-stu-id="9e3bf-245">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="9e3bf-246">Verificare che l'elenco di riepilogo a discesa della configurazione della build sia impostato su **Debug**.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-246">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="9e3bf-247">Impostare il [pulsante Avvia debug](/visualstudio/debugger/debugger-feature-tour) sul profilo **IIS** e selezionare il pulsante per avviare l'app.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-247">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="9e3bf-248">Visual Studio potrebbe richiedere un riavvio, se non si è in modalità amministratore.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-248">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="9e3bf-249">In tal caso riavviare Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-249">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="9e3bf-250">Se viene usato un certificato di sviluppo non attendibile, il browser potrebbe richiedere di creare un'eccezione per il certificato non attendibile.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-250">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="9e3bf-251">Il debug della configurazione della build Versione con [Just My Code](/visualstudio/debugger/just-my-code) e le ottimizzazioni del compilatore genera un'esperienza con funzionalità ridotta.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-251">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="9e3bf-252">Ad esempio, i punti di interruzione non vengono raggiunti.</span><span class="sxs-lookup"><span data-stu-id="9e3bf-252">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e3bf-253">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="9e3bf-253">Additional resources</span></span>

* <span data-ttu-id="9e3bf-254">[Getting Started with the IIS Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8) (Introduzione a Gestione IIS in IIS)</span><span class="sxs-lookup"><span data-stu-id="9e3bf-254">[Getting Started with the IIS Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)</span></span>
* <xref:security/enforcing-ssl>

::: moniker-end
