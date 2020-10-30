---
title: Pubblicare un'app ASP.NET Core in Azure con Visual Studio
author: rick-anderson
description: Informazioni su come pubblicare un'app ASP.NET Core in Servizio app di Azure con Visual Studio.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
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
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 817169503a80a771354e32123d65ba2bf388aa2d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060222"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="3fbde-103">Pubblicare un'app ASP.NET Core in Azure con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3fbde-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="3fbde-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3fbde-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="3fbde-105">Vedere [pubblicare un'app Web nel servizio app Azure usando Visual Studio per Mac](/visualstudio/mac/publish-app-svc?view=vsmac-2019) se si lavora in MacOS.</span><span class="sxs-lookup"><span data-stu-id="3fbde-105">See [Publish a Web app to Azure App Service using Visual Studio for Mac](/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="3fbde-106">Per risolvere un problema di distribuzione del Servizio app di Azure, vedere <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="3fbde-106">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="3fbde-107">Configurare</span><span class="sxs-lookup"><span data-stu-id="3fbde-107">Set up</span></span>

* <span data-ttu-id="3fbde-108">Aprire un [account Azure gratuito](https://azure.microsoft.com/free/dotnet/) se non è già disponibile un account.</span><span class="sxs-lookup"><span data-stu-id="3fbde-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="3fbde-109">Creare un'app Web</span><span class="sxs-lookup"><span data-stu-id="3fbde-109">Create a web app</span></span>

<span data-ttu-id="3fbde-110">Nella Pagina iniziale di Visual Studio selezionare **File > Nuovo > Progetto**</span><span class="sxs-lookup"><span data-stu-id="3fbde-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![Menu File](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="3fbde-112">Completare la finestra di dialogo **Nuovo progetto** :</span><span class="sxs-lookup"><span data-stu-id="3fbde-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="3fbde-113">Selezionare **Applicazione Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-113">Select **ASP.NET Core Web Application** .</span></span>
* <span data-ttu-id="3fbde-114">Selezionare **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-114">Select **Next** .</span></span>

![Finestra di dialogo Nuovo progetto](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="3fbde-116">Nella finestra di dialogo **Nuova applicazione Web ASP.NET Core** :</span><span class="sxs-lookup"><span data-stu-id="3fbde-116">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="3fbde-117">Selezionare **applicazione Web** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-117">Select **Web Application** .</span></span>
* <span data-ttu-id="3fbde-118">Selezionare **modifica** in autenticazione.</span><span class="sxs-lookup"><span data-stu-id="3fbde-118">Select **Change** under Authentication.</span></span>

![Finestra di dialogo nuovo ASP.NET Core Web](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="3fbde-120">Viene visualizzata la finestra di dialogo **Modifica autenticazione** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-120">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="3fbde-121">Selezionare **Account utente individuali** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-121">Select **Individual User Accounts** .</span></span>
* <span data-ttu-id="3fbde-122">Selezionare **OK** per tornare alla **nuova applicazione Web ASP.NET Core** , quindi selezionare **Crea** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-122">Select **OK** to return to the **New ASP.NET Core Web Application** , then select **Create** .</span></span>

![Finestra per autenticazione Nuova applicazione Web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="3fbde-124">Visual Studio crea la soluzione.</span><span class="sxs-lookup"><span data-stu-id="3fbde-124">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="3fbde-125">Eseguire l'app</span><span class="sxs-lookup"><span data-stu-id="3fbde-125">Run the app</span></span>

* <span data-ttu-id="3fbde-126">Premere CTRL+F5 per eseguire il progetto.</span><span class="sxs-lookup"><span data-stu-id="3fbde-126">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="3fbde-127">Testare il collegamento per la **privacy** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-127">Test the **Privacy** link.</span></span>

![Applicazione Web aperta in Microsoft Edge su localhost](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="3fbde-129">Registrare un utente</span><span class="sxs-lookup"><span data-stu-id="3fbde-129">Register a user</span></span>

* <span data-ttu-id="3fbde-130">Selezionare **Registra** e registrare un nuovo utente.</span><span class="sxs-lookup"><span data-stu-id="3fbde-130">Select **Register** and register a new user.</span></span> <span data-ttu-id="3fbde-131">È possibile usare un indirizzo di posta elettronica fittizio.</span><span class="sxs-lookup"><span data-stu-id="3fbde-131">You can use a fictitious email address.</span></span> <span data-ttu-id="3fbde-132">Quando si esegue l'invio, nella pagina viene visualizzato l'errore seguente:</span><span class="sxs-lookup"><span data-stu-id="3fbde-132">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="3fbde-133">*"Un'operazione sul database non è riuscita durante l'elaborazione della richiesta. Per risolvere il problema, è possibile applicare le migrazioni esistenti per il contesto di database dell'applicazione. "*</span><span class="sxs-lookup"><span data-stu-id="3fbde-133">*"A database operation failed while processing the request. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="3fbde-134">Selezionare **Apply Migrations** (Applica migrazioni) e, quando la pagina è stata caricata, eseguire l'aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="3fbde-134">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![A database operation failed while processing the request.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="3fbde-137">L'app Visualizza il messaggio di posta elettronica usato per registrare il nuovo utente e un collegamento di **disconnessione** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-137">The app displays the email used to register the new user and a **Logout** link.</span></span>

![Applicazione Web aperta in Microsoft Edge](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="3fbde-140">Distribuire l'app in Azure</span><span class="sxs-lookup"><span data-stu-id="3fbde-140">Deploy the app to Azure</span></span>

<span data-ttu-id="3fbde-141">Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **pubblica.** ...</span><span class="sxs-lookup"><span data-stu-id="3fbde-141">Right-click on the project in Solution Explorer and select **Publish...** .</span></span>

![Menu di scelta rapida con il collegamento per la pubblicazione evidenziato](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="3fbde-143">Nella finestra di dialogo **Pubblica** :</span><span class="sxs-lookup"><span data-stu-id="3fbde-143">In the **Publish** dialog:</span></span>

* <span data-ttu-id="3fbde-144">Selezionare **Azure** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-144">Select **Azure** .</span></span>
* <span data-ttu-id="3fbde-145">Selezionare **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-145">Select **Next** .</span></span>

![Finestra di dialogo Pubblica](publish-to-azure-webapp-using-vs/_static/maas1.png)

<span data-ttu-id="3fbde-147">Nella finestra di dialogo **Pubblica** :</span><span class="sxs-lookup"><span data-stu-id="3fbde-147">In the **Publish** dialog:</span></span>

* <span data-ttu-id="3fbde-148">Selezionare **servizio app Azure (Linux)** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-148">Select **Azure App Service (Linux)** .</span></span>
* <span data-ttu-id="3fbde-149">Selezionare **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-149">Select **Next** .</span></span>

![Finestra di dialogo pubblica: selezionare il servizio di Azure](publish-to-azure-webapp-using-vs/_static/maas2.png)

<span data-ttu-id="3fbde-151">Nella finestra di dialogo **pubblica** selezionare **Crea un nuovo servizio app Azure...**</span><span class="sxs-lookup"><span data-stu-id="3fbde-151">In the **Publish** dialog select **Create a new Azure App Service...**</span></span>

![Finestra di dialogo pubblica: selezionare l'istanza del servizio di Azure](publish-to-azure-webapp-using-vs/_static/maas3.png)

<span data-ttu-id="3fbde-153">Viene visualizzata la finestra di dialogo **Crea servizio app** :</span><span class="sxs-lookup"><span data-stu-id="3fbde-153">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="3fbde-154">I campi di immissione **Nome dell'app** , **Gruppo di risorse** e **Piano di servizio app** vengono popolati automaticamente.</span><span class="sxs-lookup"><span data-stu-id="3fbde-154">The **App Name** , **Resource Group** , and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="3fbde-155">È possibile mantenere questi nomi o modificarli.</span><span class="sxs-lookup"><span data-stu-id="3fbde-155">You can keep these names or change them.</span></span>
* <span data-ttu-id="3fbde-156">Selezionare **Crea** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-156">Select **Create** .</span></span>

![Finestra di dialogo Crea servizio app](publish-to-azure-webapp-using-vs/_static/newrg1.png)

<span data-ttu-id="3fbde-158">Al termine della creazione, la finestra di dialogo viene chiusa automaticamente e la finestra di dialogo di **pubblicazione** viene nuovamente messa a fuoco:</span><span class="sxs-lookup"><span data-stu-id="3fbde-158">After creation is completed the dialog is automatically closed and the **Publish** dialog gets focus again:</span></span>

* <span data-ttu-id="3fbde-159">La nuova istanza appena creata viene selezionata automaticamente.</span><span class="sxs-lookup"><span data-stu-id="3fbde-159">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="3fbde-160">Selezionare **Fine** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-160">Select **Finish** .</span></span>

![Finestra di dialogo pubblica: selezionare l'istanza del servizio app](publish-to-azure-webapp-using-vs/_static/select_as.png)

<span data-ttu-id="3fbde-162">Verrà visualizzata la pagina di **Riepilogo del profilo di pubblicazione** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-162">Next you see the **Publish Profile summary** page.</span></span> <span data-ttu-id="3fbde-163">Visual Studio ha rilevato che questa applicazione richiede un database di SQL Server e chiede di configurarlo.</span><span class="sxs-lookup"><span data-stu-id="3fbde-163">Visual Studio has detected that this application requires a SQL Server database and it's asking you to configure it.</span></span> <span data-ttu-id="3fbde-164">Selezionare **Configura** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-164">Select **Configure** .</span></span>

![Pagina Riepilogo profilo di pubblicazione: Configura dipendenza SQL Server](publish-to-azure-webapp-using-vs/_static/sql.png)

<span data-ttu-id="3fbde-166">Verrà visualizzata la finestra di dialogo **Configura dipendenza** :</span><span class="sxs-lookup"><span data-stu-id="3fbde-166">The **Configure dependency** dialog appears:</span></span>

* <span data-ttu-id="3fbde-167">Selezionare **database SQL di Azure** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-167">Select **Azure SQL Database** .</span></span>
* <span data-ttu-id="3fbde-168">Selezionare **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-168">Select **Next** .</span></span>

![Configurare la finestra di dialogo SQL Server dipendenza](publish-to-azure-webapp-using-vs/_static/sql1.png)

<span data-ttu-id="3fbde-170">Nella finestra di dialogo **Configura database SQL di Azure** selezionare **Crea un database SQL**</span><span class="sxs-lookup"><span data-stu-id="3fbde-170">In the **Configure Azure SQL database** dialog select **Create a SQL Database**</span></span>

![Finestra di dialogo Configura database SQL di Azure](publish-to-azure-webapp-using-vs/_static/sql2.png)

<span data-ttu-id="3fbde-172">Viene visualizzato il **database SQL di Azure per creare** :</span><span class="sxs-lookup"><span data-stu-id="3fbde-172">The **Create Azure SQL Database** appears:</span></span>

* <span data-ttu-id="3fbde-173">Il **nome del database** , il **gruppo di risorse** , il **server di database** e i campi di immissione del **piano di servizio app** sono popolati</span><span class="sxs-lookup"><span data-stu-id="3fbde-173">The **Database name** , **Resource Group** , **Database server** and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="3fbde-174">Questi valori possono essere conservati o modificati.</span><span class="sxs-lookup"><span data-stu-id="3fbde-174">You can keep these values or change them.</span></span>
* <span data-ttu-id="3fbde-175">Immettere il **nome utente dell'amministratore** di database e la **password di amministratore** del database per il **server di database** selezionato (si noti che l'account usato deve avere le autorizzazioni necessarie per creare il nuovo database SQL di Azure)</span><span class="sxs-lookup"><span data-stu-id="3fbde-175">Enter the **Database administrator username** and **Database administrator password** for the selected **Database server** (note the account you use must have the necessary permissions to create the new Azure SQL database)</span></span>
* <span data-ttu-id="3fbde-176">Selezionare **Crea** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-176">Select **Create** .</span></span>

![Finestra di dialogo nuovo database SQL di Azure](publish-to-azure-webapp-using-vs/_static/sql_create.png)

<span data-ttu-id="3fbde-178">Al termine della creazione, la finestra di dialogo viene chiusa automaticamente e la finestra di dialogo **Configura database SQL di Azure** ottiene nuovamente lo stato attivo:</span><span class="sxs-lookup"><span data-stu-id="3fbde-178">After creation is completed the dialog is automatically closed and the **Configure Azure SQL Database** dialog gets focus again:</span></span>

* <span data-ttu-id="3fbde-179">La nuova istanza appena creata viene selezionata automaticamente.</span><span class="sxs-lookup"><span data-stu-id="3fbde-179">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="3fbde-180">Selezionare **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-180">Select **Next** .</span></span>

![Finestra di dialogo Configura database SQL di Azure](publish-to-azure-webapp-using-vs/_static/sql_select.png)

<span data-ttu-id="3fbde-182">Nel passaggio successivo della finestra di dialogo **Configura database SQL di Azure** :</span><span class="sxs-lookup"><span data-stu-id="3fbde-182">In the next step of the **Configure Azure SQL Database** dialog:</span></span>

* <span data-ttu-id="3fbde-183">Immettere i campi **nome utente** e **password** connessione database per la connessione al database.</span><span class="sxs-lookup"><span data-stu-id="3fbde-183">Enter the **Database connection user name** and **Database connection password** fields.</span></span> <span data-ttu-id="3fbde-184">Questi sono i dettagli che verranno usati dall'applicazione per connettersi al database in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="3fbde-184">These are the details your application will use to connect to the database at runtime.</span></span> <span data-ttu-id="3fbde-185">La procedura consigliata consiste nell'evitare di utilizzare gli stessi dettagli del nome utente amministratore & password utilizzata nel passaggio precedente.</span><span class="sxs-lookup"><span data-stu-id="3fbde-185">Best practice is to avoid using the same details as the admin username & password used in the previous step.</span></span>
* <span data-ttu-id="3fbde-186">Selezionare **Fine** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-186">Select **Finish** .</span></span>

![Finestra di dialogo Configura database SQL di Azure, dettagli stringa di connessione](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

<span data-ttu-id="3fbde-188">Nella pagina di **Riepilogo del profilo di pubblicazione** selezionare **Impostazioni** :</span><span class="sxs-lookup"><span data-stu-id="3fbde-188">In the **Publish Profile summary** page select **Settings** :</span></span>

![Pagina Riepilogo profilo di pubblicazione: modifica impostazioni](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

<span data-ttu-id="3fbde-190">Nella pagina **Impostazioni** della finestra di dialogo **Pubblica** :</span><span class="sxs-lookup"><span data-stu-id="3fbde-190">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="3fbde-191">Espandere **database** e selezionare **Usa questa stringa di connessione in fase di esecuzione** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-191">Expand **Databases** and check **Use this connection string at runtime** .</span></span>
* <span data-ttu-id="3fbde-192">Espandere **Entity Framework migrazioni** e selezionare **applica questa migrazione alla pubblicazione** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-192">Expand **Entity Framework Migrations** and check **Apply this migration on publish** .</span></span>

* <span data-ttu-id="3fbde-193">Selezionare **Salva** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-193">Select **Save** .</span></span> <span data-ttu-id="3fbde-194">Visual Studio torna alla finestra di dialogo **Pubblica** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-194">Visual Studio returns to the **Publish** dialog.</span></span> 

![Finestra di dialogo Pubblica: pannello Impostazioni](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

<span data-ttu-id="3fbde-196">Fare clic su **Pubblica** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-196">Click **Publish** .</span></span> <span data-ttu-id="3fbde-197">Visual Studio pubblica l'app in Azure.</span><span class="sxs-lookup"><span data-stu-id="3fbde-197">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="3fbde-198">Al termine della distribuzione, l'app viene aperta in un browser.</span><span class="sxs-lookup"><span data-stu-id="3fbde-198">When the deployment completes, the app is opened in a browser.</span></span>

![Finestra di dialogo Pubblica: pannello Impostazioni](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a><span data-ttu-id="3fbde-200">Aggiornare l'app</span><span class="sxs-lookup"><span data-stu-id="3fbde-200">Update the app</span></span>

* <span data-ttu-id="3fbde-201">Modificare la pagina *pages/index. cshtml* :::no-loc(Razor)::: e modificarne il contenuto.</span><span class="sxs-lookup"><span data-stu-id="3fbde-201">Edit the *Pages/Index.cshtml* :::no-loc(Razor)::: page and change its contents.</span></span> <span data-ttu-id="3fbde-202">Ad esempio, è possibile modificare il paragrafo specificando "Hello ASP.NET Core!":</span><span class="sxs-lookup"><span data-stu-id="3fbde-202">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* <span data-ttu-id="3fbde-203">Selezionare di nuovo **pubblica** nella pagina di **Riepilogo del profilo di pubblicazione** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-203">Select **Publish** from the **Publish Profile summary** page again.</span></span>

![Pagina Riepilogo profilo di pubblicazione](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* <span data-ttu-id="3fbde-205">Dopo la pubblicazione dell'app, verificare che le modifiche apportate siano disponibili in Azure.</span><span class="sxs-lookup"><span data-stu-id="3fbde-205">After the app is published, verify the changes you made are available on Azure.</span></span>

![Verificare che l'attività sia stata completata](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="3fbde-207">Eseguire la pulizia</span><span class="sxs-lookup"><span data-stu-id="3fbde-207">Clean up</span></span>

<span data-ttu-id="3fbde-208">Al termine del test dell'app accedere al [portale di Azure](https://portal.azure.com/) ed eliminare l'app.</span><span class="sxs-lookup"><span data-stu-id="3fbde-208">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="3fbde-209">Selezionare **Gruppi di risorse** e in seguito il gruppo di risorse che è stato creato.</span><span class="sxs-lookup"><span data-stu-id="3fbde-209">Select **Resource groups** , then select the resource group you created.</span></span>

![Portale di Azure: Gruppi di risorse nel menu laterale](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="3fbde-211">Nella pagina **Gruppi di risorse** selezionare **Elimina** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-211">In the **Resource groups** page, select **Delete** .</span></span>

![Portale di Azure: pagina Gruppi di risorse](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="3fbde-213">Immettere il nome del gruppo di risorse e selezionare **Elimina** .</span><span class="sxs-lookup"><span data-stu-id="3fbde-213">Enter the name of the resource group and select **Delete** .</span></span> <span data-ttu-id="3fbde-214">A questo punto l'app e tutte le altre risorse create in questa esercitazione vengono eliminate da Azure.</span><span class="sxs-lookup"><span data-stu-id="3fbde-214">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="3fbde-215">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="3fbde-215">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="3fbde-216">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="3fbde-216">Additional resources</span></span>

* <span data-ttu-id="3fbde-217">Per Visual Studio Code, vedere [Profili di pubblicazione](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="3fbde-217">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="3fbde-218">Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="3fbde-218">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="3fbde-219">Gruppi di risorse di Azure</span><span class="sxs-lookup"><span data-stu-id="3fbde-219">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="3fbde-220">Database SQL di Azure</span><span class="sxs-lookup"><span data-stu-id="3fbde-220">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>