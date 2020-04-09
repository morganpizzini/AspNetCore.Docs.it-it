---
title: procedure consigliate per le prestazioni di ASP.NET Core
author: mjrousos
description: Suggerimenti per aumentare le prestazioni nelle app ASP.NET Core ed evitare problemi di prestazioni comuni.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
no-loc:
- SignalR
uid: performance/performance-best-practices
ms.openlocfilehash: 068a35fbe410dad24030fe68c0dfd062b402212c
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977184"
---
# <a name="aspnet-core-performance-best-practices"></a>procedure consigliate per le prestazioni di ASP.NET Core

Di [Mike Rousos](https://github.com/mjrousos)

Questo articolo fornisce linee guida per le procedure consigliate per le prestazioni con ASP.NET Core.This article provides guidelines for performance best practices with ASP.NET Core.

## <a name="cache-aggressively"></a>Memorizza nella cache in modo aggressivo

La memorizzazione nella cache è descritta in diverse parti di questo documento. Per altre informazioni, vedere <xref:performance/caching/response>.

## <a name="understand-hot-code-paths"></a>Comprendere i percorsi dei codici attivi

In questo documento, un percorso di *codice attivo* è definito come un percorso di codice che viene chiamato frequentemente e in cui si verifica gran parte del tempo di esecuzione. I percorsi di codice popup in genere limitano la scalabilità orizzontale e le prestazioni dell'app e vengono illustrati in diverse parti di questo documento.

## <a name="avoid-blocking-calls"></a>Evitare di bloccare le chiamate

ASP.NET le app di base devono essere progettate per elaborare più richieste contemporaneamente. Le API asincrone consentono a un piccolo pool di thread di gestire migliaia di richieste simultanee non attendendo chiamate di blocco. Anziché attendere il completamento di un'attività sincrona a esecuzione prolungata, il thread può lavorare su un'altra richiesta.

Un problema di prestazioni comune nelle app ASP.NET Core è il blocco delle chiamate che potrebbero essere asincrone. Molte chiamate di blocco sincrone causano la fame del pool di [thread](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) e i tempi di risposta ridotti.

**Non:**

* Bloccare l'esecuzione asincrona chiamando [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) o [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).
* Acquisire blocchi in percorsi di codice comuni. ASP.NET le app di base sono più performanti quando sono progettate per eseguire il codice in parallelo.
* Chiamare [Task.Run](/dotnet/api/system.threading.tasks.task.run) e attenderlo immediatamente. ASP.NET Core esegue già il codice dell'app nei normali thread del pool di thread, pertanto la chiamata a Task.Run comporta solo una pianificazione del pool di thread aggiuntiva. Anche se il codice pianificato bloccasse un thread, Task.Run non lo impedisce.

**Effettuare l'operazione**:

* Rendere asincroni i percorsi dei [codici attivi.](#understand-hot-code-paths)
* Chiamare l'accesso ai dati, i/o e le operazioni a esecuzione prolungata API in modo asincrono se è disponibile un'API asincrona. **Non** usare [Task.Run](/dotnet/api/system.threading.tasks.task.run) per rendere asincrona un'API synchronus.
* Rendere asincrone le azioni controller/pagina Razor.Make controller/Razor Page actions asynchronous. L'intero stack di chiamate è asincrono per trarre vantaggio dai modelli [async/await.](/dotnet/csharp/programming-guide/concepts/async/)

Un profiler, ad esempio [PerfView](https://github.com/Microsoft/perfview), può essere utilizzato per trovare i thread aggiunti frequentemente al [pool di thread](/windows/desktop/procthread/thread-pools). L'evento `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` indica un thread aggiunto al pool di thread. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="minimize-large-object-allocations"></a>Ridurre al minimo le allocazioni di oggetti di grandi dimensioni

Il Garbage Collector di .NET Core gestisce automaticamente l'allocazione e il rilascio della memoria nelle app ASP.NET Core.The [.NET Core garbage collector](/dotnet/standard/garbage-collection/) manages allocation and release of memory automatically in ASP.NET Core. L'operazione di Garbage Collection automatica in genere significa che gli sviluppatori non devono preoccuparsi di come e quando la memoria viene liberata. Tuttavia, la pulizia degli oggetti senza riferimenti richiede tempo della CPU, pertanto gli sviluppatori devono ridurre al minimo l'allocazione di oggetti nei [percorsi](#understand-hot-code-paths)di codice a caldo . L'operazione di Garbage Collection è particolarmente costosa per gli oggetti di grandi dimensioni (> 85 K byte). Gli oggetti di [grandi](/dotnet/standard/garbage-collection/large-object-heap) dimensioni vengono archiviati nell'heap oggetti grandi e richiedono un'operazione di Garbage Collection completa (generazione 2) per la pulizia. A differenza delle raccolte di generazione 0 e di generazione 1, una raccolta di generazione 2 richiede una sospensione temporanea dell'esecuzione dell'app. L'allocazione frequente e la deallocazione di oggetti di grandi dimensioni possono causare prestazioni incoerenti.

Consigli:

* **Prendere** in considerazione la memorizzazione nella cache di oggetti di grandi dimensioni che vengono utilizzati di frequente. La memorizzazione nella cache di oggetti di grandi dimensioni impedisce allocazioni costose.
* **Eseguire** pool buffer utilizzando [un\<ArrayPool T>](/dotnet/api/system.buffers.arraypool-1) per archiviare matrici di grandi dimensioni.
* **Non** allocare molti oggetti di grandi dimensioni di breve durata nei [percorsi di codice caldo](#understand-hot-code-paths).

Problemi di memoria, ad esempio il precedente, può essere diagnosticato esaminando le statistiche di garbage collection (GC) in PerfView ed esaminando:Memory issues, such as the preceding, can be diagnosed by reviewing garbage collection (GC) stats in [PerfView](https://github.com/Microsoft/perfview) and examining:

* Tempo di pausa Garbage Collection.
* Percentuale del tempo del processore impiegato per l'operazione di Garbage Collection.
* Il numero di operazioni di Garbage Collection di generazione 0, 1 e 2.How many garbage collections are generation 0, 1, and 2.

Per ulteriori informazioni, vedere [Garbage Collection e prestazioni](/dotnet/standard/garbage-collection/performance).

## <a name="optimize-data-access-and-io"></a>Ottimizzare l'accesso ai dati e l'I/OOptimize data access and I/O

Le interazioni con un archivio dati e altri servizi remoti sono spesso le parti più lente di un'app ASP.NET Core.Interactions with a data store and other remote services are often the slowest parts of an ASP.NET Core app. La lettura e la scrittura efficiente dei dati è fondamentale per ottenere buone prestazioni.

Consigli:

* **Chiamare** tutte le API di accesso ai dati in modo asincrono.
* **Non** recuperare più dati del necessario. Scrivere query per restituire solo i dati necessari per la richiesta HTTP corrente.
* **Prendere** in considerazione la memorizzazione nella cache dei dati a cui si accede di frequente recuperati da un database o da un servizio remoto se i dati leggermente obsoleti sono accettabili. A seconda dello scenario, utilizzare un [MemoryCache](xref:performance/caching/memory) o un [DistributedCache](xref:performance/caching/distributed). Per altre informazioni, vedere <xref:performance/caching/response>.
* **Riduci** al minimo i round trip di rete. L'obiettivo è recuperare i dati richiesti in una singola chiamata anziché più chiamate.
* **Utilizzare** [query senza rilevamento](/ef/core/querying/tracking#no-tracking-queries) in Entity Framework Core quando si accede ai dati per scopi di sola lettura. EF Core può restituire i risultati delle query senza rilevamento in modo più efficiente.
* **Filtrare** e aggregare `.Where` `.Select`le `.Sum` query LINQ (ad esempio, con istruzioni , o , ) in modo che il filtro venga eseguito dal database.
* **Si** consideri che EF Core risolve alcuni operatori di query sul client, che può portare a esecuzione di query inefficiente. Per ulteriori informazioni, vedere [Problemi di prestazioni](/ef/core/querying/client-eval#client-evaluation-performance-issues)di valutazione client .
* **Non** utilizzare query di proiezione sulle raccolte, il che può comportare l'esecuzione di query SQL "N e 1". Per ulteriori informazioni, vedere [Ottimizzazione delle sottoquery correlate](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).

Vedere EF High Performance per gli approcci che possono migliorare le prestazioni nelle app su larga scala:See [EF High Performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) for approaches that may improve performance in high-scale apps:

* [Pooling DbContext](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [Query compilate esplicite](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

È consigliabile misurare l'impatto degli approcci ad alte prestazioni precedenti prima di eseguire il commit della base di codice. La complessità aggiuntiva delle query compilate potrebbe non giustificare il miglioramento delle prestazioni.

I problemi relativi alle query possono essere rilevati esaminando il tempo impiegato per accedere ai dati con [Application Insights](/azure/application-insights/app-insights-overview) o con gli strumenti di profilatura. La maggior parte dei database rende inoltre disponibili statistiche relative alle query eseguite di frequente.

## <a name="pool-http-connections-with-httpclientfactory"></a>Pool di connessioni HTTP con HttpClientFactory

Sebbene [HttpClient](/dotnet/api/system.net.http.httpclient) implementi l'interfaccia, `IDisposable` è progettata per il riutilizzo. Le `HttpClient` istanze chiuse lasciano `TIME_WAIT` le prese aperte nello stato per un breve periodo di tempo. Se viene usato frequentemente un `HttpClient` percorso di codice che crea ed elimina gli oggetti, l'app potrebbe esaurire i socket disponibili. [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) è stato introdotto in ASP.NET Core 2.1 come soluzione a questo problema. Gestisce il pooling delle connessioni HTTP per ottimizzare le prestazioni e l'affidabilità.

Consigli:

* **Non** creare ed `HttpClient` eliminare direttamente le istanze.
* **Utilizzare** [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) per `HttpClient` recuperare le istanze. Per ulteriori informazioni, vedere [Usare HttpClientFactory per implementare richieste HTTP resilienti.](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)

## <a name="keep-common-code-paths-fast"></a>Mantenere veloci i percorsi di codice comuni

Si desidera che tutto il codice sia veloce. I percorsi di codice più chiamati di frequente sono i più critici da ottimizzare. incluse le seguenti:

* I componenti middleware nella pipeline di elaborazione delle richieste dell'app, in particolare il middleware vengono eseguiti nelle prime fasi della pipeline. Questi componenti hanno un grande impatto sulle prestazioni.
* Codice eseguito per ogni richiesta o più volte per richiesta. Ad esempio, la registrazione personalizzata, i gestori di autorizzazioni o l'inizializzazione di servizi temporanei.

Consigli:

* **Non** utilizzare componenti middleware personalizzati con attività a esecuzione prolungata.
* **Utilizzare** gli strumenti di profilatura delle prestazioni, ad esempio [Visual Studio Diagnostic Tools](/visualstudio/profiling/profiling-feature-tour) o [PerfView](https://github.com/Microsoft/perfview), per identificare i percorsi del [codice di interfaccia utente.](#understand-hot-code-paths)

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>Completare le attività a esecuzione prolungata all'esterno delle richieste HTTPComplete long-running Tasks outside of HTTP requests

La maggior parte delle richieste a un'app ASP.NET Core può essere gestita da un controller o da un modello di pagina che chiama i servizi necessari e restituisce una risposta HTTP. Per alcune richieste che coinvolgono attività a esecuzione prolungata, è preferibile rendere asincrono l'intero processo di richiesta-risposta.

Consigli:

* **Non** attendere il completamento delle attività a esecuzione prolungata come parte della normale elaborazione delle richieste HTTP.
* **Valutare** la possibilità di gestire le richieste a esecuzione prolungata con [servizi in background](xref:fundamentals/host/hosted-services) o fuori processo con una funzione di [Azure.](/azure/azure-functions/) Il completamento del lavoro out-of-process è particolarmente utile per le attività che richiedono un uso intensivo della CPU.
* **Utilizzare** le opzioni di comunicazione [SignalR](xref:signalr/introduction)in tempo reale, ad esempio , per comunicare con i client in modo asincrono.

## <a name="minify-client-assets"></a>Riduci le risorse dei clienti

ASP.NET le app di base con front-end complessi spesso servono molti file JavaScript, CSS o di immagine. Le prestazioni delle richieste di carico iniziali possono essere migliorate:

* Bundling, che combina più file in uno.
* Riduzione al più possibile, che riduce le dimensioni dei file rimuovendo spazi vuoti e commenti.

Consigli:

* **Utilizzare** ASP.NET supporto [incorporato](xref:client-side/bundling-and-minification) di Core per l'aggregazione e la minimizzazione delle risorse client.
* **Prendere** in considerazione altri strumenti di terze parti, ad esempio [Webpack](https://webpack.js.org/), per la gestione complessa delle risorse client.

## <a name="compress-responses"></a>Comprimere le risposte

 La riduzione delle dimensioni della risposta di solito aumenta la velocità di risposta di un'app, spesso in modo drammatico. Un modo per ridurre le dimensioni del payload consiste nel comprimere le risposte di un'app. Per ulteriori informazioni, consultate [Compressione delle risposte.](xref:performance/response-compression)

## <a name="use-the-latest-aspnet-core-release"></a>Usare la versione più recente di ASP.NET CoreUse the latest ASP.NET Core release

Ogni nuova versione di ASP.NET Core include miglioramenti delle prestazioni. Le ottimizzazioni in .NET Core e ASP.NET Core indicano che le versioni più recenti in genere superano le versioni precedenti. Ad esempio, .NET Core 2.1 ha aggiunto il supporto per le espressioni regolari compilate e ha beneficiato di [Span\<T>](https://msdn.microsoft.com/magazine/mt814808.aspx). ASP.NET Core 2.2 added support for HTTP/2. [ASP.NET Core 3.0 aggiunge molti miglioramenti](xref:aspnetcore-3.0) che riducono l'utilizzo della memoria e migliorano la velocità effettiva. Se le prestazioni sono una priorità, prendere in considerazione l'aggiornamento alla versione corrente di ASP.NET Core.If performance is a priority, consider upgrading to the current version of ASP.NET Core.

## <a name="minimize-exceptions"></a>Ridurre al minimo le eccezioni

Le eccezioni dovrebbero essere rare. La generazione e l'intercettazione di eccezioni è lenta rispetto ad altri modelli di flusso di codice. Per questo motivo, le eccezioni non devono essere utilizzate per controllare il normale flusso del programma.

Consigli:

* **Non** utilizzare la generazione o l'intercettazione di eccezioni come mezzo del normale flusso di programma, in particolare nei [percorsi](#understand-hot-code-paths)di codice a caldo .
* **Includere** la logica nell'app per rilevare e gestire le condizioni che potrebbero causare un'eccezione.
* **Generare** o intercettare eccezioni per condizioni insolite o impreviste.

Gli strumenti di diagnostica delle app, ad esempio Application Insights, consentono di identificare le eccezioni comuni in un'app che possono influire sulle prestazioni.

## <a name="performance-and-reliability"></a>Prestazioni e affidabilità

Nelle sezioni seguenti vengono forniti suggerimenti sulle prestazioni e problemi e soluzioni noti relativi all'affidabilità.

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>Evitare la lettura o la scrittura sincrona nel corpo HttpRequest/HttpResponseAvoid synchronous read or write on HttpRequest/HttpResponse body

Tutti i / o in ASP.NET Core è asincrono. I server `Stream` implementano l'interfaccia, che dispone di overload sia sincroni che asincroni. Quelli asincroni dovrebbero essere preferiti per evitare di bloccare i thread del pool di thread. Il blocco dei thread può causare la fame del pool di thread.

**Non eseguire questa operazione:** Nell'esempio riportato di seguito viene utilizzato l'oggetto <xref:System.IO.StreamReader.ReadToEnd*>. Blocca il thread corrente per attendere il risultato. Questo è un esempio di [sincronizzazione su async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
).

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

Nel codice precedente, `Get` legge in modo sincrono l'intero corpo della richiesta HTTP in memoria. Se il client viene caricato lentamente, l'app esegue la sincronizzazione tramite async. L'app esegue la sincronizzazione tramite async perché Kestrel **NON** supporta letture sincrone.

**Procedere come segue:** Nell'esempio <xref:System.IO.StreamReader.ReadToEndAsync*> seguente viene utilizzato e non viene bloccato il thread durante la lettura.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

Il codice precedente legge in modo asincrono l'intero corpo della richiesta HTTP in memoria.

> [!WARNING]
> Se la richiesta è di grandi dimensioni, la lettura dell'intero corpo della richiesta HTTP in memoria potrebbe causare una condizione di memoria insufficiente.If the request is large, reading the entire HTTP request body in memory could lead to an out memory (OOM) condition. OOM può comportare una negazione del servizio.  Per altre informazioni, vedere Evitare di [leggere corpi di richiesta di grandi dimensioni o corpi di risposta in memoria](#arlb) in questo documento.

**Procedere come segue:** L'esempio seguente è completamente asincrono usando un corpo della richiesta non memorizzato nel buffer:The following example is fully asynchronous using a non buffered request body:

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

Il codice precedente deserializza in modo asincrono il corpo della richiesta in un oggetto c'è.

## <a name="prefer-readformasync-over-requestform"></a>Preferisci ReadFormAsync rispetto a Request.Form

Usare `HttpContext.Request.ReadFormAsync` anziché `HttpContext.Request.Form`.
`HttpContext.Request.Form`può essere letto in modo sicuro solo con le seguenti condizioni:

* Il modulo è stato letto `ReadFormAsync`da una chiamata a ,
* Il valore del modulo memorizzato nella cache viene letto utilizzando`HttpContext.Request.Form`

**Non eseguire questa operazione:** Nell'esempio `HttpContext.Request.Form`riportato di seguito viene utilizzato .  `HttpContext.Request.Form`utilizza [la sincronizzazione su async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) e può portare alla fame del pool di thread.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

**Procedere come segue:** Nell'esempio `HttpContext.Request.ReadFormAsync` riportato di seguito viene utilizzato per leggere il corpo del form in modo asincrono.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>Evitare di leggere corpi di richiesta di grandi dimensioni o corpi di risposta in memoria

In .NET ogni allocazione di oggetti superiore a 85 KB finisce nell'heap oggetti grandi ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)). Gli oggetti di grandi dimensioni sono costosi in due modi:

* Il costo di allocazione è elevato perché la memoria per un oggetto di grandi dimensioni appena allocato deve essere cancellata. CLR garantisce che la memoria per tutti gli oggetti appena allocati venga cancellata.
* LOH viene raccolto con il resto del mucchio. LOH richiede [un'operazione](/dotnet/standard/garbage-collection/fundamentals) di Garbage Collection completa o [Un'insieme Gen2](/dotnet/standard/garbage-collection/fundamentals#generations).

Questo post di [blog](https://adamsitnik.com/Array-Pool/#the-problem) descrive il problema in modo conciso:

> Quando un oggetto di grandi dimensioni viene allocato, viene contrassegnato come oggetto Gen 2.When a large object is allocated, it's marked as Gen 2 object. Non Gen 0 come per i piccoli oggetti. Le conseguenze sono che se si esaurisce la memoria in LOH, GC pulisce l'intero heap gestito, non solo LOH. Quindi pulisce Gen 0, Gen 1 e Gen 2, incluso LOH. Questa operazione è denominata Garbage Collection completa ed è l'operazione di Garbage Collection che richiede più tempo. Per molte applicazioni, può essere accettabile. Ma sicuramente non per i server web ad alte prestazioni, dove sono necessari pochi buffer di memoria di grandi dimensioni per gestire una richiesta web media (lettura da un socket, decomprimere, decodificare JSON & altro ancora).

Memorizzazione ingenua di un corpo `byte[]` di `string`richiesta o risposta di grandi dimensioni in un unico:

* Può causare rapidamente lo spazio in esaurimento dello spazio nella LOH.
* Può causare problemi di prestazioni per l'app a causa dell'esecuzione completa dei cg.

## <a name="working-with-a-synchronous-data-processing-api"></a>Utilizzo di un'API di elaborazione dati sincronaWorking with a synchronous data processing API

Quando si usa un serializzatore/deserializzatore che supporta solo letture e scritture sincrone (ad esempio, [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):

* Memorizzare i dati nel buffer in memoria in modo asincrono prima di passarne nel serializzatore/deserializzatore.

> [!WARNING]
> Se la richiesta è di grandi dimensioni, potrebbe causare una condizione di memoria insufficiente (OOM). OOM può comportare una negazione del servizio.  Per altre informazioni, vedere Evitare di [leggere corpi di richiesta di grandi dimensioni o corpi di risposta in memoria](#arlb) in questo documento.

ASP.NET Core 3.0 usa <xref:System.Text.Json> per impostazione predefinita per la serializzazione JSON. <xref:System.Text.Json>:

* Legge e scrive JSON in modo asincrono.
* È ottimizzato per il testo UTF-8.
* Prestazioni in `Newtonsoft.Json`genere superiori a quelle di .

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>Non archiviare IHttpContextAccessor.HttpContext in un campoDo not store IHttpContextAccessor.HttpContext in a field

[Il IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) `HttpContext` restituisce l'oggetto della richiesta attiva quando si accede dal thread di richiesta. L'oggetto `IHttpContextAccessor.HttpContext` **non** deve essere archiviato in un campo o in una variabile.

**Non eseguire questa operazione:** Nell'esempio seguente `HttpContext` l'oggetto viene archiviato in un campo e quindi si tenta di utilizzarlo in un secondo momento.

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

Il codice precedente acquisisce spesso `HttpContext` un valore null o errato nel costruttore.

**Procedere come segue:** L'esempio seguente:

* Memorizza <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> l'oggetto in un campo.
* Utilizza `HttpContext` il campo all'ora `null`corretta e verifica la presenza di .

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>Non accedere a HttpContext da più threadDo not access HttpContext from multiple threads

`HttpContext`*NON* è thread-safe. L'accesso `HttpContext` da più thread in parallelo può causare un comportamento indefinito, ad esempio blocchi, arresti anomali e danneggiamento dei dati.

**Non eseguire questa operazione:** Nell'esempio seguente vengono effettuate tre richieste parallele e viene eseguito il log del percorso della richiesta in ingresso prima e dopo la richiesta HTTP in uscita. Il percorso della richiesta è accessibile da più thread, potenzialmente in parallelo.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

**Procedere come segue:** Nell'esempio seguente vengono copiati tutti i dati dalla richiesta in ingresso prima di effettuare le tre richieste parallele.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>Non utilizzare HttpContext al termine della richiesta

`HttpContext`è valido solo se è presente una richiesta HTTP attiva nella pipeline di ASP.NET Core.Is only valid as there is an active HTTP request in the ASP.NET Core pipeline. L'intera pipeline ASP.NET Core è una catena asincrona di delegati che esegue ogni richiesta. Quando `Task` il ritorno da questa catena `HttpContext` viene completato, il viene riciclato.

**Non eseguire questa operazione:** Nell'esempio `async void` seguente viene utilizzato il quale `await` la richiesta HTTP viene completata quando viene raggiunta la prima:

* Che è **SEMPRE** una cattiva pratica nelle app ASP.NET Core.
* Accede a `HttpResponse` dopo il completamento della richiesta HTTP.
* Il processo si blocca.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

**Procedere come segue:** Nell'esempio seguente `Task` viene restituito un al framework, in modo che la richiesta HTTP non venga completata fino al completamento dell'azione.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>Non acquisire HttpContext in thread in background

**Non eseguire questa operazione:** Nell'esempio `Controller` seguente viene illustrato `HttpContext` una chiusura sta catturando il dalla proprietà . Si tratta di una procedura non valida perché l'elemento di lavoro potrebbe:This is a bad practice because the work item could:

* Eseguire all'esterno dell'ambito della richiesta.
* Tentare di `HttpContext`leggere il file .

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

**Procedere come segue:** L'esempio seguente:

* Copia i dati necessari nell'attività in background durante la richiesta.
* Non fa riferimento a nulla dal controller.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

Le attività in background devono essere implementate come servizi ospitati. Per altre informazioni, vedere [Attività in background con servizi ospitati](xref:fundamentals/host/hosted-services).

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>Non acquisire servizi inseriti nei controller in thread in background

**Non eseguire questa operazione:** L'esempio seguente mostra che `DbContext` una `Controller` chiusura acquisisce l'oggetto dal parametro action. Questa è una cattiva pratica.  L'elemento di lavoro può essere eseguito all'esterno dell'ambito della richiesta. L'ambito `ContosoDbContext` dell'oggetto è la `ObjectDisposedException`richiesta, generando un oggetto .

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

**Procedere come segue:** L'esempio seguente:

* Inserisce <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> un in ordine per creare un ambito nell'elemento di lavoro in background. `IServiceScopeFactory`è un singleton.
* Crea un nuovo ambito di inserimento delle dipendenze nel thread in background.
* Non fa riferimento a nulla dal controller.
* Non acquisisce `ContosoDbContext` il dalla richiesta in ingresso.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

Il seguente codice evidenziato:

* Crea un ambito per la durata dell'operazione in background e risolve i servizi da essa.
* Utilizza `ContosoDbContext` dall'ambito corretto.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a>Non modificare il codice di stato o le intestazioni dopo l'avvio del corpo della risposta

ASP.NET Core non memorizza nel buffer il corpo della risposta HTTP. La prima volta che viene scritta la risposta:

* Le intestazioni vengono inviate insieme a tale blocco di corpo al client.
* Non è più possibile modificare le intestazioni di risposta.

**Non eseguire questa operazione:** Il codice seguente tenta di aggiungere intestazioni di risposta dopo che la risposta è già stata avviata:The following code tries to add response headers after the response has already started:

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

Nel codice precedente, `context.Response.Headers["test"] = "test value";` genererà un'eccezione se `next()` ha scritto nella risposta.

**Procedere come segue:** Nell'esempio seguente viene verificato se la risposta HTTP è stata avviata prima di modificare le intestazioni.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

**Procedere come segue:** Nell'esempio `HttpResponse.OnStarting` seguente viene utilizzato per impostare le intestazioni prima che le intestazioni di risposta vengano scaricate nel client.

Il controllo se la risposta non è stata avviata consente la registrazione di un callback che verrà richiamato subito prima della scrittura delle intestazioni di risposta. Verifica se la risposta non è iniziata:

* Consente di aggiungere o sostituire le intestazioni just in time.
* Non richiede la conoscenza del middleware successivo nella pipeline.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>Non chiamare next() se hai già iniziato a scrivere nel corpo della risposta

I componenti prevedono di essere chiamati solo se è possibile per loro gestire e modificare la risposta.
