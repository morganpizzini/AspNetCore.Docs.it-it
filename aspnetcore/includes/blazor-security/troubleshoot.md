## <a name="troubleshoot"></a>Risolvere problemi

### <a name="cookies-and-site-data"></a>Cookie e dati del sito

I cookie e i dati del sito possono essere mantenuti tra gli aggiornamenti delle app e interferiscono con i test e la risoluzione Quando si apportano modifiche al codice dell'app, si cancellano le modifiche dell'account utente con il provider o le modifiche alla configurazione dell'app provider:

* Cookie di accesso utente
* Cookie dell'app
* Dati del sito memorizzati nella cache e archiviati

Un approccio che impedisce ai cookie e ai dati del sito di interferire con i test e la risoluzione dei problemi consiste nel:

* Configurare un browser
  * Utilizzare un browser per i test che è possibile configurare per eliminare tutti i cookie e i dati del sito ogni volta che il browser viene chiuso.
  * Verificare che il browser sia chiuso manualmente o dall'IDE tra qualsiasi modifica apportata all'app, all'utente di test o alla configurazione del provider.
* Usare un comando personalizzato per aprire un browser in modalità privata o in incognito in Visual Studio:
  * Aprire la finestra di dialogo **Sfoglia con** dal pulsante di **esecuzione** di Visual Studio.
  * Selezionare il pulsante **Aggiungi** .
  * Consente di specificare il percorso del browser nel campo del **programma** .
  * Nel campo **arguments (argomenti** ) specificare l'opzione della riga di comando che il browser usa per aprire in modalità privata o in incognito e l'URL dell'app. Ad esempio:
    * Google Chrome &ndash;`--incognito --new-window https://localhost:5001`
    * Mozilla Firefox &ndash;`-private -url https://localhost:5001`
  * Specificare un nome nel campo **nome descrittivo** . Ad esempio: `Firefox Auth Testing`.
  * Selezionare il pulsante **OK** .
  * Per evitare di dover selezionare il profilo del browser per ogni iterazione del test con un'app, impostare il profilo come predefinito con il pulsante **Imposta come predefinito** .
  * Verificare che il browser sia chiuso dall'IDE tra qualsiasi modifica apportata all'app, all'utente di test o alla configurazione del provider.

### <a name="run-the-server-app"></a>Eseguire l'app Server

Durante il test e la risoluzione dei problemi di un'app Blazer ospitata, assicurarsi di eseguire l'app dal progetto **Server** . Ad esempio, in Visual Studio verificare che il progetto server sia evidenziato in **Esplora soluzioni** prima di avviare l'app con uno degli approcci seguenti:

* Selezionare il pulsante **Run** (Esegui).
* Usare **debug** > **Avvia debug** dal menu.
* Premere <kbd>F5</kbd>.
