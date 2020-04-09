---
title: Pubblicare un'app ASP.NET Core in Azure con Visual Studio
author: rick-anderson
description: Informazioni su come pubblicare un'app ASP.NET Core in Servizio app di Azure con Visual Studio.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 7fc3644df3dcb957f2537538aaa9506c6b38a480
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662204"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Pubblicare un'app ASP.NET Core in Azure con Visual Studio

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


Vedere [Pubblicare un'app Web nel servizio app](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) di Azure usando Visual Studio per Mac se si lavora su macOS.

Per risolvere un problema di distribuzione del Servizio app di Azure, vedere <xref:test/troubleshoot-azure-iis>.

## <a name="set-up"></a>Configurare

* Aprire un [account Azure gratuito](https://azure.microsoft.com/free/dotnet/) se non è già disponibile un account. 

## <a name="create-a-web-app"></a>Creare un'app Web

Nella Pagina iniziale di Visual Studio selezionare **File > Nuovo > Progetto**

![Menu File](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

Completare la finestra di dialogo **Nuovo progetto**:

* Nel riquadro sinistro selezionare **.NET Core**.
* Nel riquadro al centro selezionare **Applicazione Web ASP.NET Core**.
* Selezionare **OK**.

![Finestra di dialogo Nuovo progetto](publish-to-azure-webapp-using-vs/_static/new_prj.png)

Nella finestra di dialogo **Nuova applicazione Web ASP.NET Core**:

* Selezionare **Applicazione Web**.
* Selezionare **Modifica autenticazione**.

![Finestra di dialogo Nuovo progetto](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

Viene visualizzata la finestra di dialogo **Modifica autenticazione**. 

* Selezionare **Account utente individuali**.
* Selezionare **OK** per tornare a **Nuova applicazione Web ASP.NET Core** e selezionare nuovamente **OK**.

![Finestra per autenticazione Nuova applicazione Web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio crea la soluzione.

## <a name="run-the-app"></a>Eseguire l'app

* Premere CTRL+F5 per eseguire il progetto.
* Eseguire il test dei collegamenti **About** (Informazioni su) e **Contact** (Contatto).

![Applicazione Web aperta in Microsoft Edge su localhost](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Registrare un utente

* Selezionare **Registra** e registrare un nuovo utente. È possibile usare un indirizzo di posta elettronica fittizio. Quando si esegue l'invio, nella pagina viene visualizzato l'errore seguente:

    *"Errore interno del server: un'operazione di database non riuscita durante l'elaborazione della richiesta. Eccezione SQL: impossibile aprire il database. L'applicazione di migrazioni esistenti per il contesto del database applicazione può risolvere questo problema."*
* Selezionare **Apply Migrations** (Applica migrazioni) e, quando la pagina è stata caricata, eseguire l'aggiornamento.

![Un errore interno del server indicante che un'operazione di database non è riuscita durante l'elaborazione della richiesta. Per un'eccezione SQL, non è possibile aprire il database. Per risolvere il problema, provare ad applicare le migrazioni esistenti per il contesto di database dell'applicazione.](publish-to-azure-webapp-using-vs/_static/mig.png)

L'app visualizza l'indirizzo di posta elettronica usato per registrare il nuovo utente e un collegamento **Disconnessione**.

![Applicazione Web aperta in Microsoft Edge Il collegamento Register (Registra) viene sostituito dal testo Hello email@domain.com!](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Distribuire l'app in Azure

Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e selezionare **Pubblica...**.

![Menu di scelta rapida con il collegamento per la pubblicazione evidenziato](publish-to-azure-webapp-using-vs/_static/pub.png)

Nella finestra di dialogo **Pubblica**:

* Selezionare **Servizio app di Microsoft Azure**.
* Selezionare l'icona a forma di ingranaggio e quindi selezionare **Crea profilo**.
* Selezionare **Crea profilo**.

![Finestra di dialogo Pubblica](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a>Creare le risorse di Azure

Viene visualizzata la finestra di dialogo **Crea servizio app:The Create App Service** dialog appears:

* Immettere la sottoscrizione.
* I campi di immissione **Nome dell'app**, **Gruppo di risorse** e **Piano di servizio app** vengono popolati automaticamente. È possibile mantenere questi nomi o modificarli.

![Finestra di dialogo Servizio app](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* Selezionare la scheda **Servizi** per creare un nuovo database.

* Selezionare **+** l'icona verde per creare un nuovo database SQL

![Nuovo database SQL](publish-to-azure-webapp-using-vs/_static/sql.png)

* Selezionare **Nuovo** nella finestra di dialogo **Configura database SQL** per creare un nuovo database.

![Nuovo database SQL e server](publish-to-azure-webapp-using-vs/_static/conf.png)

Viene visualizzata la finestra di dialogo **Configura SQL Server**.

* Immettere nome utente e password di amministratore e selezionare **OK**. È possibile mantenere il **nome del server**predefinito . 

> [!NOTE]
> La stringa "admin" non è consentita come nome utente di amministratore.

![Finestra di dialogo Configura SQL Server](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* Selezionare **OK**.

Visual Studio torna alla finestra di dialogo **Crea servizio app**.

* Selezionare **Crea** nella finestra di dialogo **Crea servizio app**.

![Finestra di dialogo Configura database SQL](publish-to-azure-webapp-using-vs/_static/conf_final.png)

Visual Studio crea l'app Web e SQL Server in Azure. Questo passaggio potrebbe richiedere alcuni minuti. Per informazioni sulle risorse create, vedere [Risorse aggiuntive](#additional-resources).

Al termine della distribuzione, selezionare **impostazioni**:

![Finestra di dialogo Configura SQL Server](publish-to-azure-webapp-using-vs/_static/set.png)

Nella pagina **Impostazioni** della finestra di dialogo **Pubblica**:

* Espandere **Database** e **selezionare Usa questa stringa**di connessione in fase di esecuzione .
* Espandere **Migrazioni** di Entity Framework e selezionare **Applica questa migrazione alla pubblicazione**.

* Selezionare **Salva**. Visual Studio torna alla finestra di dialogo **Pubblica**. 

![Finestra di dialogo Pubblica: pannello Impostazioni](publish-to-azure-webapp-using-vs/_static/pubs.png)

Fare clic su **Pubblica**. Visual Studio pubblica l'app in Azure. Al termine della distribuzione, l'app viene aperta in un browser.

### <a name="test-your-app-in-azure"></a>Testare l'app in Azure

* Testare i collegamenti **Informazioni su** e **Contatto**

* Registra un nuovo utente

![Applicazione Web aperta in Microsoft Edge in Servizio app di Azure](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a>Aggiornare l'app

* Modificare la pagina Razor *Pages/About.cshtml* e modificarne il contenuto. Ad esempio, è possibile modificare il paragrafo specificando "Hello ASP.NET Core!":

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* Fare clic con il pulsante destro del mouse sul progetto e selezionare **Pubblica**.

![Menu di scelta rapida con il collegamento per la pubblicazione evidenziato](publish-to-azure-webapp-using-vs/_static/pub.png)

* Dopo la pubblicazione dell'app, verificare che le modifiche apportate siano disponibili in Azure.

![Verificare che l'attività sia stata completata](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>Eseguire la pulizia

Al termine del test dell'app accedere al [portale di Azure](https://portal.azure.com/) ed eliminare l'app.

* Selezionare **Gruppi di risorse** e in seguito il gruppo di risorse che è stato creato.

![Portale di Azure: Gruppi di risorse nel menu laterale](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* Nella pagina **Gruppi di risorse** selezionare **Elimina**.

![Portale di Azure: pagina Gruppi di risorse](publish-to-azure-webapp-using-vs/_static/rgd.png)

* Immettere il nome del gruppo di risorse e selezionare **Elimina**. A questo punto l'app e tutte le altre risorse create in questa esercitazione vengono eliminate da Azure.

### <a name="next-steps"></a>Passaggi successivi

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Risorse aggiuntive

* Per Visual Studio Code, vedere [Profili di pubblicazione](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).
* [Servizio app di Azure](/azure/app-service/app-service-web-overview)
* [Gruppi di risorse di AzureAzure resource groups](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Database SQL di Azure](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
