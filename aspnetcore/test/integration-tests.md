---
title: Test di integrazione in ASP.NET Core
author: rick-anderson
description: Informazioni su come i test di integrazione garantiscono che i componenti dell'app funzionano correttamente a livello di infrastruttura, tra cui database, file system e rete.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/06/2019
uid: test/integration-tests
ms.openlocfilehash: 414e47b9c5a1c843755bd79d313f503b554945db
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664696"
---
# <a name="integration-tests-in-aspnet-core"></a>Test di integrazione in ASP.NET Core

Di [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/)e [Jos van der Til](https://jvandertil.nl)

::: moniker range=">= aspnetcore-3.0"

I test di integrazione assicurano che i componenti di un'app funzionino correttamente a un livello che include l'infrastruttura di supporto dell'app, ad esempio il database, il file system e la rete. ASP.NET Core supporta i test di integrazione utilizzando un framework di unit test con un host Web di test e un server di test in memoria.

In questo argomento si presuppone una conoscenza di base degli unit test. Se non si ha familiarità con i concetti di test, vedere l'argomento [Unit Testing in .NET Core e .NET Standard](/dotnet/core/testing/) e il relativo contenuto collegato.

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ( come[scaricare](xref:index#how-to-download-a-sample))

L'app di esempio è un'app Razor Pages e presuppone una conoscenza di base delle pagine Razor.The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages. Se non si ha familiarità con le pagine Razor, vedere gli argomenti seguenti:If undes with Razor Pages, see the following topics:

* [Introduzione a Razor Pages in ASP.NET Core](xref:razor-pages/index)
* [Introduzione a Razor Pages](xref:tutorials/razor-pages/razor-pages-start)
* [Unit test per Razor Pages](xref:test/razor-pages-tests)

> [!NOTE]
> Per testare le SPA, è stato consigliato uno strumento come [Selenium](https://www.seleniumhq.org/), in grado di automatizzare un browser.

## <a name="introduction-to-integration-tests"></a>Introduzione ai test di integrazione

I test di integrazione valutano i componenti di un'app a un livello più ampio rispetto agli [unit test.](/dotnet/core/testing/) Gli unit test vengono utilizzati per testare i componenti software isolati, ad esempio i singoli metodi di classe. I test di integrazione confermano che due o più componenti dell'app funzionano insieme per produrre un risultato previsto, includendo eventualmente tutti i componenti necessari per elaborare completamente una richiesta.

Questi test più ampi vengono usati per testare l'infrastruttura dell'app e l'intero framework, spesso includendo i componenti seguenti:These broader tests are used to test the app's infrastructure and whole framework, often including the following components:

* Database
* File system
* Dispositivi di rete
* Pipeline di richiesta-risposta

Gli unit test utilizzano componenti fabbricati, *noti* come falsi o *oggetti fittizi,* al posto dei componenti dell'infrastruttura.

A differenza degli unit test, i test di integrazione:In contrast to unit tests, integration tests:

* Usa i componenti effettivi utilizzati dall'app nell'ambiente di produzione.
* Richiedere più codice e trattamento dei dati.
* Prenditi più tempo per correre.

Pertanto, limitare l'utilizzo dei test di integrazione agli scenari di infrastruttura più importanti. Se un comportamento può essere testato usando uno unit test o uno unit test, scegliere lo unit test.

> [!TIP]
> Non scrivere test di integrazione per ogni possibile permutazione di dati e accesso ai file con database e file system. Indipendentemente dal numero di posizioni all'interno di un'app che interagiscono con database e file system, un set mirato di test di integrazione di lettura, scrittura, aggiornamento ed eliminazione è in genere in grado di testare adeguatamente i componenti di database e file system. Utilizzare gli unit test per i test di routine della logica del metodo che interagiscono con questi componenti. Negli unit test, l'uso di falsi/simulazioni dell'infrastruttura comporta un'esecuzione dei test più rapida.

> [!NOTE]
> Nelle discussioni sui test di integrazione, il progetto testato viene spesso chiamato *System Under Test*, o "SUT" in breve.
>
> *"SUT" viene usato in questo argomento per fare riferimento all'app ASP.NET Core testata.*

## <a name="aspnet-core-integration-tests"></a>Test di integrazione di ASP.NET Core

I test di integrazione in ASP.NET Core richiedono quanto segue:

* Un progetto di test viene utilizzato per contenere ed eseguire i test. Il progetto di test contiene un riferimento al SUT.
* Il progetto di test crea un host Web di test per SUT e utilizza un client server di test per gestire le richieste e le risposte con SUT.
* Un test runner viene utilizzato per eseguire i test e segnalare i risultati del test.

I test di integrazione seguono una sequenza di eventi che includono i soliti passi di test *Arrange*, *Act*e *Assert:*

1. L'host web di SUT è configurato.
1. Viene creato un client server di prova per inviare richieste all'app.
1. Viene eseguito il passo del test *Arrange:* l'app di test prepara una richiesta.
1. Viene eseguito il passo del test *Act:* il client invia la richiesta e riceve la risposta.
1. Viene eseguito il passo del test *Assert:* la risposta *effettiva* viene convalidata come *passaggio* o *esito negativo* in base a una risposta *prevista.*
1. Il processo continua fino all'esecuzione di tutti i test.
1. Vengono riportati i risultati del test.

In genere, l'host Web di test è configurato in modo diverso rispetto all'host Web normale dell'app per le esecuzioni dei test. Ad esempio, per i test è possibile usare un database diverso o impostazioni dell'app diverse.

I componenti dell'infrastruttura, ad esempio l'host Web di prova e il server di test in memoria ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), vengono forniti o gestiti dal pacchetto [Microsoft.AspNetCore.Mvc.Testing.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) L'uso di questo pacchetto semplifica la creazione e l'esecuzione dei test.

Il `Microsoft.AspNetCore.Mvc.Testing` pacchetto gestisce le attività seguenti:The package handles the following tasks:

* Copia il file delle dipendenze (*.deps*) dal SUT nella directory *bin* del progetto di test.
* Imposta la radice del [contenuto sulla](xref:fundamentals/index#content-root) radice del progetto SUT in modo che i file statici e le pagine/visualizzazioni vengano trovati quando vengono eseguiti i test.
* Fornisce la classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) per semplificare `TestServer`l'avvio di SUT con .

Nella documentazione relativa agli [unit test](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) viene descritto come impostare un progetto di test e un test runner, oltre a istruzioni dettagliate su come eseguire test e consigli su come denominare test e classi di test.

> [!NOTE]
> Quando si crea un progetto di test per un'app, separare gli unit test dai test di integrazione in progetti diversi. In questo modo si garantisce che i componenti di test dell'infrastruttura non vengano inclusi accidentalmente negli unit test. La separazione dei test di unità e di integrazione consente inoltre di controllare su quale set di test vengono eseguiti.

Non c'è praticamente alcuna differenza tra la configurazione per i test di pagine Razor apps e applicazioni MVC. L'unica differenza è nel nome dei test. In un'app Razor Pages, i test degli endpoint di pagina `IndexPageTests` vengono in genere denominati in base alla classe del modello di pagina, ad esempio per testare l'integrazione dei componenti per la pagina Indice. In un'app MVC, i test sono in genere organizzati in `HomeControllerTests` base alle classi controller e denominati in base ai controller testati, ad esempio per testare l'integrazione dei componenti per il controller Home.

## <a name="test-app-prerequisites"></a>Prerequisiti dell'app di testTest app prerequisites

Il progetto di test deve:

* Fare riferimento al pacchetto [Microsoft.AspNetCore.Mvc.Testing.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)
* Specificare il Web SDK`<Project Sdk="Microsoft.NET.Sdk.Web">`nel file di progetto ( ).

Questi prerequisiti possono essere visualizzati [nell'app di esempio.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) Esaminare il file *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj.* L'app di esempio usa il framework di test xUnit e la libreria del parser [AngleSharp,](https://anglesharp.github.io/) pertanto anche l'app di esempio fa riferimento:The sample app uses the [xUnit](https://xunit.github.io/) test framework and the AngleSharp parser library, so the sample app also references:

* [xunit](https://www.nuget.org/packages/xunit)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [AngleSharp (Marca](https://www.nuget.org/packages/AngleSharp)

Entity Framework Core viene utilizzato anche nei test. L'app fa riferimento a:

* [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a>Ambiente SUT

Se [l'ambiente](xref:fundamentals/environments) del SUT non è impostato, l'ambiente predefinito è Sviluppo.If the SUT's environment isn't set, the default s development to Development.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Test di base con il valore predefinito WebApplicationFactory

[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) viene utilizzato per creare un [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) per i test di integrazione. `TEntryPoint`è la classe del punto di `Startup` ingresso del SUT, di solito la classe.

Le classi di test *implementano* un'interfaccia di sistemazione di classe ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) per indicare che la classe contiene test e fornire istanze di oggetti condivisi tra i test nella classe.

Nella classe di `BasicTests`test `WebApplicationFactory` riportata di seguito, , viene utilizzato il `Get_EndpointsReturnSuccessAndCorrectContentType`metodo per eseguire il bootstrap di SUT e fornire [un httpClient](/dotnet/api/system.net.http.httpclient) a un metodo di test, . Il metodo controlla se il codice di stato della risposta ha esito positivo `Content-Type` (codici di stato nell'intervallo 200-299) e l'intestazione è `text/html; charset=utf-8` per diverse pagine dell'app.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) crea un'istanza di `HttpClient` che segue automaticamente reindirizza e gestisce i cookie.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Per impostazione predefinita, i cookie non essenziali non vengono mantenuti tra le richieste quando il criterio di [consenso GDPR](xref:security/gdpr) è abilitato. Per conservare i cookie non essenziali, ad esempio quelli utilizzati dal provider TempData, contrassegnarli come essenziali nei test. Per istruzioni su come contrassegnare un cookie come essenziale, vedere [Cookie essenziali](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Personalizza WebApplicationFactory

La configurazione dell'host Web può essere creata `WebApplicationFactory` indipendentemente dalle classi di test ereditando da per creare una o più factory personalizzate:

1. Ereditare `WebApplicationFactory` da ed eseguire l'override di [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) consente la configurazione della raccolta di servizi con [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   Il seeding del database [nell'app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) viene eseguito dal `InitializeDbForTests` metodo. Il metodo è descritto nella sezione Esempio di [test di integrazione: Test dell'organizzazione dell'app.](#test-app-organization)

   Il contesto del database di `Startup.ConfigureServices` SUT viene registrato nel relativo metodo. Il callback dell'app `builder.ConfigureServices` di test viene eseguito *dopo* l'esecuzione del codice dell'app. `Startup.ConfigureServices` L'ordine di esecuzione è una modifica sostanziale per [l'host generico](xref:fundamentals/host/generic-host) con il rilascio di ASP.NET Core 3.0. Per usare un database diverso per i test rispetto al database dell'app, il contesto del database dell'app deve essere sostituito in `builder.ConfigureServices`.

   L'app di esempio trova il descrittore del servizio per il contesto del database e usa il descrittore per rimuovere la registrazione del servizio. Successivamente, la factory `ApplicationDbContext` aggiunge un nuovo che utilizza un database in memoria per i test.

   Per connettersi a un database diverso rispetto `UseInMemoryDatabase` al database in memoria, modificare la chiamata per connettere il contesto a un database diverso. Per utilizzare un database di test di SQL Server:To use a SQL Server test database:

   * Fare riferimento al pacchetto [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet nel file di progetto.
   * Chiamare `UseSqlServer` con una stringa di connessione al database.

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. Usare l'oggetto personalizzato `CustomWebApplicationFactory` nelle classi di test. L'esempio seguente usa `IndexPageTests` la factory nella classe:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Il client dell'app di esempio `HttpClient` è configurato per impedire i reindirizzamenti seguenti. Come spiegato più avanti nella sezione [Autenticazione fittizia,](#mock-authentication) questo consente ai test di controllare il risultato della prima risposta dell'app. La prima risposta è un reindirizzamento `Location` in molti di questi test con un'intestazione.

3. Un test tipico `HttpClient` usa i metodi di supporto e per elaborare la richiesta e la risposta:A typical test uses the e helper methods to process the request and the response:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Qualsiasi richiesta POST al SUT deve soddisfare il controllo antifalsificazione che viene automaticamente effettuato dal [sistema antifalsificazione](xref:security/data-protection/introduction)della protezione dei dati dell'app. Per richiedere la richiesta POST di un test, l'app di test deve:

1. Fai una richiesta per la pagina.
1. Analizzare il cookie dell'antifalandrale e il token di convalida della richiesta dalla risposta.
1. Effettuare la richiesta POST con il cookie antifalandria e richiedere il token di convalida sul posto.

I `SendAsync` metodi di estensione helper (*Helpers/HttpClientExtensions.cs*) e `GetDocumentAsync` il metodo helper (*Helpers/HtmlHelpers.cs*) [nell'app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) usano il parser [AngleSharp](https://anglesharp.github.io/) per gestire il controllo antiforgiato con i metodi seguenti:

* `GetDocumentAsync`&ndash; Riceve il [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) e `IHtmlDocument`restituisce un oggetto . `GetDocumentAsync`utilizza una factory che prepara una risposta `HttpResponseMessage` *virtuale* basata sull'originale . Per ulteriori informazioni, vedere la [documentazione di AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync`metodi di `HttpClient` estensione per comporre un [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) e chiamare [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) per inviare le richieste a SUT. Gli overload `SendAsync` per accettare`IHtmlFormElement`il modulo HTML ( ) e quanto segue:
  * Pulsante Invia del`IHtmlElement`modulo ( )
  * Insieme di`IEnumerable<KeyValuePair<string, string>>`valori di modulo ( )
  * Pulsante`IHtmlElement`Invia ( )`IEnumerable<KeyValuePair<string, string>>`e valori del modulo ( )

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) è una libreria di analisi di terze parti usata a scopo dimostrativo in questo argomento e nell'app di esempio. AngleSharp non è supportato o necessario per il test di integrazione delle app ASP.NET Core. È possibile utilizzare altri parser, ad esempio [Html Agility Pack (HAP)](https://html-agility-pack.net/). Un altro approccio consiste nello scrivere codice per gestire direttamente il token di verifica della richiesta del sistema antifalsificazione e il cookie antifalsificazione.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Personalizzare il client con WithWebHostBuilder

Quando è necessaria una configurazione aggiuntiva all'interno `WebApplicationFactory` di un metodo di test, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) crea un nuovo con un [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) ulteriormente personalizzato dalla configurazione.

Il `Post_DeleteMessageHandler_ReturnsRedirectToRoot` metodo di test dell'app `WithWebHostBuilder`di [esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) illustra l'uso di . Questo test esegue un'eliminazione di record nel database attivando un invio di moduli in SUT.

Poiché un `IndexPageTests` altro test nella classe esegue un'operazione che elimina tutti `Post_DeleteMessageHandler_ReturnsRedirectToRoot` i record nel database e può essere eseguito prima del metodo, il database viene reseguito in questo metodo di test per garantire che sia presente un record per l'eliminazione di SUT. La selezione del primo `messages` pulsante di eliminazione del modulo nel SUT viene simulata nella richiesta al SUT:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Opzioni client

Nella tabella seguente vengono illustrate le opzioni `HttpClient` [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) predefinite disponibili durante la creazione di istanze.

| Opzione | Descrizione | Predefinito |
| ------ | ----------- | ------- |
| [AllowAutoRedirect (AllowAutoRedirect)](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Ottiene o imposta `HttpClient` un valore che indica se le istanze devono seguire automaticamente le risposte di reindirizzamento. | `true` |
| [Baseaddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Ottiene o imposta l'indirizzo di base delle `HttpClient` istanze. | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Ottiene o `HttpClient` imposta un valore che indica se le istanze devono gestire i cookie. | `true` |
| [MaxAutomaticReindirizzami](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Ottiene o imposta il numero massimo `HttpClient` di risposte di reindirizzamento che le istanze devono seguire. | 7 |

Creare `WebApplicationFactoryClientOptions` la classe e passarla al metodo CreateClient (i valori predefiniti vengono visualizzati nell'esempio di codice):Create the class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Iniettare servizi fittizi

I servizi possono essere sottoposti a override in un test con una chiamata a [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) nel generatore host. **Per inserire servizi fittizi, SUT `Startup` deve `Startup.ConfigureServices` avere una classe con un metodo.**

L'esempio SUT include un servizio con ambito che restituisce un'offerta. L'offerta viene incorporata in un campo nascosto nella pagina Indice quando viene richiesta la pagina Indice.

*Servizi/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Servizi/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pagine/Index.cs*:

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

Il markup seguente viene generato quando viene eseguita l'app SUT:The following markup is generated when the SUT app is run:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Per testare il servizio e l'inserimento di preventivi in un test di integrazione, un servizio fittizio viene inserito nel SUT dal test. Il servizio fittizio sostituisce `QuoteService` l'app con un servizio `TestQuoteService`fornito dall'app di test, denominato :

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices`viene chiamato e il servizio con ambito è registrato:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Il markup prodotto durante l'esecuzione del test `TestQuoteService`riflette il testo del preventivo fornito da , pertanto l'asserzione passa:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Autenticazione fittizia

I test `AuthTests` nella classe verificano che un endpoint sicuro:Tests in the class check that a secure endpoint:

* Reindirizza un utente non autenticato alla pagina di accesso dell'app.
* Restituisce il contenuto per un utente autenticato.

In SUT, `/SecurePage` la pagina utilizza una convenzione [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) per applicare un [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) alla pagina. Per ulteriori informazioni, consultate Convenzioni di [autorizzazione di Razor Pages](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

Nel `Get_SecurePageRedirectsAnUnauthenticatedUser` test, un [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) è impostato per non consentire reindirizzamenti impostando [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) su `false`:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

Impedendo al client di seguire il reindirizzamento, è possibile effettuare i seguenti controlli:

* Il codice di stato restituito da SUT può essere confrontato con il risultato [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) previsto, non con il codice di stato finale dopo il reindirizzamento alla pagina di accesso, che sarebbe [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).
* Il `Location` valore dell'intestazione nelle intestazioni di `http://localhost/Identity/Account/Login`risposta viene controllato per verificare `Location` che venga avviato con , non con la risposta finale della pagina di accesso, in cui l'intestazione non sarebbe presente.

L'app di <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> test può simulare un in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) per testare gli aspetti di autenticazione e autorizzazione. Uno scenario minimo restituisce un [valore AuthenticateResult.Success:](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

`TestAuthHandler` L'oggetto viene chiamato per autenticare un `Test` `AddAuthentication` utente quando `ConfigureTestServices`lo schema di autenticazione è impostato su where is registered for :

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Per ulteriori `WebApplicationFactoryClientOptions`informazioni su , vedere la sezione [Opzioni client.](#client-options)

## <a name="set-the-environment"></a>Impostare l'ambiente

Per impostazione predefinita, l'ambiente host e app di SUT è configurato per usare l'ambiente di sviluppo. Per sostituire l'ambiente di SUT:

* Impostare `ASPNETCORE_ENVIRONMENT` la variabile di `Staging` `Production`ambiente, ad esempio , `Testing`, o un altro valore personalizzato, ad esempio ).
* Eseguire `CreateHostBuilder` l'override nell'app di `ASPNETCORE`test per leggere le variabili di ambiente precedute da .

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>In che modo l'infrastruttura di test deduce il percorso radice del contenuto dell'appHow the test infrastructure infers the app content root path

Il `WebApplicationFactory` costruttore deduce il percorso radice del [contenuto](xref:fundamentals/index#content-root) dell'app cercando un [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) `System.Reflection.Assembly.FullName`nell'assembly contenente i test di integrazione con una chiave uguale all'assembly. `TEntryPoint` Nel caso in cui non venga trovato `WebApplicationFactory` un attributo con la chiave corretta, viene eseguito il fallback alla ricerca di un file di soluzione (*.sln*) e viene aggiunto il nome dell'assembly `TEntryPoint` alla directory della soluzione. La directory radice dell'app (percorso radice del contenuto) viene usata per individuare visualizzazioni e file di contenuto.

## <a name="disable-shadow-copying"></a>Disabilitare la copia shadow

La copia shadow determina l'esecuzione dei test in una directory diversa rispetto alla directory di output. Affinché i test funzionino correttamente, è necessario disabilitare la copia shadow. [L'app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) usa xUnit e disabilita la copia shadow per xUnit includendo un file *xunit.runner.json* con l'impostazione di configurazione corretta. Per altre informazioni, vedere [Configurazione di xUnit con JSON](https://xunit.github.io/docs/configuring-with-json.html).

Aggiungere il file *xunit.runner.json* alla radice del progetto di test con il contenuto seguente:

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>Smaltimento di oggetti

Dopo l'esecuzione `IClassFixture` dei test dell'implementazione, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) e [HttpClient](/dotnet/api/system.net.http.httpclient) vengono eliminati quando xUnit elimina [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). Se gli oggetti di cui fa parte lo `IClassFixture` sviluppatore richiedono l'eliminazione, eliminarli nell'implementazione. Per ulteriori informazioni, vedere [Implementazione di un metodo Dispose](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Esempio di test di integrazione

[L'app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) è composta da due app:The sample app is composed of two apps:

| App | Directory del progetto | Descrizione |
| --- | ----------------- | ----------- |
| App Message (SUT) | *Src/RazorPagesProject (progetto src/RazorPagesProject)* | Consente a un utente di aggiungere, eliminare uno, eliminare tutti e analizzare i messaggi. |
| Testare l'app | *test/RazorPagesProject.Tests* | Utilizzato per testare l'integrazione del SUT. |

I test possono essere eseguiti utilizzando le funzionalità di test incorporate di un IDE, ad esempio [Visual Studio](https://visualstudio.microsoft.com). Se si utilizza [Visual Studio Code](https://code.visualstudio.com/) o la riga di comando, eseguire il comando seguente al prompt dei comandi nella directory *tests/RazorPagesProject.Tests:*

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>Organizzazione dell'app messaggi (SUT)

Il SUT è un sistema di messaggi Razor Pages con le seguenti caratteristiche:

* La pagina Indice dell'app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) fornisce un'interfaccia utente e metodi del modello di pagina per controllare l'aggiunta, l'eliminazione e l'analisi dei messaggi (parole medie per messaggio).
* Un messaggio è `Message` descritto dalla classe (*Data/Message.cs*) con due proprietà: `Id` (chiave) e `Text` (messaggio). La `Text` proprietà è obbligatoria e limitata a 200 caratteri.
* I messaggi vengono archiviati utilizzando il [database in memoria](/ef/core/providers/in-memory/) di Entity Framework&#8224;.
* L'app contiene un livello di accesso ai `AppDbContext` dati (DAL) nella relativa classe di contesto del database, (*Data/AppDbContext.cs*).
* Se il database è vuoto all'avvio dell'app, l'archivio messaggi viene inizializzato con tre messaggi.
* L'app `/SecurePage` include un a che è accessibile solo da un utente autenticato.

&#8224;L'argomento di EF, [Test con InMemory](/ef/core/miscellaneous/testing/in-memory), viene illustrato come utilizzare un database in memoria per i test con MSTest. In questo argomento viene utilizzato il framework di test [xUnit.This](https://xunit.github.io/) topic uses the xUnit test framework. I concetti di test e le implementazioni di test in framework di test diversi sono simili ma non identici.

Anche se l'app non usa il modello di repository e non è un esempio efficace del [modello di unità di lavoro (UoW),](https://martinfowler.com/eaaCatalog/unitOfWork.html)Le pagine Razor supportano questi modelli di sviluppo. Per altre informazioni, vedere Progettazione del livello di [persistenza dell'infrastruttura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) e Logica del controller di [test](/aspnet/core/mvc/controllers/testing) (l'esempio implementa il modello di repository).

### <a name="test-app-organization"></a>Testare l'organizzazione delle app

L'app di test è un'app console all'interno della directory *tests/RazorPagesProject.Tests.*

| Testare la directory dell'app | Descrizione |
| ------------------ | ----------- |
| *AuthTests* | Contiene metodi di test per:<ul><li>Accesso a una pagina protetta da parte di un utente non autenticato.</li><li>Accesso a una pagina protetta da <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>parte di un utente autenticato con un oggetto fittizio .</li><li>Ottenere un profilo utente GitHub e controllare l'accesso utente del profilo.</li></ul> |
| *Test di base* | Contiene un metodo di test per il routing e il tipo di contenuto. |
| *IntegrationTests* | Contiene i test di integrazione `WebApplicationFactory` per la pagina Indice utilizzando la classe personalizzata. |
| *Helper/Utilità* | <ul><li>*Utilities.cs* contiene `InitializeDbForTests` il metodo utilizzato per eseguire il seededinare il database con i dati di test.</li><li>*HtmlHelpers.cs* fornisce un metodo per `IHtmlDocument` restituire un AngleSharp per l'utilizzo da parte dei metodi di test.</li><li>*HttpClientExtensions.cs* fornire overload `SendAsync` per inviare richieste al SUT.</li></ul> |

Il framework di test è [xUnit](https://xunit.github.io/). I test di integrazione vengono eseguiti utilizzando [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), che include [test.](/dotnet/api/microsoft.aspnetcore.testhost.testserver) Poiché il pacchetto [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) viene usato per configurare l'host di test e il server di test, i `TestHost` pacchetti e `TestServer` non richiedono riferimenti diretti ai pacchetti nel file di progetto o nella configurazione dello sviluppatore dell'app di test nell'app di test.

**Seeding del database per il test**

I test di integrazione richiedono in genere un set di dati di piccole dimensioni nel database prima dell'esecuzione del test. Ad esempio, un test di eliminazione chiama per l'eliminazione di un record di database, pertanto il database deve disporre di almeno un record affinché la richiesta di eliminazione abbia esito positivo.

L'app di esempio esegue il seeding del database con tre messaggi in Utilities.cs che i test possono usare durante l'esecuzione:The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

Il contesto del database di `Startup.ConfigureServices` SUT viene registrato nel relativo metodo. Il callback dell'app `builder.ConfigureServices` di test viene eseguito *dopo* l'esecuzione del codice dell'app. `Startup.ConfigureServices` Per utilizzare un database diverso per i test, il `builder.ConfigureServices`contesto del database dell'app deve essere sostituito in . Per altre informazioni, vedere la sezione [Personalizzare WebApplicationFactory.For](#customize-webapplicationfactory) more information, see the Customize WebApplicationFactory section.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

I test di integrazione assicurano che i componenti di un'app funzionino correttamente a un livello che include l'infrastruttura di supporto dell'app, ad esempio il database, il file system e la rete. ASP.NET Core supporta i test di integrazione utilizzando un framework di unit test con un host Web di test e un server di test in memoria.

In questo argomento si presuppone una conoscenza di base degli unit test. Se non si ha familiarità con i concetti di test, vedere l'argomento [Unit Testing in .NET Core e .NET Standard](/dotnet/core/testing/) e il relativo contenuto collegato.

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ( come[scaricare](xref:index#how-to-download-a-sample))

L'app di esempio è un'app Razor Pages e presuppone una conoscenza di base delle pagine Razor.The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages. Se non si ha familiarità con le pagine Razor, vedere gli argomenti seguenti:If undes with Razor Pages, see the following topics:

* [Introduzione a Razor Pages in ASP.NET Core](xref:razor-pages/index)
* [Introduzione a Razor Pages](xref:tutorials/razor-pages/razor-pages-start)
* [Unit test per Razor Pages](xref:test/razor-pages-tests)

> [!NOTE]
> Per testare le SPA, è stato consigliato uno strumento come [Selenium](https://www.seleniumhq.org/), in grado di automatizzare un browser.

## <a name="introduction-to-integration-tests"></a>Introduzione ai test di integrazione

I test di integrazione valutano i componenti di un'app a un livello più ampio rispetto agli [unit test.](/dotnet/core/testing/) Gli unit test vengono utilizzati per testare i componenti software isolati, ad esempio i singoli metodi di classe. I test di integrazione confermano che due o più componenti dell'app funzionano insieme per produrre un risultato previsto, includendo eventualmente tutti i componenti necessari per elaborare completamente una richiesta.

Questi test più ampi vengono usati per testare l'infrastruttura dell'app e l'intero framework, spesso includendo i componenti seguenti:These broader tests are used to test the app's infrastructure and whole framework, often including the following components:

* Database
* File system
* Dispositivi di rete
* Pipeline di richiesta-risposta

Gli unit test utilizzano componenti fabbricati, *noti* come falsi o *oggetti fittizi,* al posto dei componenti dell'infrastruttura.

A differenza degli unit test, i test di integrazione:In contrast to unit tests, integration tests:

* Usa i componenti effettivi utilizzati dall'app nell'ambiente di produzione.
* Richiedere più codice e trattamento dei dati.
* Prenditi più tempo per correre.

Pertanto, limitare l'utilizzo dei test di integrazione agli scenari di infrastruttura più importanti. Se un comportamento può essere testato usando uno unit test o uno unit test, scegliere lo unit test.

> [!TIP]
> Non scrivere test di integrazione per ogni possibile permutazione di dati e accesso ai file con database e file system. Indipendentemente dal numero di posizioni all'interno di un'app che interagiscono con database e file system, un set mirato di test di integrazione di lettura, scrittura, aggiornamento ed eliminazione è in genere in grado di testare adeguatamente i componenti di database e file system. Utilizzare gli unit test per i test di routine della logica del metodo che interagiscono con questi componenti. Negli unit test, l'uso di falsi/simulazioni dell'infrastruttura comporta un'esecuzione dei test più rapida.

> [!NOTE]
> Nelle discussioni sui test di integrazione, il progetto testato viene spesso chiamato *System Under Test*, o "SUT" in breve.
>
> *"SUT" viene usato in questo argomento per fare riferimento all'app ASP.NET Core testata.*

## <a name="aspnet-core-integration-tests"></a>Test di integrazione di ASP.NET Core

I test di integrazione in ASP.NET Core richiedono quanto segue:

* Un progetto di test viene utilizzato per contenere ed eseguire i test. Il progetto di test contiene un riferimento al SUT.
* Il progetto di test crea un host Web di test per SUT e utilizza un client server di test per gestire le richieste e le risposte con SUT.
* Un test runner viene utilizzato per eseguire i test e segnalare i risultati del test.

I test di integrazione seguono una sequenza di eventi che includono i soliti passi di test *Arrange*, *Act*e *Assert:*

1. L'host web di SUT è configurato.
1. Viene creato un client server di prova per inviare richieste all'app.
1. Viene eseguito il passo del test *Arrange:* l'app di test prepara una richiesta.
1. Viene eseguito il passo del test *Act:* il client invia la richiesta e riceve la risposta.
1. Viene eseguito il passo del test *Assert:* la risposta *effettiva* viene convalidata come *passaggio* o *esito negativo* in base a una risposta *prevista.*
1. Il processo continua fino all'esecuzione di tutti i test.
1. Vengono riportati i risultati del test.

In genere, l'host Web di test è configurato in modo diverso rispetto all'host Web normale dell'app per le esecuzioni dei test. Ad esempio, per i test è possibile usare un database diverso o impostazioni dell'app diverse.

I componenti dell'infrastruttura, ad esempio l'host Web di prova e il server di test in memoria ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), vengono forniti o gestiti dal pacchetto [Microsoft.AspNetCore.Mvc.Testing.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) L'uso di questo pacchetto semplifica la creazione e l'esecuzione dei test.

Il `Microsoft.AspNetCore.Mvc.Testing` pacchetto gestisce le attività seguenti:The package handles the following tasks:

* Copia il file delle dipendenze (*.deps*) dal SUT nella directory *bin* del progetto di test.
* Imposta la radice del [contenuto sulla](xref:fundamentals/index#content-root) radice del progetto SUT in modo che i file statici e le pagine/visualizzazioni vengano trovati quando vengono eseguiti i test.
* Fornisce la classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) per semplificare `TestServer`l'avvio di SUT con .

Nella documentazione relativa agli [unit test](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) viene descritto come impostare un progetto di test e un test runner, oltre a istruzioni dettagliate su come eseguire test e consigli su come denominare test e classi di test.

> [!NOTE]
> Quando si crea un progetto di test per un'app, separare gli unit test dai test di integrazione in progetti diversi. In questo modo si garantisce che i componenti di test dell'infrastruttura non vengano inclusi accidentalmente negli unit test. La separazione dei test di unità e di integrazione consente inoltre di controllare su quale set di test vengono eseguiti.

Non c'è praticamente alcuna differenza tra la configurazione per i test di pagine Razor apps e applicazioni MVC. L'unica differenza è nel nome dei test. In un'app Razor Pages, i test degli endpoint di pagina `IndexPageTests` vengono in genere denominati in base alla classe del modello di pagina, ad esempio per testare l'integrazione dei componenti per la pagina Indice. In un'app MVC, i test sono in genere organizzati in `HomeControllerTests` base alle classi controller e denominati in base ai controller testati, ad esempio per testare l'integrazione dei componenti per il controller Home.

## <a name="test-app-prerequisites"></a>Prerequisiti dell'app di testTest app prerequisites

Il progetto di test deve:

* Fare riferimento ai seguenti pacchetti:
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* Specificare il Web SDK`<Project Sdk="Microsoft.NET.Sdk.Web">`nel file di progetto ( ). Web SDK è necessario quando si fa riferimento al [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Questi prerequisiti possono essere visualizzati [nell'app di esempio.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) Esaminare il file *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj.* L'app di esempio usa il framework di test xUnit e la libreria del parser [AngleSharp,](https://anglesharp.github.io/) pertanto anche l'app di esempio fa riferimento:The sample app uses the [xUnit](https://xunit.github.io/) test framework and the AngleSharp parser library, so the sample app also references:

* [xunit](https://www.nuget.org/packages/xunit/)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp (Marca](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>Ambiente SUT

Se [l'ambiente](xref:fundamentals/environments) del SUT non è impostato, l'ambiente predefinito è Sviluppo.If the SUT's environment isn't set, the default s development to Development.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Test di base con il valore predefinito WebApplicationFactory

[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) viene utilizzato per creare un [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) per i test di integrazione. `TEntryPoint`è la classe del punto di `Startup` ingresso del SUT, di solito la classe.

Le classi di test *implementano* un'interfaccia di sistemazione di classe ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) per indicare che la classe contiene test e fornire istanze di oggetti condivisi tra i test nella classe.

Nella classe di `BasicTests`test `WebApplicationFactory` riportata di seguito, , viene utilizzato il `Get_EndpointsReturnSuccessAndCorrectContentType`metodo per eseguire il bootstrap di SUT e fornire [un httpClient](/dotnet/api/system.net.http.httpclient) a un metodo di test, . Il metodo controlla se il codice di stato della risposta ha esito positivo `Content-Type` (codici di stato nell'intervallo 200-299) e l'intestazione è `text/html; charset=utf-8` per diverse pagine dell'app.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) crea un'istanza di `HttpClient` che segue automaticamente reindirizza e gestisce i cookie.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Per impostazione predefinita, i cookie non essenziali non vengono mantenuti tra le richieste quando il criterio di [consenso GDPR](xref:security/gdpr) è abilitato. Per conservare i cookie non essenziali, ad esempio quelli utilizzati dal provider TempData, contrassegnarli come essenziali nei test. Per istruzioni su come contrassegnare un cookie come essenziale, vedere [Cookie essenziali](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Personalizza WebApplicationFactory

La configurazione dell'host Web può essere creata `WebApplicationFactory` indipendentemente dalle classi di test ereditando da per creare una o più factory personalizzate:

1. Ereditare `WebApplicationFactory` da ed eseguire l'override di [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) consente la configurazione della raccolta di servizi con [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   Il seeding del database [nell'app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) viene eseguito dal `InitializeDbForTests` metodo. Il metodo è descritto nella sezione Esempio di [test di integrazione: Test dell'organizzazione dell'app.](#test-app-organization)

2. Usare l'oggetto personalizzato `CustomWebApplicationFactory` nelle classi di test. L'esempio seguente usa `IndexPageTests` la factory nella classe:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Il client dell'app di esempio `HttpClient` è configurato per impedire i reindirizzamenti seguenti. Come spiegato più avanti nella sezione [Autenticazione fittizia,](#mock-authentication) questo consente ai test di controllare il risultato della prima risposta dell'app. La prima risposta è un reindirizzamento `Location` in molti di questi test con un'intestazione.

3. Un test tipico `HttpClient` usa i metodi di supporto e per elaborare la richiesta e la risposta:A typical test uses the e helper methods to process the request and the response:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Qualsiasi richiesta POST al SUT deve soddisfare il controllo antifalsificazione che viene automaticamente effettuato dal [sistema antifalsificazione](xref:security/data-protection/introduction)della protezione dei dati dell'app. Per richiedere la richiesta POST di un test, l'app di test deve:

1. Fai una richiesta per la pagina.
1. Analizzare il cookie dell'antifalandrale e il token di convalida della richiesta dalla risposta.
1. Effettuare la richiesta POST con il cookie antifalandria e richiedere il token di convalida sul posto.

I `SendAsync` metodi di estensione helper (*Helpers/HttpClientExtensions.cs*) e `GetDocumentAsync` il metodo helper (*Helpers/HtmlHelpers.cs*) [nell'app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) usano il parser [AngleSharp](https://anglesharp.github.io/) per gestire il controllo antiforgiato con i metodi seguenti:

* `GetDocumentAsync`&ndash; Riceve il [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) e `IHtmlDocument`restituisce un oggetto . `GetDocumentAsync`utilizza una factory che prepara una risposta `HttpResponseMessage` *virtuale* basata sull'originale . Per ulteriori informazioni, vedere la [documentazione di AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync`metodi di `HttpClient` estensione per comporre un [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) e chiamare [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) per inviare le richieste a SUT. Gli overload `SendAsync` per accettare`IHtmlFormElement`il modulo HTML ( ) e quanto segue:
  * Pulsante Invia del`IHtmlElement`modulo ( )
  * Insieme di`IEnumerable<KeyValuePair<string, string>>`valori di modulo ( )
  * Pulsante`IHtmlElement`Invia ( )`IEnumerable<KeyValuePair<string, string>>`e valori del modulo ( )

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) è una libreria di analisi di terze parti usata a scopo dimostrativo in questo argomento e nell'app di esempio. AngleSharp non è supportato o necessario per il test di integrazione delle app ASP.NET Core. È possibile utilizzare altri parser, ad esempio [Html Agility Pack (HAP)](https://html-agility-pack.net/). Un altro approccio consiste nello scrivere codice per gestire direttamente il token di verifica della richiesta del sistema antifalsificazione e il cookie antifalsificazione.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Personalizzare il client con WithWebHostBuilder

Quando è necessaria una configurazione aggiuntiva all'interno `WebApplicationFactory` di un metodo di test, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) crea un nuovo con un [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) ulteriormente personalizzato dalla configurazione.

Il `Post_DeleteMessageHandler_ReturnsRedirectToRoot` metodo di test dell'app `WithWebHostBuilder`di [esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) illustra l'uso di . Questo test esegue un'eliminazione di record nel database attivando un invio di moduli in SUT.

Poiché un `IndexPageTests` altro test nella classe esegue un'operazione che elimina tutti `Post_DeleteMessageHandler_ReturnsRedirectToRoot` i record nel database e può essere eseguito prima del metodo, il database viene reseguito in questo metodo di test per garantire che sia presente un record per l'eliminazione di SUT. La selezione del primo `messages` pulsante di eliminazione del modulo nel SUT viene simulata nella richiesta al SUT:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Opzioni client

Nella tabella seguente vengono illustrate le opzioni `HttpClient` [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) predefinite disponibili durante la creazione di istanze.

| Opzione | Descrizione | Predefinito |
| ------ | ----------- | ------- |
| [AllowAutoRedirect (AllowAutoRedirect)](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Ottiene o imposta `HttpClient` un valore che indica se le istanze devono seguire automaticamente le risposte di reindirizzamento. | `true` |
| [Baseaddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Ottiene o imposta l'indirizzo di base delle `HttpClient` istanze. | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Ottiene o `HttpClient` imposta un valore che indica se le istanze devono gestire i cookie. | `true` |
| [MaxAutomaticReindirizzami](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Ottiene o imposta il numero massimo `HttpClient` di risposte di reindirizzamento che le istanze devono seguire. | 7 |

Creare `WebApplicationFactoryClientOptions` la classe e passarla al metodo CreateClient (i valori predefiniti vengono visualizzati nell'esempio di codice):Create the class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Iniettare servizi fittizi

I servizi possono essere sottoposti a override in un test con una chiamata a [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) nel generatore host. **Per inserire servizi fittizi, SUT `Startup` deve `Startup.ConfigureServices` avere una classe con un metodo.**

L'esempio SUT include un servizio con ambito che restituisce un'offerta. L'offerta viene incorporata in un campo nascosto nella pagina Indice quando viene richiesta la pagina Indice.

*Servizi/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Servizi/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pagine/Index.cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

Il markup seguente viene generato quando viene eseguita l'app SUT:The following markup is generated when the SUT app is run:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Per testare il servizio e l'inserimento di preventivi in un test di integrazione, un servizio fittizio viene inserito nel SUT dal test. Il servizio fittizio sostituisce `QuoteService` l'app con un servizio `TestQuoteService`fornito dall'app di test, denominato :

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices`viene chiamato e il servizio con ambito è registrato:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Il markup prodotto durante l'esecuzione del test `TestQuoteService`riflette il testo del preventivo fornito da , pertanto l'asserzione passa:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Autenticazione fittizia

I test `AuthTests` nella classe verificano che un endpoint sicuro:Tests in the class check that a secure endpoint:

* Reindirizza un utente non autenticato alla pagina di accesso dell'app.
* Restituisce il contenuto per un utente autenticato.

In SUT, `/SecurePage` la pagina utilizza una convenzione [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) per applicare un [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) alla pagina. Per ulteriori informazioni, consultate Convenzioni di [autorizzazione di Razor Pages](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

Nel `Get_SecurePageRedirectsAnUnauthenticatedUser` test, un [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) è impostato per non consentire reindirizzamenti impostando [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) su `false`:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

Impedendo al client di seguire il reindirizzamento, è possibile effettuare i seguenti controlli:

* Il codice di stato restituito da SUT può essere confrontato con il risultato [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) previsto, non con il codice di stato finale dopo il reindirizzamento alla pagina di accesso, che sarebbe [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).
* Il `Location` valore dell'intestazione nelle intestazioni di `http://localhost/Identity/Account/Login`risposta viene controllato per verificare `Location` che venga avviato con , non con la risposta finale della pagina di accesso, in cui l'intestazione non sarebbe presente.

L'app di <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> test può simulare un in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) per testare gli aspetti di autenticazione e autorizzazione. Uno scenario minimo restituisce un [valore AuthenticateResult.Success:](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

`TestAuthHandler` L'oggetto viene chiamato per autenticare un `Test` `AddAuthentication` utente quando `ConfigureTestServices`lo schema di autenticazione è impostato su where is registered for :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Per ulteriori `WebApplicationFactoryClientOptions`informazioni su , vedere la sezione [Opzioni client.](#client-options)

## <a name="set-the-environment"></a>Impostare l'ambiente

Per impostazione predefinita, l'ambiente host e app di SUT è configurato per usare l'ambiente di sviluppo. Per sostituire l'ambiente di SUT:

* Impostare `ASPNETCORE_ENVIRONMENT` la variabile di `Staging` `Production`ambiente, ad esempio , `Testing`, o un altro valore personalizzato, ad esempio ).
* Eseguire `CreateHostBuilder` l'override nell'app di `ASPNETCORE`test per leggere le variabili di ambiente precedute da .

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>In che modo l'infrastruttura di test deduce il percorso radice del contenuto dell'appHow the test infrastructure infers the app content root path

Il `WebApplicationFactory` costruttore deduce il percorso radice del [contenuto](xref:fundamentals/index#content-root) dell'app cercando un [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) `System.Reflection.Assembly.FullName`nell'assembly contenente i test di integrazione con una chiave uguale all'assembly. `TEntryPoint` Nel caso in cui non venga trovato `WebApplicationFactory` un attributo con la chiave corretta, viene eseguito il fallback alla ricerca di un file di soluzione (*.sln*) e viene aggiunto il nome dell'assembly `TEntryPoint` alla directory della soluzione. La directory radice dell'app (percorso radice del contenuto) viene usata per individuare visualizzazioni e file di contenuto.

## <a name="disable-shadow-copying"></a>Disabilitare la copia shadow

La copia shadow determina l'esecuzione dei test in una directory diversa rispetto alla directory di output. Affinché i test funzionino correttamente, è necessario disabilitare la copia shadow. [L'app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) usa xUnit e disabilita la copia shadow per xUnit includendo un file *xunit.runner.json* con l'impostazione di configurazione corretta. Per altre informazioni, vedere [Configurazione di xUnit con JSON](https://xunit.github.io/docs/configuring-with-json.html).

Aggiungere il file *xunit.runner.json* alla radice del progetto di test con il contenuto seguente:

```json
{
  "shadowCopy": false
}
```

Se si utilizza Visual Studio, impostare la proprietà **Copia nella directory** di output del file su Copia **sempre**. Se non si usa `Content` Visual Studio, aggiungi una destinazione al file di progetto dell'app di test:If not using Visual Studio, add a target to the test app's project file:

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a>Smaltimento di oggetti

Dopo l'esecuzione `IClassFixture` dei test dell'implementazione, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) e [HttpClient](/dotnet/api/system.net.http.httpclient) vengono eliminati quando xUnit elimina [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). Se gli oggetti di cui fa parte lo `IClassFixture` sviluppatore richiedono l'eliminazione, eliminarli nell'implementazione. Per ulteriori informazioni, vedere [Implementazione di un metodo Dispose](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Esempio di test di integrazione

[L'app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) è composta da due app:The sample app is composed of two apps:

| App | Directory del progetto | Descrizione |
| --- | ----------------- | ----------- |
| App Message (SUT) | *Src/RazorPagesProject (progetto src/RazorPagesProject)* | Consente a un utente di aggiungere, eliminare uno, eliminare tutti e analizzare i messaggi. |
| Testare l'app | *test/RazorPagesProject.Tests* | Utilizzato per testare l'integrazione del SUT. |

I test possono essere eseguiti utilizzando le funzionalità di test incorporate di un IDE, ad esempio [Visual Studio](https://visualstudio.microsoft.com). Se si utilizza [Visual Studio Code](https://code.visualstudio.com/) o la riga di comando, eseguire il comando seguente al prompt dei comandi nella directory *tests/RazorPagesProject.Tests:*

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a>Organizzazione dell'app messaggi (SUT)

Il SUT è un sistema di messaggi Razor Pages con le seguenti caratteristiche:

* La pagina Indice dell'app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) fornisce un'interfaccia utente e metodi del modello di pagina per controllare l'aggiunta, l'eliminazione e l'analisi dei messaggi (parole medie per messaggio).
* Un messaggio è `Message` descritto dalla classe (*Data/Message.cs*) con due proprietà: `Id` (chiave) e `Text` (messaggio). La `Text` proprietà è obbligatoria e limitata a 200 caratteri.
* I messaggi vengono archiviati utilizzando il [database in memoria](/ef/core/providers/in-memory/) di Entity Framework&#8224;.
* L'app contiene un livello di accesso ai `AppDbContext` dati (DAL) nella relativa classe di contesto del database, (*Data/AppDbContext.cs*).
* Se il database è vuoto all'avvio dell'app, l'archivio messaggi viene inizializzato con tre messaggi.
* L'app `/SecurePage` include un a che è accessibile solo da un utente autenticato.

&#8224;L'argomento di EF, [Test con InMemory](/ef/core/miscellaneous/testing/in-memory), viene illustrato come utilizzare un database in memoria per i test con MSTest. In questo argomento viene utilizzato il framework di test [xUnit.This](https://xunit.github.io/) topic uses the xUnit test framework. I concetti di test e le implementazioni di test in framework di test diversi sono simili ma non identici.

Anche se l'app non usa il modello di repository e non è un esempio efficace del [modello di unità di lavoro (UoW),](https://martinfowler.com/eaaCatalog/unitOfWork.html)Le pagine Razor supportano questi modelli di sviluppo. Per altre informazioni, vedere Progettazione del livello di [persistenza dell'infrastruttura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) e Logica del controller di [test](/aspnet/core/mvc/controllers/testing) (l'esempio implementa il modello di repository).

### <a name="test-app-organization"></a>Testare l'organizzazione delle app

L'app di test è un'app console all'interno della directory *tests/RazorPagesProject.Tests.*

| Testare la directory dell'app | Descrizione |
| ------------------ | ----------- |
| *AuthTests* | Contiene metodi di test per:<ul><li>Accesso a una pagina protetta da parte di un utente non autenticato.</li><li>Accesso a una pagina protetta da <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>parte di un utente autenticato con un oggetto fittizio .</li><li>Ottenere un profilo utente GitHub e controllare l'accesso utente del profilo.</li></ul> |
| *Test di base* | Contiene un metodo di test per il routing e il tipo di contenuto. |
| *IntegrationTests* | Contiene i test di integrazione `WebApplicationFactory` per la pagina Indice utilizzando la classe personalizzata. |
| *Helper/Utilità* | <ul><li>*Utilities.cs* contiene `InitializeDbForTests` il metodo utilizzato per eseguire il seededinare il database con i dati di test.</li><li>*HtmlHelpers.cs* fornisce un metodo per `IHtmlDocument` restituire un AngleSharp per l'utilizzo da parte dei metodi di test.</li><li>*HttpClientExtensions.cs* fornire overload `SendAsync` per inviare richieste al SUT.</li></ul> |

Il framework di test è [xUnit](https://xunit.github.io/). I test di integrazione vengono eseguiti utilizzando [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), che include [test.](/dotnet/api/microsoft.aspnetcore.testhost.testserver) Poiché il pacchetto [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) viene usato per configurare l'host di test e il server di test, i `TestHost` pacchetti e `TestServer` non richiedono riferimenti diretti ai pacchetti nel file di progetto o nella configurazione dello sviluppatore dell'app di test nell'app di test.

**Seeding del database per il test**

I test di integrazione richiedono in genere un set di dati di piccole dimensioni nel database prima dell'esecuzione del test. Ad esempio, un test di eliminazione chiama per l'eliminazione di un record di database, pertanto il database deve disporre di almeno un record affinché la richiesta di eliminazione abbia esito positivo.

L'app di esempio esegue il seeding del database con tre messaggi in Utilities.cs che i test possono usare durante l'esecuzione:The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a>Risorse aggiuntive

* [Unit test](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
