---
title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Pubblicare un'app ASP.NET Core in Azure con Visual Studio

Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


Vedere [pubblicare un'app Web nel servizio app Azure usando Visual Studio per Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) se si lavora in MacOS.

Per risolvere un problema di distribuzione del Servizio app di Azure, vedere <xref:test/troubleshoot-azure-iis>.

## <a name="set-up"></a>Configurazione

* Aprire un [account Azure gratuito](https://azure.microsoft.com/free/dotnet/) se non è già disponibile un account. 

## <a name="create-a-web-app"></a>Creare un'app Web

Nella Pagina iniziale di Visual Studio selezionare **File > Nuovo > Progetto**

![Menu File](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

Completare la finestra di dialogo **Nuovo progetto**:

* Selezionare **Applicazione Web ASP.NET Core**.
* Selezionare **Avanti**.

![Finestra di dialogo Nuovo progetto](publish-to-azure-webapp-using-vs/_static/new_prj.png)

Nella finestra di dialogo **Nuova applicazione Web ASP.NET Core**:

* Selezionare **applicazione Web**.
* Selezionare **modifica** in autenticazione.

![Finestra di dialogo nuovo ASP.NET Core Web](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

Viene visualizzata la finestra di dialogo **Modifica autenticazione**. 

* Selezionare **Account utente individuali**.
* Selezionare **OK** per tornare alla **nuova applicazione Web ASP.NET Core**, quindi selezionare **Crea**.

![Finestra per autenticazione Nuova applicazione Web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio crea la soluzione.

## <a name="run-the-app"></a>Eseguire l'app

* Premere CTRL+F5 per eseguire il progetto.
* Testare il collegamento per la **privacy** .

![Applicazione Web aperta in Microsoft Edge su localhost](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Registrare un utente

* Selezionare **Registra** e registrare un nuovo utente. È possibile usare un indirizzo di posta elettronica fittizio. Quando si esegue l'invio, nella pagina viene visualizzato l'errore seguente:

    *"Un'operazione sul database non è riuscita durante l'elaborazione della richiesta. Per risolvere il problema, è possibile applicare le migrazioni esistenti per il contesto di database dell'applicazione. "*
* Selezionare **Apply Migrations** (Applica migrazioni) e, quando la pagina è stata caricata, eseguire l'aggiornamento.

![A database operation failed while processing the request. Per risolvere il problema, provare ad applicare le migrazioni esistenti per il contesto di database dell'applicazione.](publish-to-azure-webapp-using-vs/_static/mig.png)

L'app Visualizza il messaggio di posta elettronica usato per registrare il nuovo utente e un collegamento di **disconnessione** .

![Applicazione Web aperta in Microsoft Edge Il collegamento Register (Registra) viene sostituito dal testo Hello user1@example.com!](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Distribuire l'app in Azure

Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **pubblica.**...

![Menu di scelta rapida con il collegamento per la pubblicazione evidenziato](publish-to-azure-webapp-using-vs/_static/pub.png)

Nella finestra di dialogo **Pubblica**:

* Selezionare **Azure**.
* Selezionare **Avanti**.

![Finestra di dialogo Pubblica](publish-to-azure-webapp-using-vs/_static/maas1.png)

Nella finestra di dialogo **Pubblica**:

* Selezionare **servizio app Azure (Linux)**.
* Selezionare **Avanti**.

![Finestra di dialogo pubblica: selezionare il servizio di Azure](publish-to-azure-webapp-using-vs/_static/maas2.png)

Nella finestra di dialogo **pubblica** selezionare **Crea un nuovo servizio app Azure...**

![Finestra di dialogo pubblica: selezionare l'istanza del servizio di Azure](publish-to-azure-webapp-using-vs/_static/maas3.png)

Viene visualizzata la finestra di dialogo **Crea servizio app** :

* I campi di immissione **Nome dell'app**, **Gruppo di risorse** e **Piano di servizio app** vengono popolati automaticamente. È possibile mantenere questi nomi o modificarli.
* Selezionare **Crea**.

![Finestra di dialogo Crea servizio app](publish-to-azure-webapp-using-vs/_static/newrg1.png)

Al termine della creazione, la finestra di dialogo viene chiusa automaticamente e la finestra di dialogo di **pubblicazione** viene nuovamente messa a fuoco:

* La nuova istanza appena creata viene selezionata automaticamente.
* Selezionare **Fine**.

![Finestra di dialogo pubblica: selezionare l'istanza del servizio app](publish-to-azure-webapp-using-vs/_static/select_as.png)

Verrà visualizzata la pagina di **Riepilogo del profilo di pubblicazione** . Visual Studio ha rilevato che questa applicazione richiede un database di SQL Server e chiede di configurarlo. Selezionare **Configura**.

![Pagina Riepilogo profilo di pubblicazione: Configura dipendenza SQL Server](publish-to-azure-webapp-using-vs/_static/sql.png)

Verrà visualizzata la finestra di dialogo **Configura dipendenza** :

* Selezionare **database SQL di Azure**.
* Selezionare **Avanti**.

![Configurare la finestra di dialogo SQL Server dipendenza](publish-to-azure-webapp-using-vs/_static/sql1.png)

Nella finestra di dialogo **Configura database SQL di Azure** selezionare **Crea un database SQL**

![Finestra di dialogo Configura database SQL di Azure](publish-to-azure-webapp-using-vs/_static/sql2.png)

Viene visualizzato il **database SQL di Azure per creare** :

* Il **nome del database**, il **gruppo di risorse**, il **server di database** e i campi di immissione del **piano di servizio app** sono popolati Questi valori possono essere conservati o modificati.
* Immettere il **nome utente dell'amministratore** di database e la **password di amministratore** del database per il **server di database** selezionato (si noti che l'account usato deve avere le autorizzazioni necessarie per creare il nuovo database SQL di Azure)
* Selezionare **Crea**.

![Finestra di dialogo nuovo database SQL di Azure](publish-to-azure-webapp-using-vs/_static/sql_create.png)

Al termine della creazione, la finestra di dialogo viene chiusa automaticamente e la finestra di dialogo **Configura database SQL di Azure** ottiene nuovamente lo stato attivo:

* La nuova istanza appena creata viene selezionata automaticamente.
* Selezionare **Avanti**.

![Finestra di dialogo Configura database SQL di Azure](publish-to-azure-webapp-using-vs/_static/sql_select.png)

Nel passaggio successivo della finestra di dialogo **Configura database SQL di Azure** :

* Immettere i campi **nome utente** e **password** connessione database per la connessione al database. Questi sono i dettagli che verranno usati dall'applicazione per connettersi al database in fase di esecuzione. La procedura consigliata consiste nell'evitare di utilizzare gli stessi dettagli del nome utente amministratore & password utilizzata nel passaggio precedente.
* Selezionare **Fine**.

![Finestra di dialogo Configura database SQL di Azure, dettagli stringa di connessione](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

Nella pagina di **Riepilogo del profilo di pubblicazione** selezionare **Impostazioni**:

![Pagina Riepilogo profilo di pubblicazione: modifica impostazioni](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

Nella pagina **Impostazioni** della finestra di dialogo **Pubblica**:

* Espandere **database** e selezionare **Usa questa stringa di connessione in fase di esecuzione**.
* Espandere **Entity Framework migrazioni** e selezionare **applica questa migrazione alla pubblicazione**.

* Selezionare **Salva**. Visual Studio torna alla finestra di dialogo **Pubblica**. 

![Finestra di dialogo Pubblica: pannello Impostazioni](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

Fare clic su **Pubblica**. Visual Studio pubblica l'app in Azure. Al termine della distribuzione, l'app viene aperta in un browser.

![Finestra di dialogo Pubblica: pannello Impostazioni](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a>Aggiornare l'app

* Modificare la pagina *pages/index. cshtml* Razor e modificarne il contenuto. Ad esempio, è possibile modificare il paragrafo specificando "Hello ASP.NET Core!":

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* Selezionare di nuovo **pubblica** nella pagina di **Riepilogo del profilo di pubblicazione** .

![Pagina Riepilogo profilo di pubblicazione](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

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
* [Gruppi di risorse di Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Database SQL di Azure](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
