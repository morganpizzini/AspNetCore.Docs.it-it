## <a name="troubleshoot"></a><span data-ttu-id="872af-101">Risolvere problemi</span><span class="sxs-lookup"><span data-stu-id="872af-101">Troubleshoot</span></span>

### <a name="cookies-and-site-data"></a><span data-ttu-id="872af-102">Cookie e dati del sito</span><span class="sxs-lookup"><span data-stu-id="872af-102">Cookies and site data</span></span>

<span data-ttu-id="872af-103">I cookie e i dati del sito possono persistere tra gli aggiornamenti delle app e interferire con i test e la risoluzione dei problemi.</span><span class="sxs-lookup"><span data-stu-id="872af-103">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="872af-104">Cancella quanto segue quando apporti modifiche al codice dell'app, modifiche all'account utente con il provider o modifiche alla configurazione dell'app provider:</span><span class="sxs-lookup"><span data-stu-id="872af-104">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="872af-105">Cookie di accesso utente</span><span class="sxs-lookup"><span data-stu-id="872af-105">User sign-in cookies</span></span>
* <span data-ttu-id="872af-106">Cookie dell'app</span><span class="sxs-lookup"><span data-stu-id="872af-106">App cookies</span></span>
* <span data-ttu-id="872af-107">Dati del sito memorizzati nella cache e archiviati</span><span class="sxs-lookup"><span data-stu-id="872af-107">Cached and stored site data</span></span>

<span data-ttu-id="872af-108">Un approccio per evitare che i cookie persistenti e i dati del sito interferiscano con i test e la risoluzione dei problemi consiste nel:</span><span class="sxs-lookup"><span data-stu-id="872af-108">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="872af-109">Utilizzare un browser per il test che è possibile configurare per eliminare tutti i cookie e i dati del sito ogni volta che il browser viene chiuso.</span><span class="sxs-lookup"><span data-stu-id="872af-109">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
* <span data-ttu-id="872af-110">Chiudere il browser tra qualsiasi modifica alla configurazione dell'app, dell'utente di test o del provider.</span><span class="sxs-lookup"><span data-stu-id="872af-110">Close the browser between any change to the app, test user, or provider configuration.</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="872af-111">Eseguire l'app Server</span><span class="sxs-lookup"><span data-stu-id="872af-111">Run the Server app</span></span>

<span data-ttu-id="872af-112">Durante il test e la risoluzione dei problemi di un'app Blazor ospitata, assicurarsi di eseguire l'app dal progetto **Server.**</span><span class="sxs-lookup"><span data-stu-id="872af-112">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **Server** project.</span></span> <span data-ttu-id="872af-113">Ad esempio, in Visual Studio verificare che il progetto Server sia evidenziato in **Esplora soluzioni** prima di avviare l'app con uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="872af-113">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="872af-114">Selezionare il pulsante **Run** (Esegui).</span><span class="sxs-lookup"><span data-stu-id="872af-114">Select the **Run** button.</span></span>
* <span data-ttu-id="872af-115">Utilizzare **Debug** > **Avvio debug** dal menu.</span><span class="sxs-lookup"><span data-stu-id="872af-115">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="872af-116">Premere <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="872af-116">Press <kbd>F5</kbd>.</span></span>
