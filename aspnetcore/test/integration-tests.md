---
<span data-ttu-id="0ce0f-101">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-102">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-103">'Identity'</span></span>
- <span data-ttu-id="0ce0f-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-105">'Razor'</span></span>
- <span data-ttu-id="0ce0f-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-106">'SignalR' uid:</span></span> 

---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="0ce0f-107">Test di integrazione in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0ce0f-107">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="0ce0f-108">Di [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/)e [Jos van der til](https://jvandertil.nl)</span><span class="sxs-lookup"><span data-stu-id="0ce0f-108">By [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/), and [Jos van der Til](https://jvandertil.nl)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0ce0f-109">I test di integrazione assicurano che i componenti di un'app funzionino correttamente a un livello che include l'infrastruttura di supporto dell'app, ad esempio il database, la file system e la rete.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-109">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="0ce0f-110">ASP.NET Core supporta i test di integrazione utilizzando un framework unit test con un host Web di prova e un server di prova in memoria.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-110">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="0ce0f-111">In questo argomento si presuppone una conoscenza di base degli unit test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-111">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="0ce0f-112">Se non si ha familiarità con i concetti di test, vedere gli [unit test in .NET Core e .NET standard](/dotnet/core/testing/) argomento e il contenuto collegato.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-112">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="0ce0f-113">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0ce0f-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0ce0f-114">L'app di esempio è un' Razor app di pagine e presuppone una conoscenza di base delle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-114">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="0ce0f-115">Se non si ha familiarità con Razor le pagine, vedere gli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-115">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="0ce0f-116">[Introduzione alle Razor pagine](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="0ce0f-116">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="0ce0f-117">[Introduzione alle Razor pagine](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="0ce0f-117">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="0ce0f-118">[RazorUnit test di pagine](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="0ce0f-118">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="0ce0f-119">Per il test di Spa è stato consigliato uno strumento come [Selenium](https://www.seleniumhq.org/), che consente di automatizzare un browser.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-119">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="0ce0f-120">Introduzione ai test di integrazione</span><span class="sxs-lookup"><span data-stu-id="0ce0f-120">Introduction to integration tests</span></span>

<span data-ttu-id="0ce0f-121">I test di integrazione valutano i componenti di un'app a un livello più ampio rispetto agli [unit test](/dotnet/core/testing/).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-121">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="0ce0f-122">Gli unit test vengono utilizzati per testare i componenti software isolati, ad esempio i singoli metodi di classe.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-122">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="0ce0f-123">I test di integrazione confermano che due o più componenti dell'app interagiscono per produrre un risultato previsto, eventualmente includendo tutti i componenti necessari per elaborare completamente una richiesta.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-123">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="0ce0f-124">Questi test più ampi vengono usati per testare l'infrastruttura dell'app e l'intero Framework, spesso inclusi i componenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-124">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="0ce0f-125">Database</span><span class="sxs-lookup"><span data-stu-id="0ce0f-125">Database</span></span>
* <span data-ttu-id="0ce0f-126">File system</span><span class="sxs-lookup"><span data-stu-id="0ce0f-126">File system</span></span>
* <span data-ttu-id="0ce0f-127">Dispositivi di rete</span><span class="sxs-lookup"><span data-stu-id="0ce0f-127">Network appliances</span></span>
* <span data-ttu-id="0ce0f-128">Pipeline richiesta-risposta</span><span class="sxs-lookup"><span data-stu-id="0ce0f-128">Request-response pipeline</span></span>

<span data-ttu-id="0ce0f-129">Gli unit test utilizzano componenti fabbricati, noti come *Fakes* o *oggetti fittizi*, al posto dei componenti dell'infrastruttura.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-129">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="0ce0f-130">Diversamente dagli unit test, i test di integrazione:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-130">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="0ce0f-131">Usare i componenti effettivi usati dall'app nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-131">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="0ce0f-132">Richiedere più codice e l'elaborazione dei dati.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-132">Require more code and data processing.</span></span>
* <span data-ttu-id="0ce0f-133">Richiedere più tempo per l'esecuzione.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-133">Take longer to run.</span></span>

<span data-ttu-id="0ce0f-134">Pertanto, limitare l'utilizzo dei test di integrazione ai più importanti scenari di infrastruttura.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-134">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="0ce0f-135">Se è possibile testare un comportamento utilizzando un unit test o un test di integrazione, scegliere il unit test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-135">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="0ce0f-136">Non scrivere test di integrazione per ogni possibile permutazione di dati e accesso ai file con database e file System.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-136">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="0ce0f-137">Indipendentemente dal numero di posizioni in un'app che interagiscono con i database e i file System, un set mirato di test di integrazione di lettura, scrittura, aggiornamento ed eliminazione è in genere in grado di testare adeguatamente i componenti di database e file system.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-137">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="0ce0f-138">Usare gli unit test per i test di routine della logica del metodo che interagiscono con questi componenti.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-138">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="0ce0f-139">Negli unit test, l'uso di Fakes/Mocks dell'infrastruttura comporta una maggiore esecuzione dei test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-139">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="0ce0f-140">Nelle discussioni sui test di integrazione, il progetto testato viene spesso definito *sistema*sottoposto a test oppure "Sut" per brevità.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-140">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="0ce0f-141">*"SUT" viene usato in questo argomento per fare riferimento all'app ASP.NET Core testata.*</span><span class="sxs-lookup"><span data-stu-id="0ce0f-141">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="0ce0f-142">Test di integrazione di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0ce0f-142">ASP.NET Core integration tests</span></span>

<span data-ttu-id="0ce0f-143">I test di integrazione in ASP.NET Core richiedono gli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-143">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="0ce0f-144">Un progetto di test viene utilizzato per contenere ed eseguire i test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-144">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="0ce0f-145">Il progetto di test contiene un riferimento a SUT.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-145">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="0ce0f-146">Il progetto di test crea un host Web di test per SUT e usa un client del server di prova per gestire le richieste e le risposte con SUT.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-146">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="0ce0f-147">Viene usato un test runner per eseguire i test e segnalare i risultati del test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-147">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="0ce0f-148">I test di integrazione seguono una sequenza di eventi che includono i normali passi del test *Arrange*, *Act*e *Assert* :</span><span class="sxs-lookup"><span data-stu-id="0ce0f-148">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="0ce0f-149">L'host Web di SUT è configurato.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-149">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="0ce0f-150">Viene creato un client del server di prova per inviare richieste all'app.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-150">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="0ce0f-151">Il passaggio di *disposizione* del test viene eseguito: l'app di test prepara una richiesta.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-151">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="0ce0f-152">Viene eseguito il passaggio del test *Act* : il client invia la richiesta e riceve la risposta.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-152">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="0ce0f-153">Viene eseguito il passaggio del test *Assert* : la risposta *effettiva* viene convalidata come *superato* o *non superato* in base a una risposta *prevista* .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-153">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="0ce0f-154">Il processo continua fino a quando non vengono eseguiti tutti i test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-154">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="0ce0f-155">Vengono restituiti i risultati del test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-155">The test results are reported.</span></span>

