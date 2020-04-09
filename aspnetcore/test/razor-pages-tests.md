---
title: Razor Pages unit tests in ASP.NET Core
author: rick-anderson
description: Scopri come creare unit test per le app Pagine Razor.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: test/razor-pages-tests
ms.openlocfilehash: 0e217b6b7f15519a3da44f5d074cf80fa96a3b3a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664409"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>Razor Pages unit tests in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core supporta gli unit test delle app Razor Pages. I test del livello di accesso ai dati (DAL) e dei modelli di pagina contribuiscono a garantire:

* Parti di un'app Razor Pages funzionano in modo indipendente e insieme come unità durante la costruzione dell'app.
* Le classi e i metodi hanno ambiti di responsabilità limitati.
* Esiste documentazione aggiuntiva sul modo in cui l'app deve comportarsi.
* Le regressioni, ovvero errori causati dagli aggiornamenti al codice, vengono rilevate durante la compilazione e la distribuzione automatizzate.

Questo argomento presuppone che tu abbia una conoscenza di base delle app e degli unit test di Razor Pages. Se non si ha familiarità con le app o i concetti di test di Razor Pages, vedere gli argomenti seguenti:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Testing unità di C# in .NET Core usando il test dotnet e xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ( come[scaricare](xref:index#how-to-download-a-sample))

Il progetto di esempio è composto da due app:The sample project is composed of two apps:

| App         | Cartella del progetto                     | Descrizione |
| ----------- | ---------------------------------- | ----------- |
| App Messaggio | *Src/RazorPagesTestSample (esempio)*         | Consente a un utente di aggiungere un messaggio, eliminare un messaggio, eliminare tutti i messaggi e analizzare i messaggi (trovare il numero medio di parole per messaggio). |
| Testare l'app    | *test/RazorPagesTestSample.Test* | Utilizzato per unit test del modello di pagina DAL e Index dell'app messaggio. |

