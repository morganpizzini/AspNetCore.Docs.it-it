---
<span data-ttu-id="b44b6-101">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b44b6-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b44b6-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b44b6-102">'Blazor'</span></span>
- <span data-ttu-id="b44b6-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b44b6-103">'Identity'</span></span>
- <span data-ttu-id="b44b6-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b44b6-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="b44b6-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b44b6-105">'Razor'</span></span>
- <span data-ttu-id="b44b6-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b44b6-106">'SignalR' uid:</span></span> 

---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="b44b6-107">Pubblicare un'app ASP.NET Core in Azure con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b44b6-107">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="b44b6-108">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b44b6-108">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="b44b6-109">Vedere [pubblicare un'app Web nel servizio app Azure usando Visual Studio per Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) se si lavora in MacOS.</span><span class="sxs-lookup"><span data-stu-id="b44b6-109">See [Publish a Web app to Azure App Service using Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="b44b6-110">Per risolvere un problema di distribuzione del Servizio app di Azure, vedere <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="b44b6-110">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="b44b6-111">Configurazione</span><span class="sxs-lookup"><span data-stu-id="b44b6-111">Set up</span></span>

* <span data-ttu-id="b44b6-112">Aprire un [account Azure gratuito](https://azure.microsoft.com/free/dotnet/) se non è già disponibile un account.</span><span class="sxs-lookup"><span data-stu-id="b44b6-112">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="b44b6-113">Creare un'app Web</span><span class="sxs-lookup"><span data-stu-id="b44b6-113">Create a web app</span></span>

<span data-ttu-id="b44b6-114">Nella Pagina iniziale di Visual Studio selezionare **File > Nuovo > Progetto**</span><span class="sxs-lookup"><span data-stu-id="b44b6-114">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![Menu File](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="b44b6-116">Completare la finestra di dialogo **Nuovo progetto**:</span><span class="sxs-lookup"><span data-stu-id="b44b6-116">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="b44b6-117">Selezionare **Applicazione Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-117">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="b44b6-118">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-118">Select **Next**.</span></span>

![Finestra di dialogo Nuovo progetto](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="b44b6-120">Nella finestra di dialogo **Nuova applicazione Web ASP.NET Core**:</span><span class="sxs-lookup"><span data-stu-id="b44b6-120">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="b44b6-121">Selezionare **applicazione Web**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-121">Select **Web Application**.</span></span>
* <span data-ttu-id="b44b6-122">Selezionare **modifica** in autenticazione.</span><span class="sxs-lookup"><span data-stu-id="b44b6-122">Select **Change** under Authentication.</span></span>

![Finestra di dialogo nuovo ASP.NET Core Web](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="b44b6-124">Viene visualizzata la finestra di dialogo **Modifica autenticazione**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-124">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="b44b6-125">Selezionare **Account utente individuali**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-125">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="b44b6-126">Selezionare **OK** per tornare alla **nuova applicazione Web ASP.NET Core**, quindi selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-126">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **Create**.</span></span>

![Finestra per autenticazione Nuova applicazione Web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="b44b6-128">Visual Studio crea la soluzione.</span><span class="sxs-lookup"><span data-stu-id="b44b6-128">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="b44b6-129">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="b44b6-129">Run the app</span></span>

* <span data-ttu-id="b44b6-130">Premere CTRL+F5 per eseguire il progetto.</span><span class="sxs-lookup"><span data-stu-id="b44b6-130">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="b44b6-131">Testare il collegamento per la **privacy** .</span><span class="sxs-lookup"><span data-stu-id="b44b6-131">Test the **Privacy** link.</span></span>

![Applicazione Web aperta in Microsoft Edge su localhost](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="b44b6-133">Registrare un utente</span><span class="sxs-lookup"><span data-stu-id="b44b6-133">Register a user</span></span>

* <span data-ttu-id="b44b6-134">Selezionare **Registra** e registrare un nuovo utente.</span><span class="sxs-lookup"><span data-stu-id="b44b6-134">Select **Register** and register a new user.</span></span> <span data-ttu-id="b44b6-135">È possibile usare un indirizzo di posta elettronica fittizio.</span><span class="sxs-lookup"><span data-stu-id="b44b6-135">You can use a fictitious email address.</span></span> <span data-ttu-id="b44b6-136">Quando si esegue l'invio, nella pagina viene visualizzato l'errore seguente:</span><span class="sxs-lookup"><span data-stu-id="b44b6-136">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="b44b6-137">*"Un'operazione sul database non è riuscita durante l'elaborazione della richiesta. Per risolvere il problema, è possibile applicare le migrazioni esistenti per il contesto di database dell'applicazione. "*</span><span class="sxs-lookup"><span data-stu-id="b44b6-137">*"A database operation failed while processing the request. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="b44b6-138">Selezionare **Apply Migrations** (Applica migrazioni) e, quando la pagina è stata caricata, eseguire l'aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="b44b6-138">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![A database operation failed while processing the request.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="b44b6-141">L'app Visualizza il messaggio di posta elettronica usato per registrare il nuovo utente e un collegamento di **disconnessione** .</span><span class="sxs-lookup"><span data-stu-id="b44b6-141">The app displays the email used to register the new user and a **Logout** link.</span></span>

![Applicazione Web aperta in Microsoft Edge](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="b44b6-144">Distribuire l'app in Azure</span><span class="sxs-lookup"><span data-stu-id="b44b6-144">Deploy the app to Azure</span></span>

<span data-ttu-id="b44b6-145">Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **pubblica.**...</span><span class="sxs-lookup"><span data-stu-id="b44b6-145">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Menu di scelta rapida con il collegamento per la pubblicazione evidenziato](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="b44b6-147">Nella finestra di dialogo **Pubblica**:</span><span class="sxs-lookup"><span data-stu-id="b44b6-147">In the **Publish** dialog:</span></span>

* <span data-ttu-id="b44b6-148">Selezionare **Azure**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-148">Select **Azure**.</span></span>
* <span data-ttu-id="b44b6-149">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-149">Select **Next**.</span></span>

![Finestra di dialogo Pubblica](publish-to-azure-webapp-using-vs/_static/maas1.png)

<span data-ttu-id="b44b6-151">Nella finestra di dialogo **Pubblica**:</span><span class="sxs-lookup"><span data-stu-id="b44b6-151">In the **Publish** dialog:</span></span>

* <span data-ttu-id="b44b6-152">Selezionare **servizio app Azure (Linux)**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-152">Select **Azure App Service (Linux)**.</span></span>
* <span data-ttu-id="b44b6-153">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-153">Select **Next**.</span></span>

![Finestra di dialogo pubblica: selezionare il servizio di Azure](publish-to-azure-webapp-using-vs/_static/maas2.png)

<span data-ttu-id="b44b6-155">Nella finestra di dialogo **pubblica** selezionare **Crea un nuovo servizio app Azure...**</span><span class="sxs-lookup"><span data-stu-id="b44b6-155">In the **Publish** dialog select **Create a new Azure App Service...**</span></span>

![Finestra di dialogo pubblica: selezionare l'istanza del servizio di Azure](publish-to-azure-webapp-using-vs/_static/maas3.png)

<span data-ttu-id="b44b6-157">Viene visualizzata la finestra di dialogo **Crea servizio app** :</span><span class="sxs-lookup"><span data-stu-id="b44b6-157">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="b44b6-158">I campi di immissione **Nome dell'app**, **Gruppo di risorse** e **Piano di servizio app** vengono popolati automaticamente.</span><span class="sxs-lookup"><span data-stu-id="b44b6-158">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="b44b6-159">È possibile mantenere questi nomi o modificarli.</span><span class="sxs-lookup"><span data-stu-id="b44b6-159">You can keep these names or change them.</span></span>
* <span data-ttu-id="b44b6-160">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-160">Select **Create**.</span></span>

![Finestra di dialogo Crea servizio app](publish-to-azure-webapp-using-vs/_static/newrg1.png)

<span data-ttu-id="b44b6-162">Al termine della creazione, la finestra di dialogo viene chiusa automaticamente e la finestra di dialogo di **pubblicazione** viene nuovamente messa a fuoco:</span><span class="sxs-lookup"><span data-stu-id="b44b6-162">After creation is completed the dialog is automatically closed and the **Publish** dialog gets focus again:</span></span>

* <span data-ttu-id="b44b6-163">La nuova istanza appena creata viene selezionata automaticamente.</span><span class="sxs-lookup"><span data-stu-id="b44b6-163">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="b44b6-164">Selezionare **Fine**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-164">Select **Finish**.</span></span>

![Finestra di dialogo pubblica: selezionare l'istanza del servizio app](publish-to-azure-webapp-using-vs/_static/select_as.png)

<span data-ttu-id="b44b6-166">Verrà visualizzata la pagina di **Riepilogo del profilo di pubblicazione** .</span><span class="sxs-lookup"><span data-stu-id="b44b6-166">Next you see the **Publish Profile summary** page.</span></span> <span data-ttu-id="b44b6-167">Visual Studio ha rilevato che questa applicazione richiede un database di SQL Server e chiede di configurarlo.</span><span class="sxs-lookup"><span data-stu-id="b44b6-167">Visual Studio has detected that this application requires a SQL Server database and it's asking you to configure it.</span></span> <span data-ttu-id="b44b6-168">Selezionare **Configura**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-168">Select **Configure**.</span></span>

![Pagina Riepilogo profilo di pubblicazione: Configura dipendenza SQL Server](publish-to-azure-webapp-using-vs/_static/sql.png)

<span data-ttu-id="b44b6-170">Verrà visualizzata la finestra di dialogo **Configura dipendenza** :</span><span class="sxs-lookup"><span data-stu-id="b44b6-170">The **Configure dependency** dialog appears:</span></span>

* <span data-ttu-id="b44b6-171">Selezionare **database SQL di Azure**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-171">Select **Azure SQL Database**.</span></span>
* <span data-ttu-id="b44b6-172">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-172">Select **Next**.</span></span>

![Configurare la finestra di dialogo SQL Server dipendenza](publish-to-azure-webapp-using-vs/_static/sql1.png)

<span data-ttu-id="b44b6-174">Nella finestra di dialogo **Configura database SQL di Azure** selezionare **Crea un database SQL**</span><span class="sxs-lookup"><span data-stu-id="b44b6-174">In the **Configure Azure SQL database** dialog select **Create a SQL Database**</span></span>

![Finestra di dialogo Configura database SQL di Azure](publish-to-azure-webapp-using-vs/_static/sql2.png)

<span data-ttu-id="b44b6-176">Viene visualizzato il **database SQL di Azure per creare** :</span><span class="sxs-lookup"><span data-stu-id="b44b6-176">The **Create Azure SQL Database** appears:</span></span>

* <span data-ttu-id="b44b6-177">Il **nome del database**, il **gruppo di risorse**, il **server di database** e i campi di immissione del **piano di servizio app** sono popolati</span><span class="sxs-lookup"><span data-stu-id="b44b6-177">The **Database name**, **Resource Group**, **Database server** and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="b44b6-178">Questi valori possono essere conservati o modificati.</span><span class="sxs-lookup"><span data-stu-id="b44b6-178">You can keep these values or change them.</span></span>
* <span data-ttu-id="b44b6-179">Immettere il **nome utente dell'amministratore** di database e la **password di amministratore** del database per il **server di database** selezionato (si noti che l'account usato deve avere le autorizzazioni necessarie per creare il nuovo database SQL di Azure)</span><span class="sxs-lookup"><span data-stu-id="b44b6-179">Enter the **Database administrator username** and **Database administrator password** for the selected **Database server** (note the account you use must have the necessary permissions to create the new Azure SQL database)</span></span>
* <span data-ttu-id="b44b6-180">Selezionare **Crea**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-180">Select **Create**.</span></span>

![Finestra di dialogo nuovo database SQL di Azure](publish-to-azure-webapp-using-vs/_static/sql_create.png)

<span data-ttu-id="b44b6-182">Al termine della creazione, la finestra di dialogo viene chiusa automaticamente e la finestra di dialogo **Configura database SQL di Azure** ottiene nuovamente lo stato attivo:</span><span class="sxs-lookup"><span data-stu-id="b44b6-182">After creation is completed the dialog is automatically closed and the **Configure Azure SQL Database** dialog gets focus again:</span></span>

* <span data-ttu-id="b44b6-183">La nuova istanza appena creata viene selezionata automaticamente.</span><span class="sxs-lookup"><span data-stu-id="b44b6-183">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="b44b6-184">Selezionare **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-184">Select **Next**.</span></span>

![Finestra di dialogo Configura database SQL di Azure](publish-to-azure-webapp-using-vs/_static/sql_select.png)

<span data-ttu-id="b44b6-186">Nel passaggio successivo della finestra di dialogo **Configura database SQL di Azure** :</span><span class="sxs-lookup"><span data-stu-id="b44b6-186">In the next step of the **Configure Azure SQL Database** dialog:</span></span>

* <span data-ttu-id="b44b6-187">Immettere i campi **nome utente** e **password** connessione database per la connessione al database.</span><span class="sxs-lookup"><span data-stu-id="b44b6-187">Enter the **Database connection user name** and **Database connection password** fields.</span></span> <span data-ttu-id="b44b6-188">Questi sono i dettagli che verranno usati dall'applicazione per connettersi al database in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="b44b6-188">These are the details your application will use to connect to the database at runtime.</span></span> <span data-ttu-id="b44b6-189">La procedura consigliata consiste nell'evitare di utilizzare gli stessi dettagli del nome utente amministratore & password utilizzata nel passaggio precedente.</span><span class="sxs-lookup"><span data-stu-id="b44b6-189">Best practice is to avoid using the same details as the admin username & password used in the previous step.</span></span>
* <span data-ttu-id="b44b6-190">Selezionare **Fine**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-190">Select **Finish**.</span></span>

![Finestra di dialogo Configura database SQL di Azure, dettagli stringa di connessione](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

<span data-ttu-id="b44b6-192">Nella pagina di **Riepilogo del profilo di pubblicazione** selezionare **Impostazioni**:</span><span class="sxs-lookup"><span data-stu-id="b44b6-192">In the **Publish Profile summary** page select **Settings**:</span></span>

![Pagina Riepilogo profilo di pubblicazione: modifica impostazioni](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

<span data-ttu-id="b44b6-194">Nella pagina **Impostazioni** della finestra di dialogo **Pubblica**:</span><span class="sxs-lookup"><span data-stu-id="b44b6-194">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="b44b6-195">Espandere **database** e selezionare **Usa questa stringa di connessione in fase di esecuzione**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-195">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="b44b6-196">Espandere **Entity Framework migrazioni** e selezionare **applica questa migrazione alla pubblicazione**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-196">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="b44b6-197">Selezionare **Salva**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-197">Select **Save**.</span></span> <span data-ttu-id="b44b6-198">Visual Studio torna alla finestra di dialogo **Pubblica**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-198">Visual Studio returns to the **Publish** dialog.</span></span> 

![Finestra di dialogo Pubblica: pannello Impostazioni](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

<span data-ttu-id="b44b6-200">Fare clic su **Pubblica**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-200">Click **Publish**.</span></span> <span data-ttu-id="b44b6-201">Visual Studio pubblica l'app in Azure.</span><span class="sxs-lookup"><span data-stu-id="b44b6-201">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="b44b6-202">Al termine della distribuzione, l'app viene aperta in un browser.</span><span class="sxs-lookup"><span data-stu-id="b44b6-202">When the deployment completes, the app is opened in a browser.</span></span>

![Finestra di dialogo Pubblica: pannello Impostazioni](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a><span data-ttu-id="b44b6-204">Aggiornare l'app</span><span class="sxs-lookup"><span data-stu-id="b44b6-204">Update the app</span></span>

* <span data-ttu-id="b44b6-205">Modificare la pagina *pages/index. cshtml* Razor e modificarne il contenuto.</span><span class="sxs-lookup"><span data-stu-id="b44b6-205">Edit the *Pages/Index.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="b44b6-206">Ad esempio, è possibile modificare il paragrafo specificando "Hello ASP.NET Core!":</span><span class="sxs-lookup"><span data-stu-id="b44b6-206">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* <span data-ttu-id="b44b6-207">Selezionare di nuovo **pubblica** nella pagina di **Riepilogo del profilo di pubblicazione** .</span><span class="sxs-lookup"><span data-stu-id="b44b6-207">Select **Publish** from the **Publish Profile summary** page again.</span></span>

![Pagina Riepilogo profilo di pubblicazione](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* <span data-ttu-id="b44b6-209">Dopo la pubblicazione dell'app, verificare che le modifiche apportate siano disponibili in Azure.</span><span class="sxs-lookup"><span data-stu-id="b44b6-209">After the app is published, verify the changes you made are available on Azure.</span></span>

![Verificare che l'attività sia stata completata](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="b44b6-211">Eseguire la pulizia</span><span class="sxs-lookup"><span data-stu-id="b44b6-211">Clean up</span></span>

<span data-ttu-id="b44b6-212">Al termine del test dell'app accedere al [portale di Azure](https://portal.azure.com/) ed eliminare l'app.</span><span class="sxs-lookup"><span data-stu-id="b44b6-212">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="b44b6-213">Selezionare **Gruppi di risorse** e in seguito il gruppo di risorse che è stato creato.</span><span class="sxs-lookup"><span data-stu-id="b44b6-213">Select **Resource groups**, then select the resource group you created.</span></span>

![Portale di Azure: Gruppi di risorse nel menu laterale](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="b44b6-215">Nella pagina **Gruppi di risorse** selezionare **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-215">In the **Resource groups** page, select **Delete**.</span></span>

![Portale di Azure: pagina Gruppi di risorse](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="b44b6-217">Immettere il nome del gruppo di risorse e selezionare **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="b44b6-217">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="b44b6-218">A questo punto l'app e tutte le altre risorse create in questa esercitazione vengono eliminate da Azure.</span><span class="sxs-lookup"><span data-stu-id="b44b6-218">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="b44b6-219">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="b44b6-219">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="b44b6-220">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b44b6-220">Additional resources</span></span>

* <span data-ttu-id="b44b6-221">Per Visual Studio Code, vedere [Profili di pubblicazione](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="b44b6-221">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="b44b6-222">Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="b44b6-222">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="b44b6-223">Gruppi di risorse di Azure</span><span class="sxs-lookup"><span data-stu-id="b44b6-223">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="b44b6-224">Database SQL di Azure</span><span class="sxs-lookup"><span data-stu-id="b44b6-224">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
