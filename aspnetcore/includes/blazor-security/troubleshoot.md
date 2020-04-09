## <a name="troubleshoot"></a>Risolvere problemi

### <a name="cookies-and-site-data"></a>Cookie e dati del sito

I cookie e i dati del sito possono persistere tra gli aggiornamenti delle app e interferire con i test e la risoluzione dei problemi. Cancella quanto segue quando apporti modifiche al codice dell'app, modifiche all'account utente con il provider o modifiche alla configurazione dell'app provider:

* Cookie di accesso utente
* Cookie dell'app
* Dati del sito memorizzati nella cache e archiviati

Un approccio per evitare che i cookie persistenti e i dati del sito interferiscano con i test e la risoluzione dei problemi consiste nel:

* Utilizzare un browser per il test che è possibile configurare per eliminare tutti i cookie e i dati del sito ogni volta che il browser viene chiuso.
* Chiudere il browser tra qualsiasi modifica alla configurazione dell'app, dell'utente di test o del provider.

### <a name="run-the-server-app"></a>Eseguire l'app Server

Durante il test e la risoluzione dei problemi di un'app Blazor ospitata, assicurarsi di eseguire l'app dal progetto **Server.** Ad esempio, in Visual Studio verificare che il progetto Server sia evidenziato in **Esplora soluzioni** prima di avviare l'app con uno degli approcci seguenti:

* Selezionare il pulsante **Run** (Esegui).
* Utilizzare **Debug** > **Avvio debug** dal menu.
* Premere <kbd>F5</kbd>.