I test possono essere eseguiti utilizzando le funzionalità di test incorporate di un IDE, ad esempio [Visual Studio](/visualstudio/test/unit-test-your-code) o Visual Studio [per Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). Se si utilizza [Visual Studio Code](https://code.visualstudio.com/) o la riga di comando, eseguire il comando seguente al prompt dei comandi nella cartella *tests/RazorPagesTestSample.Tests:*

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organizzazione dell'app per i messaggi

L'app di messaggi è un sistema di messaggi Razor Pages con le seguenti caratteristiche:

* La pagina Indice dell'app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) fornisce un'interfaccia utente e metodi del modello di pagina per controllare l'aggiunta, l'eliminazione e l'analisi dei messaggi (trovare il numero medio di parole per messaggio).
* Un messaggio è `Message` descritto dalla classe (*Data/Message.cs*) con due proprietà: `Id` (chiave) e `Text` (messaggio). La `Text` proprietà è obbligatoria e limitata a 200 caratteri.
* I messaggi vengono archiviati utilizzando il [database in memoria](/ef/core/providers/in-memory/) di Entity Framework&#8224;.
* L'app contiene un DAL nella `AppDbContext` relativa classe di contesto del database, (*Data/AppDbContext.cs*). I metodi DAL `virtual`sono contrassegnati , che consente di simulare i metodi da utilizzare nei test.
* Se il database è vuoto all'avvio dell'app, l'archivio messaggi viene inizializzato con tre messaggi. Questi messaggi di *seeding* vengono utilizzati anche nei test.

&#8224;L'argomento di EF, [Test con InMemory](/ef/core/miscellaneous/testing/in-memory), viene illustrato come utilizzare un database in memoria per i test con MSTest. In questo argomento viene utilizzato il framework di test [xUnit.This](https://xunit.github.io/) topic uses the xUnit test framework. I concetti di test e le implementazioni di test in framework di test diversi sono simili ma non identici.

Anche se l'app di esempio non usa il modello di repository e non è un esempio efficace del [modello di unità di lavoro (UoW),](https://martinfowler.com/eaaCatalog/unitOfWork.html)le pagine Razor supportano questi modelli di sviluppo. Per altre informazioni, vedere Progettazione <xref:mvc/controllers/testing> del livello di [persistenza dell'infrastruttura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) e (l'esempio implementa il modello di repository).

## <a name="test-app-organization"></a>Testare l'organizzazione delle app

L'app di test è un'app console all'interno della cartella *tests/RazorPagesTestSample.Tests.*

| Cartella dell'app di test | Descrizione |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* contiene gli unit test per il DAL.</li><li>*IndexPageTests.cs* contiene gli unit test per il modello di pagina indice.</li></ul> |
| *Servizi pubblici*     | Contiene `TestDbContextOptions` il metodo utilizzato per creare nuove opzioni di contesto del database per ogni unit test DAL in modo che il database venga reimpostato sulla condizione di base per ogni test. |

Il framework di test è [xUnit](https://xunit.github.io/). Il framework di simulazione [dell'oggetto](https://github.com/moq/moq4)è Moq .

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Unit test del livello di accesso ai dati (DAL)

L'app messaggio dispone di un `AppDbContext` DAL con quattro metodi contenuti nella classe (*src/RazorPagesTestSample/Data/AppDbContext.cs*). Ogni metodo ha uno o due unit test nell'app di test.

| METODO DAL               | Funzione                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Ottiene un `List<Message>` dal database ordinato `Text` dalla proprietà. |
| `AddMessageAsync`        | Aggiunge `Message` a al database.                                          |
| `DeleteAllMessagesAsync` | Elimina tutte `Message` le voci dal database.                           |
| `DeleteMessageAsync`     | Elimina un `Message` singolo dal `Id`database da .                      |

Gli unit test <xref:Microsoft.EntityFrameworkCore.DbContextOptions> del DAL `AppDbContext` richiedono quando si crea un nuovo per ogni test. Un approccio `DbContextOptions` alla creazione di per <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>ogni test consiste nell'utilizzare un:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Il problema con questo approccio è che ogni test riceve il database in qualsiasi stato il test precedente lo ha lasciato. Questo può essere problematico quando si tenta di scrivere unit test atomici che non interferiscono tra loro. Per forzare l'oggetto `AppDbContext` a utilizzare un `DbContextOptions` nuovo contesto di database per ogni test, fornire un'istanza basata su un nuovo provider di servizi. L'app di test mostra `Utilities` come `TestDbContextOptions` eseguire questa operazione usando il relativo metodo di classe (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

L'utilizzo degli `DbContextOptions` unit test DAL consente a ogni test di essere eseguito in modo atomico con una nuova istanza di database:Using the in the DAL unit tests allows each test to run atomically with a fresh database instance:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Ogni metodo di `DataAccessLayerTest` test nella classe (*UnitTests/DataAccessLayerTest.cs*) segue un modello Arrange-Act-Assert simile:

1. Disponi: il database è configurato per il test e/o viene definito il risultato previsto.
1. Atto: Il test viene eseguito.
1. Asserzione: le asserzioni vengono effettuate per determinare se il risultato del test ha esito positivo.

Ad esempio, `DeleteMessageAsync` il metodo è responsabile della `Id` rimozione di un singolo messaggio identificato dal relativo *(src/RazorPagesTestSample/Data/AppDbContext.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Esistono due test per questo metodo. Un test verifica che il metodo elimini un messaggio quando il messaggio è presente nel database. L'altro metodo verifica che il database `Id` non cambi se il messaggio per l'eliminazione non esiste. Il `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` metodo è mostrato di seguito:

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

In primo luogo, il metodo esegue il passaggio Arrange, in cui viene eseguita la preparazione per il passaggio Act. I messaggi di seeding `seedMessages`vengono ottenuti e conservati in . I messaggi di seeding vengono salvati nel database. Il messaggio `Id` con `1` un or è impostato per l'eliminazione. Quando `DeleteMessageAsync` viene eseguito il metodo, i messaggi previsti devono avere `Id` `1`tutti i messaggi ad eccezione di quello con un oggetto di tipo . La `expectedMessages` variabile rappresenta questo risultato previsto.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Il metodo agisce: il `DeleteMessageAsync` metodo viene `recId` `1`eseguito passando il di :

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Infine, il metodo `Messages` ottiene il dal contesto `expectedMessages` e lo confronta con l'affermazione che i due sono uguali:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Per confrontare che `List<Message>` i due sono gli stessi:

* I messaggi sono `Id`ordinati per .
* Le coppie di `Text` messaggi vengono confrontate nella proprietà.

Un metodo di `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` test simile, controlla il risultato del tentativo di eliminare un messaggio che non esiste. In questo caso, i messaggi previsti nel database devono `DeleteMessageAsync` essere uguali ai messaggi effettivi dopo l'esecuzione del metodo. Non dovrebbe essere apportata alcuna modifica al contenuto del database:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Unit test dei metodi del modello di pagina

Un altro set di unit test è responsabile dei test dei metodi del modello di pagina. Nell'app messaggio, i modelli di `IndexModel` pagina indice si trovano nella classe in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.

| Metodo del modello di pagina | Funzione |
| ----------------- | -------- |
| `OnGetAsync` | Ottiene i messaggi dal DAL per `GetMessagesAsync` l'interfaccia utente utilizzando il metodo . |
| `OnPostAddMessageAsync` | Se [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) è valido, chiama `AddMessageAsync` per aggiungere un messaggio al database. |
| `OnPostDeleteAllMessagesAsync` | Chiamate `DeleteAllMessagesAsync` per eliminare tutti i messaggi nel database. |
| `OnPostDeleteMessageAsync` | Viene `DeleteMessageAsync` eseguito per eliminare `Id` un messaggio con l'oggetto specificato. |
| `OnPostAnalyzeMessagesAsync` | Se nel database sono presenti uno o più messaggi, calcola il numero medio di parole per messaggio. |

I metodi del modello di pagina `IndexPageTests` vengono testati utilizzando sette test nella classe (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*). I test utilizzano il familiare modello Arrange-Assert-Act. Questi test si concentrano su:

* Determinare se i metodi seguono il comportamento corretto quando [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) non è valido.
* La conferma dei metodi <xref:Microsoft.AspNetCore.Mvc.IActionResult>produce il file .
* Verifica che le assegnazioni dei valori delle proprietà vengano eseguite correttamente.

Questo gruppo di test spesso si derisono i metodi del DAL per produrre i dati previsti per il passaggio Act in cui viene eseguito un metodo del modello di pagina. Ad esempio, `GetMessagesAsync` il `AppDbContext` metodo dell'oggetto viene simulato per produrre output. Quando un metodo del modello di pagina esegue questo metodo, la simulazione restituisce il risultato. I dati non provengono dal database. In questo modo vengono create condizioni di test affidabili e prevedibili per l'utilizzo del DAL nei test del modello di pagina.

Il `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test mostra `GetMessagesAsync` come il metodo viene simulato per il modello di pagina:The test shows how the method is mocked for the page model:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Quando `OnGetAsync` il metodo viene eseguito nel passaggio Act, `GetMessagesAsync` chiama il metodo del modello di pagina.

Passaggio unit test (*test/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage`metodo del `OnGetAsync` modello di pagina (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

Il `GetMessagesAsync` metodo nel DAL non restituisce il risultato per questa chiamata al metodo. La versione fittizia del metodo restituisce il risultato.

Nel `Assert` passaggio, i`actualMessages`messaggi effettivi ( `Messages` ) vengono assegnati dalla proprietà del modello di pagina. Un controllo del tipo viene eseguito anche quando vengono assegnati i messaggi. I messaggi previsti ed `Text` effettivi vengono confrontati in base alle relative proprietà. Il test afferma che `List<Message>` le due istanze contengono gli stessi messaggi.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Altri test in questo gruppo creano <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>oggetti <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>modello <xref:Microsoft.AspNetCore.Mvc.ActionContext> di pagina `PageContext`che `ViewDataDictionary`includono `PageContext`, , per stabilire i , a e a . Questi sono utili nello svolgimento di test. Ad esempio, l'app di `ModelState` messaggi <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> stabilisce un <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> errore con `OnPostAddMessageAsync` per verificare che venga restituito un valore valido quando viene eseguito:For example, the message app establishes a error with to check that a valid is returned when is executed:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Risorse aggiuntive

* [Testing unità di C# in .NET Core usando il test dotnet e xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Eseguire unit test del codice](/visualstudio/test/unit-test-your-code) (Visual Studio)Unit Test Your Code (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Creazione di una soluzione .NET Core completa in macOS con Visual Studio per Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Introduzione a xUnit.net: Utilizzo di .NET Core con la riga di comando di .NET SDK](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Guida introduttiva di Moq](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core supporta gli unit test delle app Razor Pages. I test del livello di accesso ai dati (DAL) e dei modelli di pagina contribuiscono a garantire:

* Parti di un'app Razor Pages funzionano in modo indipendente e insieme come unità durante la costruzione dell'app.
* Le classi e i metodi hanno ambiti di responsabilità limitati.
* Esiste documentazione aggiuntiva sul modo in cui l'app deve comportarsi.
* Le regressioni, ovvero errori causati dagli aggiornamenti al codice, vengono rilevate durante la compilazione e la distribuzione automatizzate.

Questo argomento presuppone che tu abbia una conoscenza di base delle app e degli unit test di Razor Pages. Se non si ha familiarità con le app o i concetti di test di Razor Pages, vedere gli argomenti seguenti:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Testing unità di C# in .NET Core usando il test dotnet e xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ( come[scaricare](xref:index#how-to-download-a-sample))

Il progetto di esempio è composto da due app:The sample project is composed of two apps:

| App         | Cartella del progetto                     | Descrizione |
| ----------- | ---------------------------------- | ----------- |
| App Messaggio | *Src/RazorPagesTestSample (esempio)*         | Consente a un utente di aggiungere un messaggio, eliminare un messaggio, eliminare tutti i messaggi e analizzare i messaggi (trovare il numero medio di parole per messaggio). |
| Testare l'app    | *test/RazorPagesTestSample.Test* | Utilizzato per unit test del modello di pagina DAL e Index dell'app messaggio. |

I test possono essere eseguiti utilizzando le funzionalità di test incorporate di un IDE, ad esempio [Visual Studio](/visualstudio/test/unit-test-your-code) o Visual Studio [per Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). Se si utilizza [Visual Studio Code](https://code.visualstudio.com/) o la riga di comando, eseguire il comando seguente al prompt dei comandi nella cartella *tests/RazorPagesTestSample.Tests:*

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organizzazione dell'app per i messaggi

L'app di messaggi è un sistema di messaggi Razor Pages con le seguenti caratteristiche:

* La pagina Indice dell'app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) fornisce un'interfaccia utente e metodi del modello di pagina per controllare l'aggiunta, l'eliminazione e l'analisi dei messaggi (trovare il numero medio di parole per messaggio).
* Un messaggio è `Message` descritto dalla classe (*Data/Message.cs*) con due proprietà: `Id` (chiave) e `Text` (messaggio). La `Text` proprietà è obbligatoria e limitata a 200 caratteri.
* I messaggi vengono archiviati utilizzando il [database in memoria](/ef/core/providers/in-memory/) di Entity Framework&#8224;.
* L'app contiene un DAL nella `AppDbContext` relativa classe di contesto del database, (*Data/AppDbContext.cs*). I metodi DAL `virtual`sono contrassegnati , che consente di simulare i metodi da utilizzare nei test.
* Se il database è vuoto all'avvio dell'app, l'archivio messaggi viene inizializzato con tre messaggi. Questi messaggi di *seeding* vengono utilizzati anche nei test.

&#8224;L'argomento di EF, [Test con InMemory](/ef/core/miscellaneous/testing/in-memory), viene illustrato come utilizzare un database in memoria per i test con MSTest. In questo argomento viene utilizzato il framework di test [xUnit.This](https://xunit.github.io/) topic uses the xUnit test framework. I concetti di test e le implementazioni di test in framework di test diversi sono simili ma non identici.

Anche se l'app di esempio non usa il modello di repository e non è un esempio efficace del [modello di unità di lavoro (UoW),](https://martinfowler.com/eaaCatalog/unitOfWork.html)le pagine Razor supportano questi modelli di sviluppo. Per altre informazioni, vedere Progettazione <xref:mvc/controllers/testing> del livello di [persistenza dell'infrastruttura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) e (l'esempio implementa il modello di repository).

## <a name="test-app-organization"></a>Testare l'organizzazione delle app

L'app di test è un'app console all'interno della cartella *tests/RazorPagesTestSample.Tests.*

| Cartella dell'app di test | Descrizione |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* contiene gli unit test per il DAL.</li><li>*IndexPageTests.cs* contiene gli unit test per il modello di pagina indice.</li></ul> |
| *Servizi pubblici*     | Contiene `TestDbContextOptions` il metodo utilizzato per creare nuove opzioni di contesto del database per ogni unit test DAL in modo che il database venga reimpostato sulla condizione di base per ogni test. |

Il framework di test è [xUnit](https://xunit.github.io/). Il framework di simulazione [dell'oggetto](https://github.com/moq/moq4)è Moq .

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Unit test del livello di accesso ai dati (DAL)

L'app messaggio dispone di un `AppDbContext` DAL con quattro metodi contenuti nella classe (*src/RazorPagesTestSample/Data/AppDbContext.cs*). Ogni metodo ha uno o due unit test nell'app di test.

| METODO DAL               | Funzione                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Ottiene un `List<Message>` dal database ordinato `Text` dalla proprietà. |
| `AddMessageAsync`        | Aggiunge `Message` a al database.                                          |
| `DeleteAllMessagesAsync` | Elimina tutte `Message` le voci dal database.                           |
| `DeleteMessageAsync`     | Elimina un `Message` singolo dal `Id`database da .                      |

Gli unit test <xref:Microsoft.EntityFrameworkCore.DbContextOptions> del DAL `AppDbContext` richiedono quando si crea un nuovo per ogni test. Un approccio `DbContextOptions` alla creazione di per <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>ogni test consiste nell'utilizzare un:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Il problema con questo approccio è che ogni test riceve il database in qualsiasi stato il test precedente lo ha lasciato. Questo può essere problematico quando si tenta di scrivere unit test atomici che non interferiscono tra loro. Per forzare l'oggetto `AppDbContext` a utilizzare un `DbContextOptions` nuovo contesto di database per ogni test, fornire un'istanza basata su un nuovo provider di servizi. L'app di test mostra `Utilities` come `TestDbContextOptions` eseguire questa operazione usando il relativo metodo di classe (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

L'utilizzo degli `DbContextOptions` unit test DAL consente a ogni test di essere eseguito in modo atomico con una nuova istanza di database:Using the in the DAL unit tests allows each test to run atomically with a fresh database instance:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Ogni metodo di `DataAccessLayerTest` test nella classe (*UnitTests/DataAccessLayerTest.cs*) segue un modello Arrange-Act-Assert simile:

1. Disponi: il database è configurato per il test e/o viene definito il risultato previsto.
1. Atto: Il test viene eseguito.
1. Asserzione: le asserzioni vengono effettuate per determinare se il risultato del test ha esito positivo.

Ad esempio, `DeleteMessageAsync` il metodo è responsabile della `Id` rimozione di un singolo messaggio identificato dal relativo *(src/RazorPagesTestSample/Data/AppDbContext.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Esistono due test per questo metodo. Un test verifica che il metodo elimini un messaggio quando il messaggio è presente nel database. L'altro metodo verifica che il database `Id` non cambi se il messaggio per l'eliminazione non esiste. Il `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` metodo è mostrato di seguito:

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

In primo luogo, il metodo esegue il passaggio Arrange, in cui viene eseguita la preparazione per il passaggio Act. I messaggi di seeding `seedMessages`vengono ottenuti e conservati in . I messaggi di seeding vengono salvati nel database. Il messaggio `Id` con `1` un or è impostato per l'eliminazione. Quando `DeleteMessageAsync` viene eseguito il metodo, i messaggi previsti devono avere `Id` `1`tutti i messaggi ad eccezione di quello con un oggetto di tipo . La `expectedMessages` variabile rappresenta questo risultato previsto.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Il metodo agisce: il `DeleteMessageAsync` metodo viene `recId` `1`eseguito passando il di :

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Infine, il metodo `Messages` ottiene il dal contesto `expectedMessages` e lo confronta con l'affermazione che i due sono uguali:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Per confrontare che `List<Message>` i due sono gli stessi:

* I messaggi sono `Id`ordinati per .
* Le coppie di `Text` messaggi vengono confrontate nella proprietà.

Un metodo di `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` test simile, controlla il risultato del tentativo di eliminare un messaggio che non esiste. In questo caso, i messaggi previsti nel database devono `DeleteMessageAsync` essere uguali ai messaggi effettivi dopo l'esecuzione del metodo. Non dovrebbe essere apportata alcuna modifica al contenuto del database:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Unit test dei metodi del modello di pagina

Un altro set di unit test è responsabile dei test dei metodi del modello di pagina. Nell'app messaggio, i modelli di `IndexModel` pagina indice si trovano nella classe in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.

| Metodo del modello di pagina | Funzione |
| ----------------- | -------- |
| `OnGetAsync` | Ottiene i messaggi dal DAL per `GetMessagesAsync` l'interfaccia utente utilizzando il metodo . |
| `OnPostAddMessageAsync` | Se [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) è valido, chiama `AddMessageAsync` per aggiungere un messaggio al database. |
| `OnPostDeleteAllMessagesAsync` | Chiamate `DeleteAllMessagesAsync` per eliminare tutti i messaggi nel database. |
| `OnPostDeleteMessageAsync` | Viene `DeleteMessageAsync` eseguito per eliminare `Id` un messaggio con l'oggetto specificato. |
| `OnPostAnalyzeMessagesAsync` | Se nel database sono presenti uno o più messaggi, calcola il numero medio di parole per messaggio. |

I metodi del modello di pagina `IndexPageTests` vengono testati utilizzando sette test nella classe (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*). I test utilizzano il familiare modello Arrange-Assert-Act. Questi test si concentrano su:

* Determinare se i metodi seguono il comportamento corretto quando [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) non è valido.
* La conferma dei metodi <xref:Microsoft.AspNetCore.Mvc.IActionResult>produce il file .
* Verifica che le assegnazioni dei valori delle proprietà vengano eseguite correttamente.

Questo gruppo di test spesso si derisono i metodi del DAL per produrre i dati previsti per il passaggio Act in cui viene eseguito un metodo del modello di pagina. Ad esempio, `GetMessagesAsync` il `AppDbContext` metodo dell'oggetto viene simulato per produrre output. Quando un metodo del modello di pagina esegue questo metodo, la simulazione restituisce il risultato. I dati non provengono dal database. In questo modo vengono create condizioni di test affidabili e prevedibili per l'utilizzo del DAL nei test del modello di pagina.

Il `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test mostra `GetMessagesAsync` come il metodo viene simulato per il modello di pagina:The test shows how the method is mocked for the page model:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Quando `OnGetAsync` il metodo viene eseguito nel passaggio Act, `GetMessagesAsync` chiama il metodo del modello di pagina.

Passaggio unit test (*test/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage`metodo del `OnGetAsync` modello di pagina (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

Il `GetMessagesAsync` metodo nel DAL non restituisce il risultato per questa chiamata al metodo. La versione fittizia del metodo restituisce il risultato.

Nel `Assert` passaggio, i`actualMessages`messaggi effettivi ( `Messages` ) vengono assegnati dalla proprietà del modello di pagina. Un controllo del tipo viene eseguito anche quando vengono assegnati i messaggi. I messaggi previsti ed `Text` effettivi vengono confrontati in base alle relative proprietà. Il test afferma che `List<Message>` le due istanze contengono gli stessi messaggi.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Altri test in questo gruppo creano <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>oggetti <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>modello <xref:Microsoft.AspNetCore.Mvc.ActionContext> di pagina `PageContext`che `ViewDataDictionary`includono `PageContext`, , per stabilire i , a e a . Questi sono utili nello svolgimento di test. Ad esempio, l'app di `ModelState` messaggi <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> stabilisce un <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> errore con `OnPostAddMessageAsync` per verificare che venga restituito un valore valido quando viene eseguito:For example, the message app establishes a error with to check that a valid is returned when is executed:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Risorse aggiuntive

* [Testing unità di C# in .NET Core usando il test dotnet e xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Eseguire unit test del codice](/visualstudio/test/unit-test-your-code) (Visual Studio)Unit Test Your Code (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Creazione di una soluzione .NET Core completa in macOS con Visual Studio per Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Introduzione a xUnit.net: Utilizzo di .NET Core con la riga di comando di .NET SDK](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Guida introduttiva di Moq](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end
