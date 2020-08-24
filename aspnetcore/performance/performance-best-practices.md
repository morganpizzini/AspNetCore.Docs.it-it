---
title: Procedure consigliate per le prestazioni ASP.NET Core
author: mjrousos
description: Suggerimenti per migliorare le prestazioni in ASP.NET Core app ed evitare problemi comuni relativi alle prestazioni.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/performance-best-practices
ms.openlocfilehash: 587872b269d897d7c86eb77c110a4b6432218ed3
ms.sourcegitcommit: dd0e87abf2bb50ee992d9185bb256ed79d48f545
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88746559"
---
# <a name="aspnet-core-performance-best-practices"></a>Procedure consigliate per le prestazioni ASP.NET Core

Di [Mike risveglia](https://github.com/mjrousos)

Questo articolo fornisce linee guida per le procedure consigliate per le prestazioni con ASP.NET Core.

## <a name="cache-aggressively"></a>Cache in modo aggressivo

La memorizzazione nella cache viene discussa in diverse parti di questo documento. Per altre informazioni, vedere <xref:performance/caching/response>.

## <a name="understand-hot-code-paths"></a>Informazioni sui percorsi del codice attivo

In questo documento un *percorso di codice critico* viene definito come un percorso di codice chiamato di frequente e in cui si verifica la maggior parte del tempo di esecuzione. I percorsi di codice a caldo limitano in genere la scalabilità orizzontale e le prestazioni dell'app e vengono discussi in diverse parti di questo documento.

## <a name="avoid-blocking-calls"></a>Evitare le chiamate di blocco

ASP.NET Core le app devono essere progettate per elaborare molte richieste contemporaneamente. Le API asincrone consentono a un pool di thread di dimensioni ridotte di gestire migliaia di richieste simultanee non attendendo chiamate di blocco. Invece di attendere il completamento di un'attività sincrona a esecuzione prolungata, il thread può funzionare su un'altra richiesta.

Un problema di prestazioni comune nelle app ASP.NET Core consiste nel bloccare le chiamate che potrebbero essere asincrone. Molte chiamate di blocco sincrono causano l'esaurimento del [pool di thread](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) e i tempi di risposta ridotti.

**Non:**

* Blocca l'esecuzione asincrona chiamando [Task. Wait](/dotnet/api/system.threading.tasks.task.wait) o [Task. Result](/dotnet/api/system.threading.tasks.task-1.result).
* Acquisisci blocchi nei percorsi di codice comuni. Le app ASP.NET Core sono più efficienti quando vengono progettate per l'esecuzione di codice in parallelo.
* Chiamare [Task. Run](/dotnet/api/system.threading.tasks.task.run) e attenderlo immediatamente. ASP.NET Core esegue già il codice dell'app nei thread del pool di thread normali, quindi la chiamata di Task. Run comporta solo la pianificazione di pool di thread superflui. Anche se il codice pianificato blocca un thread, Task. Run non lo impedisce.

**Do**:

* Rendere asincroni i [percorsi di codice caldo](#understand-hot-code-paths) .
* Chiamare le API di accesso ai dati, I/O e operazioni con esecuzione prolungata in modo asincrono se è disponibile un'API asincrona. Non **usare** [Task. Run](/dotnet/api/system.threading.tasks.task.run) per rendere asincrona un'API sincrona.
* Rendere asincrone le azioni del controller o della Razor pagina. L'intero stack di chiamate è asincrono per trarre vantaggio dai modelli [Async/Await](/dotnet/csharp/programming-guide/concepts/async/) .

Un profiler, ad esempio [PerfView](https://github.com/Microsoft/perfview), può essere usato per trovare i thread aggiunti di frequente al [pool di thread](/windows/desktop/procthread/thread-pools). L' `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` evento indica che un thread è stato aggiunto al pool di thread. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="return-ienumerablet-or-iasyncenumerablet"></a>Return IEnumerable \<T> o IAsyncEnumerable\<T>

La restituzione `IEnumerable<T>` da un'azione comporta l'iterazione di una raccolta sincrona da parte del serializzatore. Il risultato è il blocco delle chiamate e un potenziale per l'inedia del pool di thread. Per evitare l'enumerazione sincrona, usare `ToListAsync` prima di restituire l'enumerabile.

A partire da ASP.NET Core 3,0, `IAsyncEnumerable<T>` può essere usato come alternativa a `IEnumerable<T>` che enumera in modo asincrono. Per altre informazioni, vedere [tipi restituiti azione del controller](xref:web-api/action-return-types#return-ienumerablet-or-iasyncenumerablet).

## <a name="minimize-large-object-allocations"></a>Ridurre al minimo le allocazioni di oggetti di grandi dimensioni

[.NET Core Garbage Collector](/dotnet/standard/garbage-collection/) gestisce automaticamente l'allocazione e il rilascio di memoria nelle app ASP.NET Core. Il Garbage Collection automatico in genere significa che gli sviluppatori non devono preoccuparsi di come o quando la memoria viene liberata. Tuttavia, la pulizia di oggetti senza riferimenti richiede tempo CPU, quindi gli sviluppatori dovrebbero ridurre al minimo l'allocazione di oggetti nei [percorsi di codice a caldo](#understand-hot-code-paths). Il processo di Garbage Collection è particolarmente costoso in oggetti di grandi dimensioni (> 85 K byte). Gli oggetti di grandi dimensioni vengono archiviati nell' [heap degli oggetti grandi](/dotnet/standard/garbage-collection/large-object-heap) e richiedono una Garbage Collection completa (seconda generazione) per eseguire la pulizia. Diversamente dalle raccolte di generazione 0 e generazione 1, una raccolta di generazione 2 richiede una sospensione temporanea dell'esecuzione dell'app. L'allocazione e la deallocazione frequenti di oggetti di grandi dimensioni possono causare prestazioni incoerenti.

Consigli:

* **Prendere in** considerazione la memorizzazione nella cache di oggetti di grandi dimensioni usati di frequente. La memorizzazione nella cache di oggetti di grandi dimensioni impedisce allocazioni costose.
* **Eseguire** il pool di buffer usando un [ArrayPool \<T> ](/dotnet/api/system.buffers.arraypool-1) per archiviare matrici di grandi dimensioni.
* **Non** allocare molti oggetti di grandi dimensioni di breve durata nei [percorsi del codice a caldo](#understand-hot-code-paths).

È possibile diagnosticare problemi di memoria, ad esempio quelli precedenti, esaminando le statistiche Garbage Collection (GC) in [PerfView](https://github.com/Microsoft/perfview) ed esaminando:

* Tempo di sospensione della Garbage Collection.
* Percentuale del tempo del processore impiegato per Garbage Collection.
* Il numero di Garbage Collection di generazione 0, 1 e 2.

Per altre informazioni, vedere [Garbage Collection e performance](/dotnet/standard/garbage-collection/performance).

## <a name="optimize-data-access-and-io"></a>Ottimizzare l'accesso ai dati e l'I/O

Le interazioni con un archivio dati e altri servizi remoti sono spesso le parti più lente di un'app ASP.NET Core. La lettura e la scrittura dei dati in modo efficiente sono essenziali per garantire prestazioni ottimali.

Consigli:

* **Chiamare tutte** le API di accesso ai dati in modo asincrono.
* **Non** recuperare più dati del necessario. Scrivere query per restituire solo i dati necessari per la richiesta HTTP corrente.
* **Si consiglia di** memorizzare nella cache i dati a cui si accede di frequente recuperati da un database o da un servizio remoto se i dati leggermente non aggiornati sono accettabili. A seconda dello scenario, utilizzare un oggetto [MemoryCache](xref:performance/caching/memory) o un [DistributedCache](xref:performance/caching/distributed). Per altre informazioni, vedere <xref:performance/caching/response>.
* **Ridurre al** minimo i round trip di rete. L'obiettivo è recuperare i dati necessari in una singola chiamata invece che in diverse chiamate.
* **Utilizzare** [query senza rilevamento](/ef/core/querying/tracking#no-tracking-queries) in Entity Framework Core durante l'accesso ai dati per scopi di sola lettura. EF Core possibile restituire in modo più efficiente i risultati delle query senza rilevamento.
* **Filtrare e** aggregare query LINQ `.Where` `.Select` , ad esempio con istruzioni, o, in `.Sum` modo che il filtro venga eseguito dal database.
* **Tenere presente** che EF Core risolve alcuni operatori di query nel client, il che potrebbe causare un'esecuzione inefficiente delle query. Per ulteriori informazioni, vedere la pagina relativa ai [problemi di prestazioni della valutazione dei client](/ef/core/querying/client-eval#client-evaluation-performance-issues).
* **Non** usare query di proiezione sulle raccolte, operazione che può causare l'esecuzione di query SQL "N + 1". Per ulteriori informazioni, vedere [ottimizzazione delle sottoquery correlate](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).

Vedere [EF High Performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) per approcci che possono migliorare le prestazioni nelle app a scalabilità elevata:

* [Pooling DbContext](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [Query compilate esplicite](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

Si consiglia di misurare l'effetto degli approcci ad alte prestazioni precedenti prima di eseguire il commit della codebase. La complessità aggiuntiva delle query compilate potrebbe non giustificare il miglioramento delle prestazioni.

È possibile rilevare i problemi di query esaminando il tempo impiegato per accedere ai dati con [Application Insights](/azure/application-insights/app-insights-overview) o con gli strumenti di profilatura. La maggior parte dei database rende disponibili anche le statistiche relative alle query eseguite di frequente.

## <a name="pool-http-connections-with-httpclientfactory"></a>Pool di connessioni HTTP con HttpClientFactory

Sebbene [HttpClient](/dotnet/api/system.net.http.httpclient) implementi l' `IDisposable` interfaccia, è progettato per il riutilizzo. `HttpClient`Le istanze chiuse lasciano i socket aperti nello `TIME_WAIT` stato per un breve periodo di tempo. Se viene usato di frequente un percorso di codice che crea ed Elimina `HttpClient` oggetti, l'app può esaurire i socket disponibili. [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) è stato introdotto in ASP.NET Core 2,1 come soluzione per questo problema. Gestisce il pool di connessioni HTTP per ottimizzare le prestazioni e l'affidabilità.

Consigli:

* **Non** creare ed eliminare direttamente le `HttpClient` istanze.
* **Usare** [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) per recuperare le `HttpClient` istanze. Per altre informazioni, vedere [usare HttpClientFactory per implementare richieste http resilienti](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

## <a name="keep-common-code-paths-fast"></a>Mantieni rapidamente i percorsi di codice comuni

Si vuole che tutto il codice sia veloce. I percorsi di codice comunemente chiamati sono i più importanti da ottimizzare. Sono inclusi:

* Componenti middleware nella pipeline di elaborazione delle richieste dell'app, in particolare il middleware viene eseguito all'inizio della pipeline. Questi componenti hanno un notevole effetto sulle prestazioni.
* Codice eseguito per ogni richiesta o più volte per richiesta. Ad esempio, la registrazione personalizzata, i gestori di autorizzazione o l'inizializzazione di servizi temporanei.

Consigli:

* **Non** usare componenti middleware personalizzati con attività a esecuzione prolungata.
* **Usare gli** strumenti di profilatura delle prestazioni, ad esempio [Visual Studio strumenti di diagnostica](/visualstudio/profiling/profiling-feature-tour) o [PerfView](https://github.com/Microsoft/perfview)), per identificare i [percorsi del codice attivo](#understand-hot-code-paths).

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>Completa attività a esecuzione prolungata all'esterno delle richieste HTTP

La maggior parte delle richieste a un'app ASP.NET Core può essere gestita da un controller o da un modello di pagina che chiama i servizi necessari e restituisce una risposta HTTP. Per alcune richieste che coinvolgono attività a esecuzione prolungata, è preferibile rendere asincrono l'intero processo di richiesta-risposta.

Consigli:

* **Non attendere il** completamento delle attività con esecuzione prolungata come parte dell'elaborazione di una richiesta HTTP ordinata.
* **Si** consiglia di gestire le richieste con esecuzione prolungata con [Servizi in background](xref:fundamentals/host/hosted-services) o out-of-process con una [funzione di Azure](/azure/azure-functions/). Il completamento del lavoro out-of-process è particolarmente vantaggioso per le attività con utilizzo intensivo della CPU.
* **Usare** le opzioni di comunicazione in tempo reale, ad esempio [SignalR](xref:signalr/introduction) , per comunicare con i client in modo asincrono.

## <a name="minify-client-assets"></a>Asset client di minimizzare

ASP.NET Core le app con front-end complessi servono spesso molti file JavaScript, CSS o image. Le prestazioni delle richieste di caricamento iniziale possono essere migliorate:

* Creazione di bundle, che combina più file in uno.
* Minimizzazione, che riduce le dimensioni dei file rimuovendo gli spazi vuoti e i commenti.

Consigli:

* **Usare il** [supporto predefinito](xref:client-side/bundling-and-minification) di ASP.NET Core per la creazione di bundle e la minimizzazione delle risorse client.
* **Prendere in** considerazione altri strumenti di terze parti, ad esempio [Webpack](https://webpack.js.org/), per la gestione delle risorse client complesse.

## <a name="compress-responses"></a>Comprimi risposte

 La riduzione delle dimensioni della risposta in genere aumenta la velocità di risposta di un'app, spesso in modo significativo. Un modo per ridurre le dimensioni del payload consiste nel comprimere le risposte di un'app. Per altre informazioni, vedere [compressione delle risposte](xref:performance/response-compression).

## <a name="use-the-latest-aspnet-core-release"></a>Usa la versione ASP.NET Core più recente

Ogni nuova versione di ASP.NET Core include miglioramenti delle prestazioni. Le ottimizzazioni in .NET Core e ASP.NET Core indicano che le versioni più recenti superano in genere le versioni precedenti. Ad esempio, .NET Core 2,1 ha aggiunto il supporto per le espressioni regolari compilate ed è stato avvantaggiato da [span \<T> ](/archive/msdn-magazine/2018/january/csharp-all-about-span-exploring-a-new-net-mainstay). ASP.NET Core 2,2 è stato aggiunto il supporto per HTTP/2. [ASP.NET Core 3,0 aggiunge molti miglioramenti](xref:aspnetcore-3.0) che consentono di ridurre l'utilizzo della memoria e migliorare la velocità effettiva. Se le prestazioni sono una priorità, provare a eseguire l'aggiornamento alla versione corrente di ASP.NET Core.

## <a name="minimize-exceptions"></a>Riduci le eccezioni

Le eccezioni devono essere rare. Le eccezioni di generazione e rilevamento sono lente rispetto ad altri modelli di flusso del codice. Per questo motivo, le eccezioni non devono essere usate per controllare il flusso di programma normale.

Consigli:

* **Non** usare la generazione o l'intercettazione di eccezioni come mezzo del normale flusso di programma, soprattutto nei [percorsi di codice caldo](#understand-hot-code-paths).
* **Includere la** logica nell'app per rilevare e gestire le condizioni che causerebbero un'eccezione.
* **Generare o** rilevare eccezioni per condizioni insolite o impreviste.

Gli strumenti di diagnostica delle app, ad esempio Application Insights, consentono di identificare le eccezioni comuni in un'app che possono influire sulle prestazioni.

## <a name="performance-and-reliability"></a>Prestazioni e affidabilità

Le sezioni seguenti forniscono suggerimenti sulle prestazioni e problemi di affidabilità noti e soluzioni.

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>Evitare la lettura o la scrittura sincrona sul corpo HttpRequest/HttpResponse

Tutti I/O in ASP.NET Core è asincrono. I server implementano l' `Stream` interfaccia, che include overload sincroni e asincroni. Per evitare il blocco dei thread del pool di thread, è preferibile eseguire un'operazione asincrona. I thread di blocco possono causare l'esaurimento del pool di thread.

Non **eseguire questa operazione:** Nell'esempio seguente viene usato l'oggetto <xref:System.IO.StreamReader.ReadToEnd*> . Blocca il thread corrente in modo che attenda il risultato. Questo è un esempio di [sincronizzazione su Async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
).

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

Nel codice precedente, legge in modo `Get` sincrono l'intero corpo della richiesta HTTP in memoria. Se il client sta caricando lentamente, l'app sta eseguendo la sincronizzazione su Async. L'app esegue la sincronizzazione su Async **perché il gheppio non supporta le** letture sincrone.

**Eseguire questa operazione:** Nell'esempio seguente viene usato <xref:System.IO.StreamReader.ReadToEndAsync*> e non blocca il thread durante la lettura.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

Il codice precedente legge in modo asincrono l'intero corpo della richiesta HTTP in memoria.

> [!WARNING]
> Se la richiesta è di grandi dimensioni, la lettura dell'intero corpo della richiesta HTTP in memoria potrebbe causare una condizione di memoria insufficiente. La memoria insufficiente può causare un attacco Denial of Service.  Per altre informazioni, vedere evitare di leggere i corpi delle [richieste di grandi dimensioni o i corpi di risposta in memoria](#arlb) in questo documento.

**Eseguire questa operazione:** L'esempio seguente è completamente asincrono utilizzando un corpo della richiesta non memorizzato nel buffer:

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

Il codice precedente deserializza in modo asincrono il corpo della richiesta in un oggetto C#.

## <a name="prefer-readformasync-over-requestform"></a>Preferisci ReadFormAsync su request. Form

Usare `HttpContext.Request.ReadFormAsync` invece di `HttpContext.Request.Form`.
`HttpContext.Request.Form` è possibile leggere in modo sicuro solo con le condizioni seguenti:

* Il modulo è stato letto da una chiamata a `ReadFormAsync` e
* Il valore del modulo memorizzato nella cache viene letto usando `HttpContext.Request.Form`

Non **eseguire questa operazione:** Nell'esempio seguente viene utilizzato `HttpContext.Request.Form` .  `HttpContext.Request.Form` Usa la [sincronizzazione su Async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) e può causare l'esaurimento del pool di thread.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

**Eseguire questa operazione:** Nell'esempio seguente viene utilizzato `HttpContext.Request.ReadFormAsync` per leggere il corpo del form in modo asincrono.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>Evitare di leggere corpi di richieste o corpi di risposta di grandi dimensioni in memoria

In .NET ogni allocazione di oggetti superiore a 85 KB si conclude nell'heap degli oggetti grandi ([Loh](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)). Gli oggetti di grandi dimensioni sono costosi in due modi:

* Il costo di allocazione è elevato perché la memoria per un oggetto di grandi dimensioni appena allocato deve essere cancellata. CLR garantisce che la memoria per tutti gli oggetti appena allocati venga cancellata.
* L'heap oggetti grandi viene raccolto con il resto dell'heap. LOH richiede una raccolta completa di [Garbage Collection](/dotnet/standard/garbage-collection/fundamentals) o [Gen2](/dotnet/standard/garbage-collection/fundamentals#generations).

Questo [post di Blog](https://adamsitnik.com/Array-Pool/#the-problem) descrive brevemente il problema:

> Quando un oggetto di grandi dimensioni viene allocato, viene contrassegnato come oggetto di generazione 2. Non è di generazione 0 come per gli oggetti piccoli. Le conseguenze sono che se si esaurisce la memoria nell'heap oggetti grandi, GC pulisce l'intero heap gestito, non solo LOH. Quindi, esegue la pulizia di generazione 0, generazione 1 e generazione 2, incluso LOH. Questo metodo è denominato Garbage Collection completo ed è il Garbage Collection più dispendioso in termini di tempo. Per molte applicazioni, può essere accettabile. Ma sicuramente non per i server Web a prestazioni elevate, in cui sono necessari pochi buffer di memoria per gestire una richiesta Web Media (lettura da un socket, decomprimere, decodificare JSON & altro).

Archiviazione ingenua di un corpo di richiesta o di risposta di grandi dimensioni in un singolo `byte[]` o `string` :

* Può comportare un rapido esaurimento dello spazio nell'heap oggetti grandi.
* Potrebbe causare problemi di prestazioni per l'app a causa dell'esecuzione di cataloghi globali completi.

## <a name="working-with-a-synchronous-data-processing-api"></a>Uso di un'API di elaborazione dati sincrona

Quando si usa un serializzatore/deserializzatore che supporta solo letture e scritture sincrone (ad esempio,  [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):

* Memorizza nel buffer i dati in memoria in modo asincrono prima di passarli al serializzatore/deserializzatore.

> [!WARNING]
> Se la richiesta è di grandi dimensioni, potrebbe causare una condizione di memoria insufficiente. La memoria insufficiente può causare un attacco Denial of Service.  Per altre informazioni, vedere evitare di leggere i corpi delle [richieste di grandi dimensioni o i corpi di risposta in memoria](#arlb) in questo documento.

<xref:System.Text.Json>Per impostazione predefinita, ASP.NET Core 3,0 USA per la serializzazione JSON. <xref:System.Text.Json>:

* Legge e scrive JSON in modo asincrono.
* È ottimizzato per il testo UTF-8.
* Prestazioni in genere superiori rispetto a `Newtonsoft.Json` .

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>Non archiviare IHttpContextAccessor. HttpContext in un campo

[IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) restituisce l'oggetto `HttpContext` della richiesta attiva quando si accede dal thread della richiesta. L' `IHttpContextAccessor.HttpContext` oggetto **non** deve essere archiviato in un campo o in una variabile.

Non **eseguire questa operazione:** Nell'esempio seguente viene archiviato `HttpContext` in un campo e quindi viene eseguito un tentativo di utilizzarlo in un secondo momento.

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

Il codice precedente acquisisce frequentemente un valore null o errato `HttpContext` nel costruttore.

**Eseguire questa operazione:** Nell'esempio seguente:

* Archivia <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> in un campo.
* Utilizza il `HttpContext` campo all'ora corretta e verifica la presenza di `null` .

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>Non accedere a HttpContext da più thread

`HttpContext`*non* è thread-safe. L'accesso `HttpContext` da più thread in parallelo può causare un comportamento indefinito, ad esempio blocchi, arresti anomali e danneggiamenti dei dati.

Non **eseguire questa operazione:** Nell'esempio seguente vengono effettuate tre richieste parallele e viene registrato il percorso della richiesta in ingresso prima e dopo la richiesta HTTP in uscita. È possibile accedere al percorso della richiesta da più thread, potenzialmente in parallelo.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

**Eseguire questa operazione:** Nell'esempio seguente vengono copiati tutti i dati dalla richiesta in ingresso prima di eseguire le tre richieste parallele.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>Non usare HttpContext dopo il completamento della richiesta

`HttpContext` è valido solo se nella pipeline ASP.NET Core è presente una richiesta HTTP attiva. L'intera pipeline di ASP.NET Core è una catena asincrona di delegati che esegue tutte le richieste. Quando l'oggetto `Task` restituito da questa catena viene completato, `HttpContext` viene riciclato.

Non **eseguire questa operazione:** Nell'esempio seguente viene usato il comando `async void` che rende la richiesta HTTP completa quando `await` viene raggiunto il primo:

* Si tratta **sempre** di una procedura non valida nelle app ASP.NET Core.
* Accede al `HttpResponse` dopo il completamento della richiesta HTTP.
* Arresta in modo anomalo il processo.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

**Eseguire questa operazione:** Nell'esempio seguente viene restituito un oggetto `Task` al Framework, pertanto la richiesta HTTP non viene completata fino al completamento dell'azione.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>Non acquisire HttpContext nei thread in background

Non **eseguire questa operazione:** Nell'esempio seguente viene illustrata una chiusura che acquisisce `HttpContext` dalla `Controller` Proprietà. Si tratta di una procedura non valida perché l'elemento di lavoro potrebbe:

* Eseguire all'esterno dell'ambito della richiesta.
* Tentativo di leggere l'oggetto errato `HttpContext` .

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

**Eseguire questa operazione:** Nell'esempio seguente:

* Copia i dati necessari nell'attività in background durante la richiesta.
* Non fa riferimento ad alcun elemento del controller.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

Le attività in background devono essere implementate come servizi ospitati. Per altre informazioni, vedere [Attività in background con servizi ospitati](xref:fundamentals/host/hosted-services).

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>Non acquisire i servizi inseriti nei controller nei thread in background

Non **eseguire questa operazione:** Nell'esempio seguente viene illustrata una chiusura che acquisisce `DbContext` dal `Controller` parametro Action. Si tratta di una procedura non valida.  L'elemento di lavoro può essere eseguito all'esterno dell'ambito della richiesta. `ContosoDbContext`Ha come ambito la richiesta, ottenendo un oggetto `ObjectDisposedException` .

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

**Eseguire questa operazione:** Nell'esempio seguente:

* Inserisce un oggetto per <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> creare un ambito nell'elemento di lavoro in background. `IServiceScopeFactory` è un singleton.
* Crea un nuovo ambito di inserimento delle dipendenze nel thread in background.
* Non fa riferimento ad alcun elemento del controller.
* Non acquisisce `ContosoDbContext` dalla richiesta in ingresso.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

Il codice evidenziato seguente:

* Crea un ambito per la durata dell'operazione in background e risolve i servizi.
* USA `ContosoDbContext` dall'ambito corretto.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a>Non modificare il codice di stato o le intestazioni dopo l'avvio del corpo della risposta

ASP.NET Core non memorizza nel buffer il corpo della risposta HTTP. La prima volta che viene scritta la risposta:

* Le intestazioni vengono inviate insieme al blocco del corpo al client.
* Non è più possibile modificare le intestazioni della risposta.

Non **eseguire questa operazione:** Il codice seguente tenta di aggiungere intestazioni di risposta dopo che la risposta è già stata avviata:

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

Nel codice precedente, `context.Response.Headers["test"] = "test value";` genererà un'eccezione se `next()` ha scritto nella risposta.

**Eseguire questa operazione:** Nell'esempio seguente viene verificato se la risposta HTTP è stata avviata prima di modificare le intestazioni.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

**Eseguire questa operazione:** Nell'esempio seguente viene usato `HttpResponse.OnStarting` per impostare le intestazioni prima che le intestazioni di risposta vengano scaricate nel client.

Verifica se la risposta non è iniziata consente la registrazione di un callback che verrà richiamato immediatamente prima della scrittura delle intestazioni di risposta. Verifica per verificare se la risposta non è stata avviata:

* Fornisce la possibilità di accodare o eseguire l'override delle intestazioni solo nel tempo.
* Non richiede la conoscenza del middleware successivo nella pipeline.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>Non chiamare Next () se è già stata avviata la scrittura nel corpo della risposta

I componenti si aspettano di essere chiamati solo se è possibile gestire e modificare la risposta.

## <a name="use-in-process-hosting-with-iis"></a>Usare l'hosting in-process con IIS

Se si usa l'hosting in-process, un'app ASP.NET Core esegue lo stesso processo del processo di lavoro IIS. L'hosting in-process garantisce prestazioni migliori rispetto all'hosting out-of-process, perché le richieste non vengono inviate tramite proxy sulla scheda loopback. La scheda loopback è un'interfaccia di rete che restituisce il traffico di rete in uscita allo stesso computer. Per gestire il processo, IIS usa il [servizio Attivazione processo Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Per impostazione predefinita, i progetti sono basati sul modello di hosting in-process in ASP.NET Core 3,0 e versioni successive.

Per ulteriori informazioni, vedere [ASP.NET Core host in Windows con IIS](xref:host-and-deploy/iis/index)