<span data-ttu-id="0ce0f-156">In genere, l'host Web di test viene configurato in modo diverso rispetto al normale host web dell'app per le esecuzioni dei test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-156">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="0ce0f-157">Ad esempio, è possibile usare un database diverso o impostazioni di app diverse per i test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-157">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="0ce0f-158">I componenti dell'infrastruttura, ad esempio l'host Web di test e il server di prova in memoria ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), vengono forniti o gestiti dal pacchetto [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-158">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="0ce0f-159">L'utilizzo di questo pacchetto semplifica la creazione e l'esecuzione di test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-159">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="0ce0f-160">Il `Microsoft.AspNetCore.Mvc.Testing` pacchetto gestisce le attività seguenti:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-160">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="0ce0f-161">Copia il file delle dipendenze (*. Deps*) da SUT nella directory *bin* del progetto di test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-161">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="0ce0f-162">Imposta la [radice del contenuto](xref:fundamentals/index#content-root) sulla radice del progetto di Sut in modo che i file e le pagine e le visualizzazioni statiche vengano rilevati durante l'esecuzione dei test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-162">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="0ce0f-163">Fornisce la classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) per semplificare l'avvio del SUT con `TestServer` .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-163">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="0ce0f-164">Nella documentazione relativa agli [unit test](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) viene descritto come configurare un progetto di test e un test runner, oltre a istruzioni dettagliate su come eseguire i test e le indicazioni su come denominare i test e le classi di test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-164">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="0ce0f-165">Quando si crea un progetto di test per un'app, separare gli unit test dai test di integrazione in progetti diversi.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-165">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="0ce0f-166">Ciò consente di garantire che i componenti di test dell'infrastruttura non vengano accidentalmente inclusi negli unit test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-166">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="0ce0f-167">La separazione dei test di unità e integrazione consente inoltre di controllare il set di test da eseguire.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-167">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="0ce0f-168">Non esiste praticamente alcuna differenza tra la configurazione per i test delle app per le Razor pagine e le app MVC.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-168">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="0ce0f-169">L'unica differenza consiste nel modo in cui i test vengono denominati.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-169">The only difference is in how the tests are named.</span></span> <span data-ttu-id="0ce0f-170">In un' Razor app pagine, i test degli endpoint della pagina vengono in genere denominati dopo la classe del modello di pagina, ad esempio per `IndexPageTests` testare l'integrazione dei componenti per la pagina di indice.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-170">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="0ce0f-171">In un'app MVC i test sono in genere organizzati per classi controller e denominati dopo i controller testati, ad esempio per `HomeControllerTests` testare l'integrazione dei componenti per il controller Home.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-171">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="0ce0f-172">Test prerequisiti app</span><span class="sxs-lookup"><span data-stu-id="0ce0f-172">Test app prerequisites</span></span>

<span data-ttu-id="0ce0f-173">Il progetto di test deve:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-173">The test project must:</span></span>

* <span data-ttu-id="0ce0f-174">Fare riferimento al pacchetto [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-174">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="0ce0f-175">Specificare il Web SDK nel file di progetto ( `<Project Sdk="Microsoft.NET.Sdk.Web">` ).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-175">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="0ce0f-176">Questi prerequisiti possono essere visualizzati nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-176">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="0ce0f-177">Esaminare il file *tests/RazorPagesProject. tests/RazorPagesProject. tests. csproj* .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-177">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="0ce0f-178">L'app di esempio usa il Framework di test [xUnit](https://xunit.github.io/) e la libreria del parser [AngleSharp](https://anglesharp.github.io/) , quindi l'app di esempio fa riferimento anche a:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-178">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="0ce0f-179">xUnit</span><span class="sxs-lookup"><span data-stu-id="0ce0f-179">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="0ce0f-180">xUnit. Runner. VisualStudio</span><span class="sxs-lookup"><span data-stu-id="0ce0f-180">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="0ce0f-181">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="0ce0f-181">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="0ce0f-182">Entity Framework Core viene inoltre usato nei test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-182">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="0ce0f-183">L'app fa riferimento a:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-183">The app references:</span></span>

* [<span data-ttu-id="0ce0f-184">Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="0ce0f-184">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* <span data-ttu-id="0ce0f-185">[Microsoft Identity . AspNetCore. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="0ce0f-185">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span></span>
* [<span data-ttu-id="0ce0f-186">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="0ce0f-186">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="0ce0f-187">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="0ce0f-187">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="0ce0f-188">Microsoft. EntityFrameworkCore. Tools</span><span class="sxs-lookup"><span data-stu-id="0ce0f-188">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="0ce0f-189">Ambiente SUT</span><span class="sxs-lookup"><span data-stu-id="0ce0f-189">SUT environment</span></span>

<span data-ttu-id="0ce0f-190">Se l' [ambiente](xref:fundamentals/environments) di Sut non è impostato, il valore predefinito dell'ambiente è Development.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-190">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="0ce0f-191">Test di base con WebApplicationFactory predefinito</span><span class="sxs-lookup"><span data-stu-id="0ce0f-191">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="0ce0f-192">[WebApplicationFactory \< TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) viene usato per creare un [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) per i test di integrazione.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-192">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="0ce0f-193">`TEntryPoint`è la classe del punto di ingresso di SUT, in genere la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-193">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="0ce0f-194">Le classi di test implementano una *classe fixture* Interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) per indicare che la classe contiene test e fornisce istanze di oggetti condivisi tra i test nella classe.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-194">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="0ce0f-195">La classe di test seguente, `BasicTests` , USA `WebApplicationFactory` per avviare il SUT e fornire un [HttpClient](/dotnet/api/system.net.http.httpclient) a un metodo di test, `Get_EndpointsReturnSuccessAndCorrectContentType` .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-195">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="0ce0f-196">Il metodo controlla se il codice di stato della risposta ha esito positivo (codici di stato nell'intervallo 200-299) e l' `Content-Type` intestazione è `text/html; charset=utf-8` per diverse pagine dell'app.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-196">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="0ce0f-197">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) crea un'istanza di `HttpClient` che segue automaticamente i reindirizzamenti e gestisce i cookie.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-197">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="0ce0f-198">Per impostazione predefinita, i cookie non essenziali non vengono conservati nelle richieste quando i [criteri di consenso GDPR](xref:security/gdpr) sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-198">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="0ce0f-199">Per mantenere cookie non essenziali, ad esempio quelli usati dal provider TempData, contrassegnarli come essenziali nei test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-199">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="0ce0f-200">Per istruzioni su come contrassegnare un cookie come essenziale, vedere [cookie essenziali](xref:security/gdpr#essential-cookies).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-200">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="0ce0f-201">Personalizzare WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="0ce0f-201">Customize WebApplicationFactory</span></span>

<span data-ttu-id="0ce0f-202">La configurazione dell'host Web può essere creata indipendentemente dalle classi di test ereditando da `WebApplicationFactory` per creare una o più factory personalizzate:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-202">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="0ce0f-203">Ereditare da `WebApplicationFactory` ed eseguire l'override di [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-203">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="0ce0f-204">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) consente la configurazione della raccolta di servizi con [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span><span class="sxs-lookup"><span data-stu-id="0ce0f-204">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="0ce0f-205">Il seeding del database nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) viene eseguito dal `InitializeDbForTests` metodo.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-205">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="0ce0f-206">Il metodo è descritto nella sezione esempio di test di [integrazione: organizzazione di app di test](#test-app-organization) .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-206">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="0ce0f-207">Il contesto di database di SUT viene registrato nel relativo `Startup.ConfigureServices` metodo.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-207">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0ce0f-208">Il callback dell'app di test `builder.ConfigureServices` viene eseguito *dopo* l'esecuzione del codice dell'app `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-208">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="0ce0f-209">L'ordine di esecuzione è una modifica di rilievo per l' [host generico](xref:fundamentals/host/generic-host) con la versione di ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-209">The execution order is a breaking change for the [Generic Host](xref:fundamentals/host/generic-host) with the release of ASP.NET Core 3.0.</span></span> <span data-ttu-id="0ce0f-210">Per usare un database diverso per i test rispetto al database dell'app, è necessario sostituire il contesto di database dell'app in `builder.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-210">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="0ce0f-211">Per SUTs che usano ancora [host Web} (xrif: Fundamentals/host/Web-host), il callback dell'app di test `builder.ConfigureServices` viene eseguito *prima* del codice di Sut `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-211">For SUTs that still use the [Web Host}(xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="0ce0f-212">Il callback dell'app di test `builder.ConfigureTestServices` viene eseguito *dopo*.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-212">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

   <span data-ttu-id="0ce0f-213">L'app di esempio trova il descrittore del servizio per il contesto del database e usa il descrittore per rimuovere la registrazione del servizio.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-213">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="0ce0f-214">Successivamente, la Factory aggiunge un nuovo `ApplicationDbContext` che usa un database in memoria per i test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-214">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="0ce0f-215">Per connettersi a un database diverso dal database in memoria, modificare la `UseInMemoryDatabase` chiamata per connettere il contesto a un database diverso.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-215">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="0ce0f-216">Per utilizzare un database di test SQL Server:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-216">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="0ce0f-217">Fare riferimento al pacchetto NuGet [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-217">Reference the [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="0ce0f-218">Chiamare `UseSqlServer` con una stringa di connessione al database.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-218">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="0ce0f-219">Usare l'oggetto personalizzato `CustomWebApplicationFactory` nelle classi di test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-219">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="0ce0f-220">Nell'esempio seguente viene usata la Factory nella `IndexPageTests` classe:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-220">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="0ce0f-221">Il client dell'app di esempio è configurato per impedire l'esecuzione dei `HttpClient` reindirizzamenti seguenti.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-221">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="0ce0f-222">Come illustrato più avanti nella sezione [autenticazione fittizia](#mock-authentication) , questo consente ai test di verificare il risultato della prima risposta dell'app.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-222">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="0ce0f-223">La prima risposta è un reindirizzamento in molti di questi test con un' `Location` intestazione.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-223">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="0ce0f-224">Un test tipico usa i `HttpClient` metodi helper e per elaborare la richiesta e la risposta:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-224">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="0ce0f-225">Qualsiasi richiesta POST a SUT deve soddisfare il controllo antifalsificazione creato automaticamente dal [sistema antifalsificazione di protezione dei dati](xref:security/data-protection/introduction)dell'app.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-225">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="0ce0f-226">Per disporre la richiesta POST di un test, l'app di test deve:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-226">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="0ce0f-227">Effettuare una richiesta per la pagina.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-227">Make a request for the page.</span></span>
1. <span data-ttu-id="0ce0f-228">Analizzare il cookie antifalsificazione e richiedere il token di convalida dalla risposta.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-228">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="0ce0f-229">Eseguire la richiesta POST con il cookie antifalsificazione e il token di convalida della richiesta sul posto.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-229">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="0ce0f-230">I `SendAsync` metodi di estensione Helper (*Helper/Metodo HttpClientExtensions. cs*) e il `GetDocumentAsync` metodo helper (*Helper/htmlhelpers. cs*) nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) usano il parser [AngleSharp](https://anglesharp.github.io/) per gestire il controllo antifalsificazione con i metodi seguenti:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-230">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="0ce0f-231">`GetDocumentAsync`&ndash;Riceve il [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) e restituisce un oggetto `IHtmlDocument` .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-231">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="0ce0f-232">`GetDocumentAsync`Usa una factory che prepara una *risposta virtuale* basata sull'originale `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-232">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="0ce0f-233">Per ulteriori informazioni, vedere la [documentazione di AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-233">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="0ce0f-234">`SendAsync`i metodi di estensione per `HttpClient` compongono un [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) e chiamano [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) per inviare richieste a SUT.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-234">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="0ce0f-235">Gli overload per `SendAsync` accettano il formato HTML ( `IHtmlFormElement` ) e gli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-235">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="0ce0f-236">Pulsante Invia nel formato ( `IHtmlElement` )</span><span class="sxs-lookup"><span data-stu-id="0ce0f-236">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="0ce0f-237">Raccolta di valori di form ( `IEnumerable<KeyValuePair<string, string>>` )</span><span class="sxs-lookup"><span data-stu-id="0ce0f-237">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="0ce0f-238">Pulsante Invia ( `IHtmlElement` ) e valori form ( `IEnumerable<KeyValuePair<string, string>>` )</span><span class="sxs-lookup"><span data-stu-id="0ce0f-238">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="0ce0f-239">[AngleSharp](https://anglesharp.github.io/) è una libreria di analisi di terze parti usata a scopo dimostrativo in questo argomento e nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-239">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="0ce0f-240">AngleSharp non è supportato o richiesto per i test di integrazione delle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-240">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="0ce0f-241">È possibile usare altri parser, ad esempio [HTML Agility Pack (HAP)](https://html-agility-pack.net/).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-241">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="0ce0f-242">Un altro approccio consiste nel scrivere codice per gestire direttamente il token di verifica delle richieste del sistema antifalsificazione e il cookie antifalsificazione.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-242">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="0ce0f-243">Personalizzare il client con WithWebHostBuilder</span><span class="sxs-lookup"><span data-stu-id="0ce0f-243">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="0ce0f-244">Quando è richiesta una configurazione aggiuntiva in un metodo di test, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) crea un nuovo oggetto `WebApplicationFactory` con un [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) che viene ulteriormente personalizzato in base alla configurazione.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-244">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="0ce0f-245">Il `Post_DeleteMessageHandler_ReturnsRedirectToRoot` metodo di test dell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) illustra l'uso di `WithWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-245">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="0ce0f-246">Questo test esegue l'eliminazione di un record nel database attivando un invio di form in SUT.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-246">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="0ce0f-247">Poiché un altro test della `IndexPageTests` classe esegue un'operazione che elimina tutti i record nel database e può essere eseguita prima del `Post_DeleteMessageHandler_ReturnsRedirectToRoot` metodo, il database viene reinizializzato in questo metodo di test per assicurarsi che sia presente un record per il SUT da eliminare.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-247">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="0ce0f-248">La selezione del primo pulsante Elimina del `messages` modulo in SUT viene simulata nella richiesta al SUT:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-248">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="0ce0f-249">Opzioni client</span><span class="sxs-lookup"><span data-stu-id="0ce0f-249">Client options</span></span>

<span data-ttu-id="0ce0f-250">Nella tabella seguente viene illustrato il valore predefinito di [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) disponibile durante la creazione di `HttpClient` istanze.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-250">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="0ce0f-251">Opzione</span><span class="sxs-lookup"><span data-stu-id="0ce0f-251">Option</span></span> | <span data-ttu-id="0ce0f-252">Descrizione</span><span class="sxs-lookup"><span data-stu-id="0ce0f-252">Description</span></span> | <span data-ttu-id="0ce0f-253">Predefinito</span><span class="sxs-lookup"><span data-stu-id="0ce0f-253">Default</span></span> |
| ---
<span data-ttu-id="0ce0f-254">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-255">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-256">'Identity'</span></span>
- <span data-ttu-id="0ce0f-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-258">'Razor'</span></span>
- <span data-ttu-id="0ce0f-259">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-259">'SignalR' uid:</span></span> 

<span data-ttu-id="0ce0f-260">--- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-260">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-261">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-262">'Identity'</span></span>
- <span data-ttu-id="0ce0f-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-264">'Razor'</span></span>
- <span data-ttu-id="0ce0f-265">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-266">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-267">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-268">'Identity'</span></span>
- <span data-ttu-id="0ce0f-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-270">'Razor'</span></span>
- <span data-ttu-id="0ce0f-271">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-272">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-273">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-274">'Identity'</span></span>
- <span data-ttu-id="0ce0f-275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-275">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-276">'Razor'</span></span>
- <span data-ttu-id="0ce0f-277">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-277">'SignalR' uid:</span></span> 

<span data-ttu-id="0ce0f-278">------ | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-278">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-279">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-280">'Identity'</span></span>
- <span data-ttu-id="0ce0f-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-282">'Razor'</span></span>
- <span data-ttu-id="0ce0f-283">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-283">'SignalR' uid:</span></span> 

<span data-ttu-id="0ce0f-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Ottiene o imposta un valore che indica se le `HttpClient` istanze devono seguire automaticamente le risposte di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="0ce0f-285"> | `true`| | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Ottiene o imposta l'indirizzo di base delle `HttpClient` istanze di.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-285"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="0ce0f-286"> | `http://localhost`| | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Ottiene o imposta un valore che indica se le `HttpClient` istanze devono gestire i cookie.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-286"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="0ce0f-287"> | `true`| | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Ottiene o imposta il numero massimo di risposte di reindirizzamento che le `HttpClient` istanze devono seguire.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-287"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="0ce0f-288">| 7 |</span><span class="sxs-lookup"><span data-stu-id="0ce0f-288">| 7 |</span></span>

<span data-ttu-id="0ce0f-289">Creare la `WebApplicationFactoryClientOptions` classe e passarla al metodo [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (i valori predefiniti sono mostrati nell'esempio di codice):</span><span class="sxs-lookup"><span data-stu-id="0ce0f-289">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="0ce0f-290">Inserire i servizi fittizi</span><span class="sxs-lookup"><span data-stu-id="0ce0f-290">Inject mock services</span></span>

<span data-ttu-id="0ce0f-291">È possibile eseguire l'override dei servizi in un test con una chiamata a [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) nel generatore host.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-291">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="0ce0f-292">**Per inserire i servizi fittizi, SUT deve disporre di una `Startup` classe con un `Startup.ConfigureServices` metodo.**</span><span class="sxs-lookup"><span data-stu-id="0ce0f-292">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="0ce0f-293">L'esempio SUT include un servizio con ambito che restituisce un'offerta.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-293">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="0ce0f-294">La virgoletta è incorporata in un campo nascosto nella pagina di indice quando viene richiesta la pagina di indice.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-294">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="0ce0f-295">*Services/IQuoteService. cs*:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-295">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="0ce0f-296">*Services/QuoteService. cs*:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-296">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="0ce0f-297">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-297">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="0ce0f-298">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-298">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="0ce0f-299">*Pages/index. cs*:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-299">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="0ce0f-300">Quando viene eseguita l'app SUT, viene generato il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-300">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="0ce0f-301">Per testare il servizio e l'inserimento di virgolette in un test di integrazione, un servizio fittizio viene inserito in SUT dal test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-301">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="0ce0f-302">Il servizio fittizio sostituisce l'app `QuoteService` con un servizio fornito dall'app di test, denominato `TestQuoteService` :</span><span class="sxs-lookup"><span data-stu-id="0ce0f-302">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="0ce0f-303">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-303">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="0ce0f-304">`ConfigureTestServices`viene chiamato e il servizio con ambito viene registrato:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-304">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="0ce0f-305">Il markup prodotto durante l'esecuzione del test riflette il testo tra virgolette fornito da `TestQuoteService` , quindi l'asserzione passa:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-305">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="0ce0f-306">Autenticazione fittizia</span><span class="sxs-lookup"><span data-stu-id="0ce0f-306">Mock authentication</span></span>

<span data-ttu-id="0ce0f-307">I test della `AuthTests` classe verificano che un endpoint protetto:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-307">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="0ce0f-308">Reindirizza un utente non autenticato alla pagina di accesso dell'app.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-308">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="0ce0f-309">Restituisce il contenuto per un utente autenticato.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-309">Returns content for an authenticated user.</span></span>

<span data-ttu-id="0ce0f-310">In SUT la `/SecurePage` pagina usa una convenzione [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) per applicare un [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) alla pagina.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-310">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="0ce0f-311">Per ulteriori informazioni, vedere la pagina relativa alle [ Razor convenzioni di autorizzazione](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-311">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="0ce0f-312">Nel `Get_SecurePageRedirectsAnUnauthenticatedUser` test un [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) è impostato in modo da non consentire i reindirizzamenti impostando [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) su `false` :</span><span class="sxs-lookup"><span data-stu-id="0ce0f-312">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="0ce0f-313">Se si impedisce al client di seguire il reindirizzamento, è possibile effettuare i controlli seguenti:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-313">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="0ce0f-314">Il codice di stato restituito da SUT può essere controllato rispetto al risultato [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) previsto, non al codice di stato finale dopo il reindirizzamento alla pagina di accesso, che sarebbe [HttpStatusCode. OK](/dotnet/api/system.net.httpstatuscode).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-314">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="0ce0f-315">Il `Location` valore dell'intestazione nelle intestazioni della risposta viene controllato per confermare che inizia con `http://localhost/Identity/Account/Login` , non con la risposta finale della pagina di accesso, in cui l' `Location` intestazione non è presente.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-315">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="0ce0f-316">L'app di test può simulare un <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) per testare gli aspetti dell'autenticazione e dell'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-316">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="0ce0f-317">Uno scenario minimo restituisce [AuthenticateResult. Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span><span class="sxs-lookup"><span data-stu-id="0ce0f-317">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="0ce0f-318">Il metodo `TestAuthHandler` viene chiamato per autenticare un utente quando lo schema di autenticazione è impostato su `Test` dove `AddAuthentication` è registrato per `ConfigureTestServices` :</span><span class="sxs-lookup"><span data-stu-id="0ce0f-318">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="0ce0f-319">Per ulteriori informazioni su `WebApplicationFactoryClientOptions` , vedere la sezione [Opzioni client](#client-options) .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-319">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="0ce0f-320">Impostare l'ambiente</span><span class="sxs-lookup"><span data-stu-id="0ce0f-320">Set the environment</span></span>

<span data-ttu-id="0ce0f-321">Per impostazione predefinita, l'host e l'ambiente app di SUT sono configurati per l'utilizzo dell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-321">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="0ce0f-322">Per eseguire l'override dell'ambiente di SUT quando si usa `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="0ce0f-322">To override the SUT's environment when using `IHostBuilder`:</span></span>

* <span data-ttu-id="0ce0f-323">Impostare la `ASPNETCORE_ENVIRONMENT` variabile di ambiente (ad esempio,, `Staging` `Production` o un altro valore personalizzato, ad esempio `Testing` ).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-323">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="0ce0f-324">Eseguire l'override `CreateHostBuilder` nell'app di test per leggere le variabili di ambiente con prefisso `ASPNETCORE` .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-324">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() =>
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

<span data-ttu-id="0ce0f-325">Se SUT utilizza l'host Web ( `IWebHostBuilder` ), eseguire l'override di `CreateWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="0ce0f-325">If the SUT uses the Web Host (`IWebHostBuilder`), override `CreateWebHostBuilder`:</span></span>

```csharp
protected override IWebHostBuilder CreateWebHostBuilder() =>
    base.CreateWebHostBuilder().UseEnvironment("Testing");
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="0ce0f-326">Come l'infrastruttura di test deduce il percorso radice del contenuto dell'app</span><span class="sxs-lookup"><span data-stu-id="0ce0f-326">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="0ce0f-327">Il `WebApplicationFactory` Costruttore deduce il percorso [radice del contenuto](xref:fundamentals/index#content-root) dell'app cercando un [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) nell'assembly contenente i test di integrazione con una chiave uguale all' `TEntryPoint` assembly `System.Reflection.Assembly.FullName` .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-327">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="0ce0f-328">Se non viene trovato un attributo con la chiave corretta, viene `WebApplicationFactory` eseguito il fallback alla ricerca di un file di soluzione (con*estensione sln*) e il nome dell'assembly viene aggiunto `TEntryPoint` alla directory della soluzione.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-328">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="0ce0f-329">La directory radice dell'app (percorso radice del contenuto) viene usata per individuare le visualizzazioni e i file di contenuto.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-329">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="0ce0f-330">Disabilitare la copia shadow</span><span class="sxs-lookup"><span data-stu-id="0ce0f-330">Disable shadow copying</span></span>

<span data-ttu-id="0ce0f-331">La copia shadow comporta l'esecuzione dei test in una directory diversa rispetto alla directory di output.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-331">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="0ce0f-332">Per il corretto funzionamento dei test, è necessario disabilitare la copia shadow.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-332">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="0ce0f-333">L' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) USA xUnit e Disabilita la copia shadow per xUnit includendo un file *xUnit. Runner. JSON* con l'impostazione di configurazione corretta.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-333">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="0ce0f-334">Per altre informazioni, vedere [configurazione di xUnit con JSON](https://xunit.github.io/docs/configuring-with-json.html).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-334">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="0ce0f-335">Aggiungere il file *xUnit. Runner. JSON* alla radice del progetto di test con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-335">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="0ce0f-336">Eliminazione di oggetti</span><span class="sxs-lookup"><span data-stu-id="0ce0f-336">Disposal of objects</span></span>

<span data-ttu-id="0ce0f-337">Dopo l'esecuzione dei test dell' `IClassFixture` implementazione, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) e [HttpClient](/dotnet/api/system.net.http.httpclient) vengono eliminati quando xUnit Elimina [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-337">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="0ce0f-338">Se gli oggetti di cui è stata creata un'istanza dallo sviluppatore richiedono l'eliminazione, eliminarli nell' `IClassFixture` implementazione.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-338">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="0ce0f-339">Per ulteriori informazioni, vedere [implementazione di un metodo Dispose](/dotnet/standard/garbage-collection/implementing-dispose).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-339">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="0ce0f-340">Esempio di test di integrazione</span><span class="sxs-lookup"><span data-stu-id="0ce0f-340">Integration tests sample</span></span>

<span data-ttu-id="0ce0f-341">L' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) è costituita da due app:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-341">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="0ce0f-342">App</span><span class="sxs-lookup"><span data-stu-id="0ce0f-342">App</span></span> | <span data-ttu-id="0ce0f-343">Directory del progetto</span><span class="sxs-lookup"><span data-stu-id="0ce0f-343">Project directory</span></span> | <span data-ttu-id="0ce0f-344">Descrizione</span><span class="sxs-lookup"><span data-stu-id="0ce0f-344">Description</span></span> |
| --- | ---
<span data-ttu-id="0ce0f-345">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-346">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-347">'Identity'</span></span>
- <span data-ttu-id="0ce0f-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-349">'Razor'</span></span>
- <span data-ttu-id="0ce0f-350">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-351">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-352">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-353">'Identity'</span></span>
- <span data-ttu-id="0ce0f-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-355">'Razor'</span></span>
- <span data-ttu-id="0ce0f-356">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-357">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-358">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-359">'Identity'</span></span>
- <span data-ttu-id="0ce0f-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-361">'Razor'</span></span>
- <span data-ttu-id="0ce0f-362">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-363">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-364">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-365">'Identity'</span></span>
- <span data-ttu-id="0ce0f-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-367">'Razor'</span></span>
- <span data-ttu-id="0ce0f-368">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-369">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-370">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-371">'Identity'</span></span>
- <span data-ttu-id="0ce0f-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-373">'Razor'</span></span>
- <span data-ttu-id="0ce0f-374">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-375">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-376">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-377">'Identity'</span></span>
- <span data-ttu-id="0ce0f-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-379">'Razor'</span></span>
- <span data-ttu-id="0ce0f-380">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-380">'SignalR' uid:</span></span> 

<span data-ttu-id="0ce0f-381">--------- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-381">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-382">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-383">'Identity'</span></span>
- <span data-ttu-id="0ce0f-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-385">'Razor'</span></span>
- <span data-ttu-id="0ce0f-386">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-387">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-388">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-389">'Identity'</span></span>
- <span data-ttu-id="0ce0f-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-391">'Razor'</span></span>
- <span data-ttu-id="0ce0f-392">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-393">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-394">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-395">'Identity'</span></span>
- <span data-ttu-id="0ce0f-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-397">'Razor'</span></span>
- <span data-ttu-id="0ce0f-398">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-398">'SignalR' uid:</span></span> 

<span data-ttu-id="0ce0f-399">------ | | App Message (SUT) | *src/RazorPagesProject* | Consente a un utente di aggiungere, eliminare, eliminare tutti i messaggi e analizzarli.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-399">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="0ce0f-400">| | App di test | *test/RazorPagesProject. test* | Utilizzato per eseguire il test di integrazione di SUT.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-400">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="0ce0f-401">I test possono essere eseguiti usando le funzionalità di test predefinite di un IDE, ad esempio [Visual Studio](https://visualstudio.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-401">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="0ce0f-402">Se si usa [Visual Studio Code](https://code.visualstudio.com/) o la riga di comando, eseguire il comando seguente al prompt dei comandi nella directory *tests/RazorPagesProject. tests* :</span><span class="sxs-lookup"><span data-stu-id="0ce0f-402">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="0ce0f-403">Organizzazione Message app (SUT)</span><span class="sxs-lookup"><span data-stu-id="0ce0f-403">Message app (SUT) organization</span></span>

<span data-ttu-id="0ce0f-404">SUT è un Razor sistema di messaggi di pagine con le seguenti caratteristiche:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-404">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="0ce0f-405">La pagina di indice dell'app (*pages/index. cshtml* e *pages/index. cshtml. cs*) fornisce i metodi di interfaccia utente e modello di pagina per controllare l'aggiunta, l'eliminazione e l'analisi dei messaggi (media parole per messaggio).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-405">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="0ce0f-406">Un messaggio viene descritto dalla `Message` classe (*Data/Message. cs*) con due proprietà: `Id` (chiave) e `Text` (messaggio).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-406">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="0ce0f-407">La `Text` proprietà è obbligatoria e limitata a 200 caratteri.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-407">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="0ce0f-408">I messaggi vengono archiviati utilizzando&#8224; [di database in memoria di Entity Framework](/ef/core/providers/in-memory/) .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-408">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="0ce0f-409">L'app contiene un livello di accesso ai dati (DAL) nella classe del contesto di database, `AppDbContext` (*Data/AppDbContext. cs*).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-409">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="0ce0f-410">Se il database è vuoto all'avvio dell'app, l'archivio messaggi viene inizializzato con tre messaggi.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-410">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="0ce0f-411">L'app include un oggetto a `/SecurePage` cui è possibile accedere solo da un utente autenticato.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-411">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="0ce0f-412">&#8224;argomento EF, [test con InMemory](/ef/core/miscellaneous/testing/in-memory), spiega come usare un database in memoria per i test con MSTest.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-412">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="0ce0f-413">In questo argomento viene usato il Framework di test di [xUnit](https://xunit.github.io/) .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-413">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="0ce0f-414">I concetti di test e le implementazioni di test in diversi framework di test sono simili, ma non identici.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-414">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="0ce0f-415">Anche se l'app non usa il modello di repository e non è un esempio efficace del [modello di unità di lavoro (UOW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), le Razor pagine supportano questi modelli di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-415">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="0ce0f-416">Per altre informazioni, vedere [progettazione del livello di persistenza dell'infrastruttura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) e della [logica del controller di test](/aspnet/core/mvc/controllers/testing) (l'esempio implementa il modello di repository).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-416">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="0ce0f-417">Testare l'organizzazione dell'app</span><span class="sxs-lookup"><span data-stu-id="0ce0f-417">Test app organization</span></span>

<span data-ttu-id="0ce0f-418">L'app di test è un'app console all'interno della directory *tests/RazorPagesProject. tests* .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-418">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="0ce0f-419">Testare la directory dell'app</span><span class="sxs-lookup"><span data-stu-id="0ce0f-419">Test app directory</span></span> | <span data-ttu-id="0ce0f-420">Descrizione</span><span class="sxs-lookup"><span data-stu-id="0ce0f-420">Description</span></span> |
| ---
<span data-ttu-id="0ce0f-421">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-422">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-423">'Identity'</span></span>
- <span data-ttu-id="0ce0f-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-425">'Razor'</span></span>
- <span data-ttu-id="0ce0f-426">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-427">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-428">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-429">'Identity'</span></span>
- <span data-ttu-id="0ce0f-430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-430">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-431">'Razor'</span></span>
- <span data-ttu-id="0ce0f-432">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-433">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-434">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-435">'Identity'</span></span>
- <span data-ttu-id="0ce0f-436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-436">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-437">'Razor'</span></span>
- <span data-ttu-id="0ce0f-438">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-439">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-440">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-441">'Identity'</span></span>
- <span data-ttu-id="0ce0f-442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-442">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-443">'Razor'</span></span>
- <span data-ttu-id="0ce0f-444">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-445">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-446">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-447">'Identity'</span></span>
- <span data-ttu-id="0ce0f-448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-448">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-449">'Razor'</span></span>
- <span data-ttu-id="0ce0f-450">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-451">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-452">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-453">'Identity'</span></span>
- <span data-ttu-id="0ce0f-454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-454">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-455">'Razor'</span></span>
- <span data-ttu-id="0ce0f-456">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-457">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-458">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-459">'Identity'</span></span>
- <span data-ttu-id="0ce0f-460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-461">'Razor'</span></span>
- <span data-ttu-id="0ce0f-462">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-462">'SignalR' uid:</span></span> 

<span data-ttu-id="0ce0f-463">--------- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-463">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-464">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-465">'Identity'</span></span>
- <span data-ttu-id="0ce0f-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-467">'Razor'</span></span>
- <span data-ttu-id="0ce0f-468">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-469">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-470">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-471">'Identity'</span></span>
- <span data-ttu-id="0ce0f-472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-472">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-473">'Razor'</span></span>
- <span data-ttu-id="0ce0f-474">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-475">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-476">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-477">'Identity'</span></span>
- <span data-ttu-id="0ce0f-478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-478">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-479">'Razor'</span></span>
- <span data-ttu-id="0ce0f-480">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-480">'SignalR' uid:</span></span> 

<span data-ttu-id="0ce0f-481">------ | | *AuthTests* | Contiene i metodi di test per:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-481">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="0ce0f-482">Accesso a una pagina sicura da un utente non autenticato.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-482">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="0ce0f-483">Accesso a una pagina sicura da un utente autenticato con una simulazione <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-483">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="0ce0f-484">Ottenere un profilo utente GitHub e controllare l'accesso dell'utente del profilo.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-484">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="0ce0f-485">| | *BasicTests* | Contiene un metodo di test per il routing e il tipo di contenuto.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-485">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="0ce0f-486">| | *IntegrationTests* | Contiene i test di integrazione per la pagina di indice utilizzando la `WebApplicationFactory` classe personalizzata.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-486">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="0ce0f-487">| | *Helper/utilità* | </span><span class="sxs-lookup"><span data-stu-id="0ce0f-487">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="0ce0f-488">*Utilities.cs* contiene il `InitializeDbForTests` metodo utilizzato per eseguire il seeding del database con dati di test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-488">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="0ce0f-489">*HtmlHelpers.cs* fornisce un metodo per restituire un AngleSharp `IHtmlDocument` per l'uso da parte dei metodi di test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-489">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="0ce0f-490">*HttpClientExtensions.cs* forniscono overload per `SendAsync` per inviare richieste a SUT.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-490">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="0ce0f-491">Il Framework di test è [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-491">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="0ce0f-492">I test di integrazione vengono eseguiti usando [Microsoft. AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost), che include [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-492">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="0ce0f-493">Poiché il pacchetto [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) viene usato per configurare l'host di test e il server di prova, i `TestHost` `TestServer` pacchetti e non richiedono riferimenti diretti al pacchetto nel file di progetto dell'app di test o nella configurazione per sviluppatori nell'app di test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-493">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="0ce0f-494">**Seeding del database per il test**</span><span class="sxs-lookup"><span data-stu-id="0ce0f-494">**Seeding the database for testing**</span></span>

<span data-ttu-id="0ce0f-495">I test di integrazione richiedono in genere un set di dati di piccole dimensioni nel database prima dell'esecuzione del test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-495">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="0ce0f-496">Ad esempio, un test di eliminazione richiede l'eliminazione di un record del database, pertanto è necessario che il database disponga di almeno un record affinché la richiesta di eliminazione abbia esito positivo.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-496">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="0ce0f-497">L'app di esempio esegue il seeding del database con tre messaggi in *Utilities.cs* che possono essere usati dai test quando vengono eseguiti:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-497">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="0ce0f-498">Il contesto di database di SUT viene registrato nel relativo `Startup.ConfigureServices` metodo.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-498">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0ce0f-499">Il callback dell'app di test `builder.ConfigureServices` viene eseguito *dopo* l'esecuzione del codice dell'app `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-499">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="0ce0f-500">Per utilizzare un database diverso per i test, è necessario sostituire il contesto di database dell'applicazione in `builder.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-500">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="0ce0f-501">Per ulteriori informazioni, vedere la sezione [Customize WebApplicationFactory](#customize-webapplicationfactory) .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-501">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

<span data-ttu-id="0ce0f-502">Per SUTs che usano ancora [host Web} (xrif: Fundamentals/host/Web-host), il callback dell'app di test `builder.ConfigureServices` viene eseguito *prima* del codice di Sut `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-502">For SUTs that still use the [Web Host}(xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="0ce0f-503">Il callback dell'app di test `builder.ConfigureTestServices` viene eseguito *dopo*.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-503">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0ce0f-504">I test di integrazione assicurano che i componenti di un'app funzionino correttamente a un livello che include l'infrastruttura di supporto dell'app, ad esempio il database, la file system e la rete.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-504">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="0ce0f-505">ASP.NET Core supporta i test di integrazione utilizzando un framework unit test con un host Web di prova e un server di prova in memoria.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-505">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="0ce0f-506">In questo argomento si presuppone una conoscenza di base degli unit test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-506">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="0ce0f-507">Se non si ha familiarità con i concetti di test, vedere gli [unit test in .NET Core e .NET standard](/dotnet/core/testing/) argomento e il contenuto collegato.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-507">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="0ce0f-508">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0ce0f-508">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0ce0f-509">L'app di esempio è un' Razor app di pagine e presuppone una conoscenza di base delle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-509">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="0ce0f-510">Se non si ha familiarità con Razor le pagine, vedere gli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-510">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="0ce0f-511">[Introduzione alle Razor pagine](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="0ce0f-511">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="0ce0f-512">[Introduzione alle Razor pagine](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="0ce0f-512">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="0ce0f-513">[RazorUnit test di pagine](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="0ce0f-513">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="0ce0f-514">Per il test di Spa è stato consigliato uno strumento come [Selenium](https://www.seleniumhq.org/), che consente di automatizzare un browser.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-514">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="0ce0f-515">Introduzione ai test di integrazione</span><span class="sxs-lookup"><span data-stu-id="0ce0f-515">Introduction to integration tests</span></span>

<span data-ttu-id="0ce0f-516">I test di integrazione valutano i componenti di un'app a un livello più ampio rispetto agli [unit test](/dotnet/core/testing/).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-516">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="0ce0f-517">Gli unit test vengono utilizzati per testare i componenti software isolati, ad esempio i singoli metodi di classe.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-517">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="0ce0f-518">I test di integrazione confermano che due o più componenti dell'app interagiscono per produrre un risultato previsto, eventualmente includendo tutti i componenti necessari per elaborare completamente una richiesta.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-518">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="0ce0f-519">Questi test più ampi vengono usati per testare l'infrastruttura dell'app e l'intero Framework, spesso inclusi i componenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-519">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="0ce0f-520">Database</span><span class="sxs-lookup"><span data-stu-id="0ce0f-520">Database</span></span>
* <span data-ttu-id="0ce0f-521">File system</span><span class="sxs-lookup"><span data-stu-id="0ce0f-521">File system</span></span>
* <span data-ttu-id="0ce0f-522">Dispositivi di rete</span><span class="sxs-lookup"><span data-stu-id="0ce0f-522">Network appliances</span></span>
* <span data-ttu-id="0ce0f-523">Pipeline richiesta-risposta</span><span class="sxs-lookup"><span data-stu-id="0ce0f-523">Request-response pipeline</span></span>

<span data-ttu-id="0ce0f-524">Gli unit test utilizzano componenti fabbricati, noti come *Fakes* o *oggetti fittizi*, al posto dei componenti dell'infrastruttura.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-524">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="0ce0f-525">Diversamente dagli unit test, i test di integrazione:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-525">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="0ce0f-526">Usare i componenti effettivi usati dall'app nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-526">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="0ce0f-527">Richiedere più codice e l'elaborazione dei dati.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-527">Require more code and data processing.</span></span>
* <span data-ttu-id="0ce0f-528">Richiedere più tempo per l'esecuzione.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-528">Take longer to run.</span></span>

<span data-ttu-id="0ce0f-529">Pertanto, limitare l'utilizzo dei test di integrazione ai più importanti scenari di infrastruttura.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-529">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="0ce0f-530">Se è possibile testare un comportamento utilizzando un unit test o un test di integrazione, scegliere il unit test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-530">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="0ce0f-531">Non scrivere test di integrazione per ogni possibile permutazione di dati e accesso ai file con database e file System.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-531">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="0ce0f-532">Indipendentemente dal numero di posizioni in un'app che interagiscono con i database e i file System, un set mirato di test di integrazione di lettura, scrittura, aggiornamento ed eliminazione è in genere in grado di testare adeguatamente i componenti di database e file system.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-532">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="0ce0f-533">Usare gli unit test per i test di routine della logica del metodo che interagiscono con questi componenti.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-533">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="0ce0f-534">Negli unit test, l'uso di Fakes/Mocks dell'infrastruttura comporta una maggiore esecuzione dei test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-534">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="0ce0f-535">Nelle discussioni sui test di integrazione, il progetto testato viene spesso definito *sistema*sottoposto a test oppure "Sut" per brevità.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-535">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="0ce0f-536">*"SUT" viene usato in questo argomento per fare riferimento all'app ASP.NET Core testata.*</span><span class="sxs-lookup"><span data-stu-id="0ce0f-536">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="0ce0f-537">Test di integrazione di ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0ce0f-537">ASP.NET Core integration tests</span></span>

<span data-ttu-id="0ce0f-538">I test di integrazione in ASP.NET Core richiedono gli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-538">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="0ce0f-539">Un progetto di test viene utilizzato per contenere ed eseguire i test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-539">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="0ce0f-540">Il progetto di test contiene un riferimento a SUT.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-540">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="0ce0f-541">Il progetto di test crea un host Web di test per SUT e usa un client del server di prova per gestire le richieste e le risposte con SUT.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-541">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="0ce0f-542">Viene usato un test runner per eseguire i test e segnalare i risultati del test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-542">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="0ce0f-543">I test di integrazione seguono una sequenza di eventi che includono i normali passi del test *Arrange*, *Act*e *Assert* :</span><span class="sxs-lookup"><span data-stu-id="0ce0f-543">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="0ce0f-544">L'host Web di SUT è configurato.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-544">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="0ce0f-545">Viene creato un client del server di prova per inviare richieste all'app.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-545">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="0ce0f-546">Il passaggio di *disposizione* del test viene eseguito: l'app di test prepara una richiesta.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-546">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="0ce0f-547">Viene eseguito il passaggio del test *Act* : il client invia la richiesta e riceve la risposta.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-547">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="0ce0f-548">Viene eseguito il passaggio del test *Assert* : la risposta *effettiva* viene convalidata come *superato* o *non superato* in base a una risposta *prevista* .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-548">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="0ce0f-549">Il processo continua fino a quando non vengono eseguiti tutti i test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-549">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="0ce0f-550">Vengono restituiti i risultati del test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-550">The test results are reported.</span></span>

<span data-ttu-id="0ce0f-551">In genere, l'host Web di test viene configurato in modo diverso rispetto al normale host web dell'app per le esecuzioni dei test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-551">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="0ce0f-552">Ad esempio, è possibile usare un database diverso o impostazioni di app diverse per i test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-552">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="0ce0f-553">I componenti dell'infrastruttura, ad esempio l'host Web di test e il server di prova in memoria ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), vengono forniti o gestiti dal pacchetto [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-553">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="0ce0f-554">L'utilizzo di questo pacchetto semplifica la creazione e l'esecuzione di test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-554">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="0ce0f-555">Il `Microsoft.AspNetCore.Mvc.Testing` pacchetto gestisce le attività seguenti:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-555">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="0ce0f-556">Copia il file delle dipendenze (*. Deps*) da SUT nella directory *bin* del progetto di test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-556">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="0ce0f-557">Imposta la [radice del contenuto](xref:fundamentals/index#content-root) sulla radice del progetto di Sut in modo che i file e le pagine e le visualizzazioni statiche vengano rilevati durante l'esecuzione dei test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-557">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="0ce0f-558">Fornisce la classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) per semplificare l'avvio del SUT con `TestServer` .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-558">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="0ce0f-559">Nella documentazione relativa agli [unit test](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) viene descritto come configurare un progetto di test e un test runner, oltre a istruzioni dettagliate su come eseguire i test e le indicazioni su come denominare i test e le classi di test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-559">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="0ce0f-560">Quando si crea un progetto di test per un'app, separare gli unit test dai test di integrazione in progetti diversi.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-560">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="0ce0f-561">Ciò consente di garantire che i componenti di test dell'infrastruttura non vengano accidentalmente inclusi negli unit test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-561">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="0ce0f-562">La separazione dei test di unità e integrazione consente inoltre di controllare il set di test da eseguire.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-562">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="0ce0f-563">Non esiste praticamente alcuna differenza tra la configurazione per i test delle app per le Razor pagine e le app MVC.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-563">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="0ce0f-564">L'unica differenza consiste nel modo in cui i test vengono denominati.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-564">The only difference is in how the tests are named.</span></span> <span data-ttu-id="0ce0f-565">In un' Razor app pagine, i test degli endpoint della pagina vengono in genere denominati dopo la classe del modello di pagina, ad esempio per `IndexPageTests` testare l'integrazione dei componenti per la pagina di indice.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-565">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="0ce0f-566">In un'app MVC i test sono in genere organizzati per classi controller e denominati dopo i controller testati, ad esempio per `HomeControllerTests` testare l'integrazione dei componenti per il controller Home.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-566">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="0ce0f-567">Test prerequisiti app</span><span class="sxs-lookup"><span data-stu-id="0ce0f-567">Test app prerequisites</span></span>

<span data-ttu-id="0ce0f-568">Il progetto di test deve:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-568">The test project must:</span></span>

* <span data-ttu-id="0ce0f-569">Fare riferimento ai pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-569">Reference the following packages:</span></span>
  * [<span data-ttu-id="0ce0f-570">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="0ce0f-570">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="0ce0f-571">Microsoft. AspNetCore. Mvc. testing</span><span class="sxs-lookup"><span data-stu-id="0ce0f-571">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="0ce0f-572">Specificare il Web SDK nel file di progetto ( `<Project Sdk="Microsoft.NET.Sdk.Web">` ).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-572">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="0ce0f-573">Il Web SDK è obbligatorio quando si fa riferimento al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-573">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="0ce0f-574">Questi prerequisiti possono essere visualizzati nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-574">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="0ce0f-575">Esaminare il file *tests/RazorPagesProject. tests/RazorPagesProject. tests. csproj* .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-575">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="0ce0f-576">L'app di esempio usa il Framework di test [xUnit](https://xunit.github.io/) e la libreria del parser [AngleSharp](https://anglesharp.github.io/) , quindi l'app di esempio fa riferimento anche a:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-576">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="0ce0f-577">xUnit</span><span class="sxs-lookup"><span data-stu-id="0ce0f-577">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="0ce0f-578">xUnit. Runner. VisualStudio</span><span class="sxs-lookup"><span data-stu-id="0ce0f-578">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="0ce0f-579">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="0ce0f-579">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="0ce0f-580">Ambiente SUT</span><span class="sxs-lookup"><span data-stu-id="0ce0f-580">SUT environment</span></span>

<span data-ttu-id="0ce0f-581">Se l' [ambiente](xref:fundamentals/environments) di Sut non è impostato, il valore predefinito dell'ambiente è Development.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-581">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="0ce0f-582">Test di base con WebApplicationFactory predefinito</span><span class="sxs-lookup"><span data-stu-id="0ce0f-582">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="0ce0f-583">[WebApplicationFactory \< TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) viene usato per creare un [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) per i test di integrazione.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-583">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="0ce0f-584">`TEntryPoint`è la classe del punto di ingresso di SUT, in genere la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-584">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="0ce0f-585">Le classi di test implementano una *classe fixture* Interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) per indicare che la classe contiene test e fornisce istanze di oggetti condivisi tra i test nella classe.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-585">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="0ce0f-586">La classe di test seguente, `BasicTests` , USA `WebApplicationFactory` per avviare il SUT e fornire un [HttpClient](/dotnet/api/system.net.http.httpclient) a un metodo di test, `Get_EndpointsReturnSuccessAndCorrectContentType` .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-586">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="0ce0f-587">Il metodo controlla se il codice di stato della risposta ha esito positivo (codici di stato nell'intervallo 200-299) e l' `Content-Type` intestazione è `text/html; charset=utf-8` per diverse pagine dell'app.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-587">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="0ce0f-588">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) crea un'istanza di `HttpClient` che segue automaticamente i reindirizzamenti e gestisce i cookie.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-588">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="0ce0f-589">Per impostazione predefinita, i cookie non essenziali non vengono conservati nelle richieste quando i [criteri di consenso GDPR](xref:security/gdpr) sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-589">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="0ce0f-590">Per mantenere cookie non essenziali, ad esempio quelli usati dal provider TempData, contrassegnarli come essenziali nei test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-590">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="0ce0f-591">Per istruzioni su come contrassegnare un cookie come essenziale, vedere [cookie essenziali](xref:security/gdpr#essential-cookies).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-591">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="0ce0f-592">Personalizzare WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="0ce0f-592">Customize WebApplicationFactory</span></span>

<span data-ttu-id="0ce0f-593">La configurazione dell'host Web può essere creata indipendentemente dalle classi di test ereditando da `WebApplicationFactory` per creare una o più factory personalizzate:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-593">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="0ce0f-594">Ereditare da `WebApplicationFactory` ed eseguire l'override di [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-594">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="0ce0f-595">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) consente la configurazione della raccolta di servizi con [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), che viene eseguito prima dell'app `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-595">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), which is executed before the app's `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0ce0f-596">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) consente di ignorare e modificare i servizi registrati nella raccolta di servizi con [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):</span><span class="sxs-lookup"><span data-stu-id="0ce0f-596">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows overriding and modifying registered services in the service collection with [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="0ce0f-597">Il seeding del database nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) viene eseguito dal `InitializeDbForTests` metodo.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-597">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="0ce0f-598">Il metodo è descritto nella sezione esempio di test di [integrazione: organizzazione di app di test](#test-app-organization) .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-598">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="0ce0f-599">Usare l'oggetto personalizzato `CustomWebApplicationFactory` nelle classi di test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-599">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="0ce0f-600">Nell'esempio seguente viene usata la Factory nella `IndexPageTests` classe:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-600">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="0ce0f-601">Il client dell'app di esempio è configurato per impedire l'esecuzione dei `HttpClient` reindirizzamenti seguenti.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-601">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="0ce0f-602">Come illustrato più avanti nella sezione [autenticazione fittizia](#mock-authentication) , questo consente ai test di verificare il risultato della prima risposta dell'app.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-602">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="0ce0f-603">La prima risposta è un reindirizzamento in molti di questi test con un' `Location` intestazione.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-603">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="0ce0f-604">Un test tipico usa i `HttpClient` metodi helper e per elaborare la richiesta e la risposta:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-604">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="0ce0f-605">Qualsiasi richiesta POST a SUT deve soddisfare il controllo antifalsificazione creato automaticamente dal [sistema antifalsificazione di protezione dei dati](xref:security/data-protection/introduction)dell'app.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-605">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="0ce0f-606">Per disporre la richiesta POST di un test, l'app di test deve:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-606">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="0ce0f-607">Effettuare una richiesta per la pagina.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-607">Make a request for the page.</span></span>
1. <span data-ttu-id="0ce0f-608">Analizzare il cookie antifalsificazione e richiedere il token di convalida dalla risposta.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-608">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="0ce0f-609">Eseguire la richiesta POST con il cookie antifalsificazione e il token di convalida della richiesta sul posto.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-609">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="0ce0f-610">I `SendAsync` metodi di estensione Helper (*Helper/Metodo HttpClientExtensions. cs*) e il `GetDocumentAsync` metodo helper (*Helper/htmlhelpers. cs*) nell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) usano il parser [AngleSharp](https://anglesharp.github.io/) per gestire il controllo antifalsificazione con i metodi seguenti:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-610">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="0ce0f-611">`GetDocumentAsync`&ndash;Riceve il [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) e restituisce un oggetto `IHtmlDocument` .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-611">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="0ce0f-612">`GetDocumentAsync`Usa una factory che prepara una *risposta virtuale* basata sull'originale `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-612">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="0ce0f-613">Per ulteriori informazioni, vedere la [documentazione di AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-613">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="0ce0f-614">`SendAsync`i metodi di estensione per `HttpClient` compongono un [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) e chiamano [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) per inviare richieste a SUT.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-614">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="0ce0f-615">Gli overload per `SendAsync` accettano il formato HTML ( `IHtmlFormElement` ) e gli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-615">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="0ce0f-616">Pulsante Invia nel formato ( `IHtmlElement` )</span><span class="sxs-lookup"><span data-stu-id="0ce0f-616">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="0ce0f-617">Raccolta di valori di form ( `IEnumerable<KeyValuePair<string, string>>` )</span><span class="sxs-lookup"><span data-stu-id="0ce0f-617">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="0ce0f-618">Pulsante Invia ( `IHtmlElement` ) e valori form ( `IEnumerable<KeyValuePair<string, string>>` )</span><span class="sxs-lookup"><span data-stu-id="0ce0f-618">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="0ce0f-619">[AngleSharp](https://anglesharp.github.io/) è una libreria di analisi di terze parti usata a scopo dimostrativo in questo argomento e nell'app di esempio.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-619">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="0ce0f-620">AngleSharp non è supportato o richiesto per i test di integrazione delle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-620">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="0ce0f-621">È possibile usare altri parser, ad esempio [HTML Agility Pack (HAP)](https://html-agility-pack.net/).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-621">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="0ce0f-622">Un altro approccio consiste nel scrivere codice per gestire direttamente il token di verifica delle richieste del sistema antifalsificazione e il cookie antifalsificazione.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-622">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="0ce0f-623">Personalizzare il client con WithWebHostBuilder</span><span class="sxs-lookup"><span data-stu-id="0ce0f-623">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="0ce0f-624">Quando è richiesta una configurazione aggiuntiva in un metodo di test, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) crea un nuovo oggetto `WebApplicationFactory` con un [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) che viene ulteriormente personalizzato in base alla configurazione.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-624">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="0ce0f-625">Il `Post_DeleteMessageHandler_ReturnsRedirectToRoot` metodo di test dell' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) illustra l'uso di `WithWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-625">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="0ce0f-626">Questo test esegue l'eliminazione di un record nel database attivando un invio di form in SUT.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-626">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="0ce0f-627">Poiché un altro test della `IndexPageTests` classe esegue un'operazione che elimina tutti i record nel database e può essere eseguita prima del `Post_DeleteMessageHandler_ReturnsRedirectToRoot` metodo, il database viene reinizializzato in questo metodo di test per assicurarsi che sia presente un record per il SUT da eliminare.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-627">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="0ce0f-628">La selezione del primo pulsante Elimina del `messages` modulo in SUT viene simulata nella richiesta al SUT:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-628">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="0ce0f-629">Opzioni client</span><span class="sxs-lookup"><span data-stu-id="0ce0f-629">Client options</span></span>

<span data-ttu-id="0ce0f-630">Nella tabella seguente viene illustrato il valore predefinito di [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) disponibile durante la creazione di `HttpClient` istanze.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-630">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="0ce0f-631">Opzione</span><span class="sxs-lookup"><span data-stu-id="0ce0f-631">Option</span></span> | <span data-ttu-id="0ce0f-632">Descrizione</span><span class="sxs-lookup"><span data-stu-id="0ce0f-632">Description</span></span> | <span data-ttu-id="0ce0f-633">Predefinito</span><span class="sxs-lookup"><span data-stu-id="0ce0f-633">Default</span></span> |
| ---
<span data-ttu-id="0ce0f-634">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-635">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-636">'Identity'</span></span>
- <span data-ttu-id="0ce0f-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-638">'Razor'</span></span>
- <span data-ttu-id="0ce0f-639">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-639">'SignalR' uid:</span></span> 

<span data-ttu-id="0ce0f-640">--- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-640">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-641">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-642">'Identity'</span></span>
- <span data-ttu-id="0ce0f-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-644">'Razor'</span></span>
- <span data-ttu-id="0ce0f-645">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-645">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-646">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-647">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-647">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-648">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-648">'Identity'</span></span>
- <span data-ttu-id="0ce0f-649">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-649">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-650">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-650">'Razor'</span></span>
- <span data-ttu-id="0ce0f-651">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-651">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-652">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-653">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-653">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-654">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-654">'Identity'</span></span>
- <span data-ttu-id="0ce0f-655">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-655">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-656">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-656">'Razor'</span></span>
- <span data-ttu-id="0ce0f-657">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-657">'SignalR' uid:</span></span> 

<span data-ttu-id="0ce0f-658">------ | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-658">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-659">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-659">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-660">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-660">'Identity'</span></span>
- <span data-ttu-id="0ce0f-661">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-661">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-662">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-662">'Razor'</span></span>
- <span data-ttu-id="0ce0f-663">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-663">'SignalR' uid:</span></span> 

<span data-ttu-id="0ce0f-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Ottiene o imposta un valore che indica se le `HttpClient` istanze devono seguire automaticamente le risposte di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="0ce0f-665"> | `true`| | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Ottiene o imposta l'indirizzo di base delle `HttpClient` istanze di.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-665"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="0ce0f-666"> | `http://localhost`| | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Ottiene o imposta un valore che indica se le `HttpClient` istanze devono gestire i cookie.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-666"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="0ce0f-667"> | `true`| | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Ottiene o imposta il numero massimo di risposte di reindirizzamento che le `HttpClient` istanze devono seguire.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-667"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="0ce0f-668">| 7 |</span><span class="sxs-lookup"><span data-stu-id="0ce0f-668">| 7 |</span></span>

<span data-ttu-id="0ce0f-669">Creare la `WebApplicationFactoryClientOptions` classe e passarla al metodo [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (i valori predefiniti sono mostrati nell'esempio di codice):</span><span class="sxs-lookup"><span data-stu-id="0ce0f-669">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="0ce0f-670">Inserire i servizi fittizi</span><span class="sxs-lookup"><span data-stu-id="0ce0f-670">Inject mock services</span></span>

<span data-ttu-id="0ce0f-671">È possibile eseguire l'override dei servizi in un test con una chiamata a [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) nel generatore host.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-671">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="0ce0f-672">**Per inserire i servizi fittizi, SUT deve disporre di una `Startup` classe con un `Startup.ConfigureServices` metodo.**</span><span class="sxs-lookup"><span data-stu-id="0ce0f-672">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="0ce0f-673">L'esempio SUT include un servizio con ambito che restituisce un'offerta.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-673">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="0ce0f-674">La virgoletta è incorporata in un campo nascosto nella pagina di indice quando viene richiesta la pagina di indice.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-674">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="0ce0f-675">*Services/IQuoteService. cs*:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-675">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="0ce0f-676">*Services/QuoteService. cs*:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-676">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="0ce0f-677">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-677">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="0ce0f-678">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-678">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="0ce0f-679">*Pages/index. cs*:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-679">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="0ce0f-680">Quando viene eseguita l'app SUT, viene generato il markup seguente:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-680">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="0ce0f-681">Per testare il servizio e l'inserimento di virgolette in un test di integrazione, un servizio fittizio viene inserito in SUT dal test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-681">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="0ce0f-682">Il servizio fittizio sostituisce l'app `QuoteService` con un servizio fornito dall'app di test, denominato `TestQuoteService` :</span><span class="sxs-lookup"><span data-stu-id="0ce0f-682">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="0ce0f-683">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-683">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="0ce0f-684">`ConfigureTestServices`viene chiamato e il servizio con ambito viene registrato:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-684">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="0ce0f-685">Il markup prodotto durante l'esecuzione del test riflette il testo tra virgolette fornito da `TestQuoteService` , quindi l'asserzione passa:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-685">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="0ce0f-686">Autenticazione fittizia</span><span class="sxs-lookup"><span data-stu-id="0ce0f-686">Mock authentication</span></span>

<span data-ttu-id="0ce0f-687">I test della `AuthTests` classe verificano che un endpoint protetto:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-687">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="0ce0f-688">Reindirizza un utente non autenticato alla pagina di accesso dell'app.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-688">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="0ce0f-689">Restituisce il contenuto per un utente autenticato.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-689">Returns content for an authenticated user.</span></span>

<span data-ttu-id="0ce0f-690">In SUT la `/SecurePage` pagina usa una convenzione [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) per applicare un [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) alla pagina.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-690">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="0ce0f-691">Per ulteriori informazioni, vedere la pagina relativa alle [ Razor convenzioni di autorizzazione](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-691">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="0ce0f-692">Nel `Get_SecurePageRedirectsAnUnauthenticatedUser` test un [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) è impostato in modo da non consentire i reindirizzamenti impostando [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) su `false` :</span><span class="sxs-lookup"><span data-stu-id="0ce0f-692">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="0ce0f-693">Se si impedisce al client di seguire il reindirizzamento, è possibile effettuare i controlli seguenti:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-693">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="0ce0f-694">Il codice di stato restituito da SUT può essere controllato rispetto al risultato [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) previsto, non al codice di stato finale dopo il reindirizzamento alla pagina di accesso, che sarebbe [HttpStatusCode. OK](/dotnet/api/system.net.httpstatuscode).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-694">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="0ce0f-695">Il `Location` valore dell'intestazione nelle intestazioni della risposta viene controllato per confermare che inizia con `http://localhost/Identity/Account/Login` , non con la risposta finale della pagina di accesso, in cui l' `Location` intestazione non è presente.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-695">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="0ce0f-696">L'app di test può simulare un <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) per testare gli aspetti dell'autenticazione e dell'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-696">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="0ce0f-697">Uno scenario minimo restituisce [AuthenticateResult. Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span><span class="sxs-lookup"><span data-stu-id="0ce0f-697">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="0ce0f-698">Il metodo `TestAuthHandler` viene chiamato per autenticare un utente quando lo schema di autenticazione è impostato su `Test` dove `AddAuthentication` è registrato per `ConfigureTestServices` :</span><span class="sxs-lookup"><span data-stu-id="0ce0f-698">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="0ce0f-699">Per ulteriori informazioni su `WebApplicationFactoryClientOptions` , vedere la sezione [Opzioni client](#client-options) .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-699">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="0ce0f-700">Impostare l'ambiente</span><span class="sxs-lookup"><span data-stu-id="0ce0f-700">Set the environment</span></span>

<span data-ttu-id="0ce0f-701">Per impostazione predefinita, l'host e l'ambiente app di SUT sono configurati per l'utilizzo dell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-701">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="0ce0f-702">Per eseguire l'override dell'ambiente di SUT:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-702">To override the SUT's environment:</span></span>

* <span data-ttu-id="0ce0f-703">Impostare la `ASPNETCORE_ENVIRONMENT` variabile di ambiente (ad esempio,, `Staging` `Production` o un altro valore personalizzato, ad esempio `Testing` ).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-703">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="0ce0f-704">Eseguire l'override `CreateWebHostBuilder` nell'app di test per leggere la `ASPNETCORE_ENVIRONMENT` variabile di ambiente.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-704">Override `CreateWebHostBuilder` in the test app to read the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override IWebHostBuilder CreateWebHostBuilder()
    {
        return base.CreateWebHostBuilder()
            .UseEnvironment(
                Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    }

    ...
}
```

<span data-ttu-id="0ce0f-705">L'ambiente può anche essere impostato direttamente nel generatore host in un oggetto personalizzato <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="0ce0f-705">The environment can also be set directly on the host builder in a custom <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601>:</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.UseEnvironment(
            Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    
        ...
    }

    ...
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="0ce0f-706">Come l'infrastruttura di test deduce il percorso radice del contenuto dell'app</span><span class="sxs-lookup"><span data-stu-id="0ce0f-706">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="0ce0f-707">Il `WebApplicationFactory` Costruttore deduce il percorso [radice del contenuto](xref:fundamentals/index#content-root) dell'app cercando un [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) nell'assembly contenente i test di integrazione con una chiave uguale all' `TEntryPoint` assembly `System.Reflection.Assembly.FullName` .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-707">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="0ce0f-708">Se non viene trovato un attributo con la chiave corretta, viene `WebApplicationFactory` eseguito il fallback alla ricerca di un file di soluzione (con*estensione sln*) e il nome dell'assembly viene aggiunto `TEntryPoint` alla directory della soluzione.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-708">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="0ce0f-709">La directory radice dell'app (percorso radice del contenuto) viene usata per individuare le visualizzazioni e i file di contenuto.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-709">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="0ce0f-710">Disabilitare la copia shadow</span><span class="sxs-lookup"><span data-stu-id="0ce0f-710">Disable shadow copying</span></span>

<span data-ttu-id="0ce0f-711">La copia shadow comporta l'esecuzione dei test in una directory diversa rispetto alla directory di output.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-711">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="0ce0f-712">Per il corretto funzionamento dei test, è necessario disabilitare la copia shadow.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-712">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="0ce0f-713">L' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) USA xUnit e Disabilita la copia shadow per xUnit includendo un file *xUnit. Runner. JSON* con l'impostazione di configurazione corretta.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-713">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="0ce0f-714">Per altre informazioni, vedere [configurazione di xUnit con JSON](https://xunit.github.io/docs/configuring-with-json.html).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-714">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="0ce0f-715">Aggiungere il file *xUnit. Runner. JSON* alla radice del progetto di test con il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-715">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="0ce0f-716">Se si usa Visual Studio, impostare la proprietà **copia nella directory di output** del file su **copia sempre**.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-716">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="0ce0f-717">Se non si usa Visual Studio, aggiungere una `Content` destinazione al file di progetto dell'app di test:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-717">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="0ce0f-718">Eliminazione di oggetti</span><span class="sxs-lookup"><span data-stu-id="0ce0f-718">Disposal of objects</span></span>

<span data-ttu-id="0ce0f-719">Dopo l'esecuzione dei test dell' `IClassFixture` implementazione, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) e [HttpClient](/dotnet/api/system.net.http.httpclient) vengono eliminati quando xUnit Elimina [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-719">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="0ce0f-720">Se gli oggetti di cui è stata creata un'istanza dallo sviluppatore richiedono l'eliminazione, eliminarli nell' `IClassFixture` implementazione.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-720">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="0ce0f-721">Per ulteriori informazioni, vedere [implementazione di un metodo Dispose](/dotnet/standard/garbage-collection/implementing-dispose).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-721">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="0ce0f-722">Esempio di test di integrazione</span><span class="sxs-lookup"><span data-stu-id="0ce0f-722">Integration tests sample</span></span>

<span data-ttu-id="0ce0f-723">L' [app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) è costituita da due app:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-723">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="0ce0f-724">App</span><span class="sxs-lookup"><span data-stu-id="0ce0f-724">App</span></span> | <span data-ttu-id="0ce0f-725">Directory del progetto</span><span class="sxs-lookup"><span data-stu-id="0ce0f-725">Project directory</span></span> | <span data-ttu-id="0ce0f-726">Descrizione</span><span class="sxs-lookup"><span data-stu-id="0ce0f-726">Description</span></span> |
| --- | ---
<span data-ttu-id="0ce0f-727">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-728">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-729">'Identity'</span></span>
- <span data-ttu-id="0ce0f-730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-730">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-731">'Razor'</span></span>
- <span data-ttu-id="0ce0f-732">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-733">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-734">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-735">'Identity'</span></span>
- <span data-ttu-id="0ce0f-736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-736">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-737">'Razor'</span></span>
- <span data-ttu-id="0ce0f-738">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-739">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-740">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-741">'Identity'</span></span>
- <span data-ttu-id="0ce0f-742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-742">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-743">'Razor'</span></span>
- <span data-ttu-id="0ce0f-744">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-745">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-746">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-747">'Identity'</span></span>
- <span data-ttu-id="0ce0f-748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-748">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-749">'Razor'</span></span>
- <span data-ttu-id="0ce0f-750">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-751">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-752">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-753">'Identity'</span></span>
- <span data-ttu-id="0ce0f-754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-754">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-755">'Razor'</span></span>
- <span data-ttu-id="0ce0f-756">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-757">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-758">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-759">'Identity'</span></span>
- <span data-ttu-id="0ce0f-760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-760">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-761">'Razor'</span></span>
- <span data-ttu-id="0ce0f-762">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-762">'SignalR' uid:</span></span> 

<span data-ttu-id="0ce0f-763">--------- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-763">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-764">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-765">'Identity'</span></span>
- <span data-ttu-id="0ce0f-766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-766">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-767">'Razor'</span></span>
- <span data-ttu-id="0ce0f-768">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-768">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-769">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-770">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-770">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-771">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-771">'Identity'</span></span>
- <span data-ttu-id="0ce0f-772">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-772">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-773">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-773">'Razor'</span></span>
- <span data-ttu-id="0ce0f-774">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-774">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-775">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-776">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-776">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-777">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-777">'Identity'</span></span>
- <span data-ttu-id="0ce0f-778">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-778">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-779">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-779">'Razor'</span></span>
- <span data-ttu-id="0ce0f-780">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-780">'SignalR' uid:</span></span> 

<span data-ttu-id="0ce0f-781">------ | | App Message (SUT) | *src/RazorPagesProject* | Consente a un utente di aggiungere, eliminare, eliminare tutti i messaggi e analizzarli.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-781">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="0ce0f-782">| | App di test | *test/RazorPagesProject. test* | Utilizzato per eseguire il test di integrazione di SUT.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-782">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="0ce0f-783">I test possono essere eseguiti usando le funzionalità di test predefinite di un IDE, ad esempio [Visual Studio](https://visualstudio.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-783">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="0ce0f-784">Se si usa [Visual Studio Code](https://code.visualstudio.com/) o la riga di comando, eseguire il comando seguente al prompt dei comandi nella directory *tests/RazorPagesProject. tests* :</span><span class="sxs-lookup"><span data-stu-id="0ce0f-784">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="0ce0f-785">Organizzazione Message app (SUT)</span><span class="sxs-lookup"><span data-stu-id="0ce0f-785">Message app (SUT) organization</span></span>

<span data-ttu-id="0ce0f-786">SUT è un Razor sistema di messaggi di pagine con le seguenti caratteristiche:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-786">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="0ce0f-787">La pagina di indice dell'app (*pages/index. cshtml* e *pages/index. cshtml. cs*) fornisce i metodi di interfaccia utente e modello di pagina per controllare l'aggiunta, l'eliminazione e l'analisi dei messaggi (media parole per messaggio).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-787">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="0ce0f-788">Un messaggio viene descritto dalla `Message` classe (*Data/Message. cs*) con due proprietà: `Id` (chiave) e `Text` (messaggio).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-788">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="0ce0f-789">La `Text` proprietà è obbligatoria e limitata a 200 caratteri.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-789">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="0ce0f-790">I messaggi vengono archiviati utilizzando&#8224; [di database in memoria di Entity Framework](/ef/core/providers/in-memory/) .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-790">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="0ce0f-791">L'app contiene un livello di accesso ai dati (DAL) nella classe del contesto di database, `AppDbContext` (*Data/AppDbContext. cs*).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-791">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="0ce0f-792">Se il database è vuoto all'avvio dell'app, l'archivio messaggi viene inizializzato con tre messaggi.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-792">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="0ce0f-793">L'app include un oggetto a `/SecurePage` cui è possibile accedere solo da un utente autenticato.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-793">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="0ce0f-794">&#8224;argomento EF, [test con InMemory](/ef/core/miscellaneous/testing/in-memory), spiega come usare un database in memoria per i test con MSTest.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-794">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="0ce0f-795">In questo argomento viene usato il Framework di test di [xUnit](https://xunit.github.io/) .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-795">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="0ce0f-796">I concetti di test e le implementazioni di test in diversi framework di test sono simili, ma non identici.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-796">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="0ce0f-797">Anche se l'app non usa il modello di repository e non è un esempio efficace del [modello di unità di lavoro (UOW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), le Razor pagine supportano questi modelli di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-797">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="0ce0f-798">Per altre informazioni, vedere [progettazione del livello di persistenza dell'infrastruttura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) e della [logica del controller di test](/aspnet/core/mvc/controllers/testing) (l'esempio implementa il modello di repository).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-798">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="0ce0f-799">Testare l'organizzazione dell'app</span><span class="sxs-lookup"><span data-stu-id="0ce0f-799">Test app organization</span></span>

<span data-ttu-id="0ce0f-800">L'app di test è un'app console all'interno della directory *tests/RazorPagesProject. tests* .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-800">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="0ce0f-801">Testare la directory dell'app</span><span class="sxs-lookup"><span data-stu-id="0ce0f-801">Test app directory</span></span> | <span data-ttu-id="0ce0f-802">Descrizione</span><span class="sxs-lookup"><span data-stu-id="0ce0f-802">Description</span></span> |
| ---
<span data-ttu-id="0ce0f-803">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-803">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-804">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-804">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-805">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-805">'Identity'</span></span>
- <span data-ttu-id="0ce0f-806">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-806">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-807">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-807">'Razor'</span></span>
- <span data-ttu-id="0ce0f-808">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-808">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-809">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-809">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-810">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-810">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-811">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-811">'Identity'</span></span>
- <span data-ttu-id="0ce0f-812">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-812">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-813">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-813">'Razor'</span></span>
- <span data-ttu-id="0ce0f-814">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-814">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-815">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-815">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-816">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-816">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-817">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-817">'Identity'</span></span>
- <span data-ttu-id="0ce0f-818">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-818">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-819">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-819">'Razor'</span></span>
- <span data-ttu-id="0ce0f-820">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-820">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-821">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-821">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-822">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-822">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-823">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-823">'Identity'</span></span>
- <span data-ttu-id="0ce0f-824">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-824">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-825">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-825">'Razor'</span></span>
- <span data-ttu-id="0ce0f-826">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-826">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-827">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-827">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-828">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-828">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-829">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-829">'Identity'</span></span>
- <span data-ttu-id="0ce0f-830">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-830">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-831">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-831">'Razor'</span></span>
- <span data-ttu-id="0ce0f-832">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-832">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-833">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-833">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-834">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-834">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-835">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-835">'Identity'</span></span>
- <span data-ttu-id="0ce0f-836">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-836">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-837">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-837">'Razor'</span></span>
- <span data-ttu-id="0ce0f-838">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-838">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-839">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-839">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-840">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-840">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-841">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-841">'Identity'</span></span>
- <span data-ttu-id="0ce0f-842">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-842">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-843">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-843">'Razor'</span></span>
- <span data-ttu-id="0ce0f-844">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-844">'SignalR' uid:</span></span> 

<span data-ttu-id="0ce0f-845">--------- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-845">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-846">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-846">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-847">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-847">'Identity'</span></span>
- <span data-ttu-id="0ce0f-848">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-848">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-849">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-849">'Razor'</span></span>
- <span data-ttu-id="0ce0f-850">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-850">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-851">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-851">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-852">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-852">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-853">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-853">'Identity'</span></span>
- <span data-ttu-id="0ce0f-854">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-854">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-855">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-855">'Razor'</span></span>
- <span data-ttu-id="0ce0f-856">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-856">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0ce0f-857">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-857">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0ce0f-858">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-858">'Blazor'</span></span>
- <span data-ttu-id="0ce0f-859">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-859">'Identity'</span></span>
- <span data-ttu-id="0ce0f-860">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-860">'Let's Encrypt'</span></span>
- <span data-ttu-id="0ce0f-861">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0ce0f-861">'Razor'</span></span>
- <span data-ttu-id="0ce0f-862">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="0ce0f-862">'SignalR' uid:</span></span> 

<span data-ttu-id="0ce0f-863">------ | | *AuthTests* | Contiene i metodi di test per:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-863">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="0ce0f-864">Accesso a una pagina sicura da un utente non autenticato.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-864">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="0ce0f-865">Accesso a una pagina sicura da un utente autenticato con una simulazione <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</span><span class="sxs-lookup"><span data-stu-id="0ce0f-865">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="0ce0f-866">Ottenere un profilo utente GitHub e controllare l'accesso dell'utente del profilo.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-866">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="0ce0f-867">| | *BasicTests* | Contiene un metodo di test per il routing e il tipo di contenuto.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-867">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="0ce0f-868">| | *IntegrationTests* | Contiene i test di integrazione per la pagina di indice utilizzando la `WebApplicationFactory` classe personalizzata.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-868">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="0ce0f-869">| | *Helper/utilità* | </span><span class="sxs-lookup"><span data-stu-id="0ce0f-869">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="0ce0f-870">*Utilities.cs* contiene il `InitializeDbForTests` metodo utilizzato per eseguire il seeding del database con dati di test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-870">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="0ce0f-871">*HtmlHelpers.cs* fornisce un metodo per restituire un AngleSharp `IHtmlDocument` per l'uso da parte dei metodi di test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-871">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="0ce0f-872">*HttpClientExtensions.cs* forniscono overload per `SendAsync` per inviare richieste a SUT.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-872">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="0ce0f-873">Il Framework di test è [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-873">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="0ce0f-874">I test di integrazione vengono eseguiti usando [Microsoft. AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost), che include [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="0ce0f-874">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="0ce0f-875">Poiché il pacchetto [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) viene usato per configurare l'host di test e il server di prova, i `TestHost` `TestServer` pacchetti e non richiedono riferimenti diretti al pacchetto nel file di progetto dell'app di test o nella configurazione per sviluppatori nell'app di test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-875">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="0ce0f-876">**Seeding del database per il test**</span><span class="sxs-lookup"><span data-stu-id="0ce0f-876">**Seeding the database for testing**</span></span>

<span data-ttu-id="0ce0f-877">I test di integrazione richiedono in genere un set di dati di piccole dimensioni nel database prima dell'esecuzione del test.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-877">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="0ce0f-878">Ad esempio, un test di eliminazione richiede l'eliminazione di un record del database, pertanto è necessario che il database disponga di almeno un record affinché la richiesta di eliminazione abbia esito positivo.</span><span class="sxs-lookup"><span data-stu-id="0ce0f-878">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="0ce0f-879">L'app di esempio esegue il seeding del database con tre messaggi in *Utilities.cs* che possono essere usati dai test quando vengono eseguiti:</span><span class="sxs-lookup"><span data-stu-id="0ce0f-879">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="0ce0f-880">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="0ce0f-880">Additional resources</span></span>

* [<span data-ttu-id="0ce0f-881">Unit test</span><span class="sxs-lookup"><span data-stu-id="0ce0f-881">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
