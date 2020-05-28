---
<span data-ttu-id="c31ab-101">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c31ab-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c31ab-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c31ab-102">'Blazor'</span></span>
- <span data-ttu-id="c31ab-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c31ab-103">'Identity'</span></span>
- <span data-ttu-id="c31ab-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c31ab-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="c31ab-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c31ab-105">'Razor'</span></span>
- <span data-ttu-id="c31ab-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="c31ab-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="c31ab-107">ASP.NET Core test di carico/stress</span><span class="sxs-lookup"><span data-stu-id="c31ab-107">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="c31ab-108">Test di carico e test di stress sono importanti per garantire che un'app Web sia efficiente e scalabile.</span><span class="sxs-lookup"><span data-stu-id="c31ab-108">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="c31ab-109">I rispettivi obiettivi sono diversi anche se condividono spesso test simili.</span><span class="sxs-lookup"><span data-stu-id="c31ab-109">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="c31ab-110">**Test di carico**: verificare se l'app è in grado di gestire un carico specificato di utenti per un determinato scenario soddisfacendo comunque l'obiettivo della risposta.</span><span class="sxs-lookup"><span data-stu-id="c31ab-110">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="c31ab-111">L'app viene eseguita in condizioni normali.</span><span class="sxs-lookup"><span data-stu-id="c31ab-111">The app is run under normal conditions.</span></span>

<span data-ttu-id="c31ab-112">**Test di stress**: verifica la stabilità dell'app quando viene eseguita in condizioni estreme, spesso per un lungo periodo di tempo.</span><span class="sxs-lookup"><span data-stu-id="c31ab-112">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="c31ab-113">I test inseriscono un carico utente elevato, picchi o aumento graduale del carico, sull'app o limitano le risorse di elaborazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="c31ab-113">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="c31ab-114">I test di stress determinano se un'app in stress può recuperare da un errore e restituire normalmente il comportamento previsto.</span><span class="sxs-lookup"><span data-stu-id="c31ab-114">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="c31ab-115">In condizioni di stress, l'app non viene eseguita in condizioni normali.</span><span class="sxs-lookup"><span data-stu-id="c31ab-115">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="c31ab-116">Visual Studio 2019 è l'ultima versione di Visual Studio con le funzionalità del test di carico.</span><span class="sxs-lookup"><span data-stu-id="c31ab-116">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="c31ab-117">Per i clienti che necessitano di strumenti di test di carico in futuro, è consigliabile usare strumenti alternativi, ad esempio Apache JMeter, Akamai CloudTest e BlazeMeter.</span><span class="sxs-lookup"><span data-stu-id="c31ab-117">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="c31ab-118">Per ulteriori informazioni, vedere le [Note sulla versione di Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="c31ab-118">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="c31ab-119">Strumenti di Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c31ab-119">Visual Studio tools</span></span>

<span data-ttu-id="c31ab-120">Visual Studio consente agli utenti di creare, sviluppare ed eseguire il debug di test di carico e prestazioni Web.</span><span class="sxs-lookup"><span data-stu-id="c31ab-120">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="c31ab-121">È disponibile un'opzione per la creazione di test mediante la registrazione di azioni in un Web browser.</span><span class="sxs-lookup"><span data-stu-id="c31ab-121">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="c31ab-122">Per informazioni su come creare, configurare ed eseguire i progetti di test di carico usando Visual Studio 2017, vedere [Guida introduttiva: creare un progetto di test di carico](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="c31ab-122">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="c31ab-123">I test di carico possono essere configurati per l'esecuzione in locale o in esecuzione nel cloud usando Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="c31ab-123">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="c31ab-124">Strumenti di terzi</span><span class="sxs-lookup"><span data-stu-id="c31ab-124">Third-party tools</span></span>

<span data-ttu-id="c31ab-125">L'elenco seguente contiene gli strumenti per le prestazioni Web di terze parti con diversi set di funzionalità:</span><span class="sxs-lookup"><span data-stu-id="c31ab-125">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="c31ab-126">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="c31ab-126">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="c31ab-127">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="c31ab-127">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="c31ab-128">Gatling</span><span class="sxs-lookup"><span data-stu-id="c31ab-128">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="c31ab-129">K6</span><span class="sxs-lookup"><span data-stu-id="c31ab-129">k6</span></span>](https://k6.io)
* [<span data-ttu-id="c31ab-130">Locusta</span><span class="sxs-lookup"><span data-stu-id="c31ab-130">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="c31ab-131">Websurge di Wind West</span><span class="sxs-lookup"><span data-stu-id="c31ab-131">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="c31ab-132">Netling</span><span class="sxs-lookup"><span data-stu-id="c31ab-132">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="c31ab-133">Vegeta</span><span class="sxs-lookup"><span data-stu-id="c31ab-133">Vegeta</span></span>](https://github.com/tsenart/vegeta)

