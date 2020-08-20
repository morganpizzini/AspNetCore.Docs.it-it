---
title: Gestione della memoria e modelli in ASP.NET Core
author: rick-anderson
description: Informazioni sulla gestione della memoria in ASP.NET Core e sul funzionamento della Garbage Collector (GC).
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
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
uid: performance/memory
ms.openlocfilehash: c409eaaf07109d363581ee7d61dc76521d6818d0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630666"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a>Gestione della memoria e Garbage Collection (GC) in ASP.NET Core

Di [Sébastien Ros](https://github.com/sebastienros) e [Rick Anderson](https://twitter.com/RickAndMSFT)

La gestione della memoria è complessa, anche in un Framework gestito come .NET. L'analisi e la comprensione dei problemi di memoria possono essere complesse. Questo articolo:

* È stato motivato da molte *perdite di memoria* e da problemi *GC non funzionanti* . La maggior parte di questi problemi è stata causata dalla mancata comprensione del funzionamento dell'utilizzo della memoria in .NET Core o dalla mancata comprensione del modo in cui viene misurata.
* Viene illustrato l'utilizzo di memoria problematico e vengono suggeriti approcci alternativi.

## <a name="how-garbage-collection-gc-works-in-net-core"></a>Funzionamento di Garbage Collection (GC) in .NET Core

Il GC alloca i segmenti dell'heap in cui ogni segmento è un intervallo di memoria contiguo. Gli oggetti inseriti nell'heap vengono categorizzati in una delle tre generazioni seguenti: 0, 1 o 2. La generazione determina la frequenza con cui il GC tenta di rilasciare la memoria sugli oggetti gestiti a cui l'app non fa più riferimento. Le generazioni con numero inferiore sono più frequenti.

Gli oggetti vengono spostati da una generazione all'altra in base al relativo ciclo di vita. Man mano che gli oggetti si trovano più a lungo, vengono spostati in una generazione più elevata. Come indicato in precedenza, le generazioni più elevate sono meno frequenti. Gli oggetti vissuti a breve termine rimangono sempre nella generazione 0. Ad esempio, gli oggetti a cui viene fatto riferimento durante il ciclo di vita di una richiesta Web sono di breve durata. I [singleton](xref:fundamentals/dependency-injection#service-lifetimes) a livello di applicazione in genere migrano alla generazione 2.

Quando viene avviata un'app ASP.NET Core, il GC:

* Riserva una certa quantità di memoria per i segmenti heap iniziali.
* Esegue il commit di una piccola parte della memoria quando il runtime viene caricato.

Le allocazioni di memoria precedenti vengono eseguite per motivi di prestazioni. Il vantaggio in termini di prestazioni deriva da segmenti di heap nella memoria contigua.

### <a name="call-gccollect"></a>Chiamare GC. Raccogliere

Chiamata a [GC. Raccogli](xref:System.GC.Collect*) in modo esplicito:

* **Non** deve essere eseguita dalle app ASP.NET Core di produzione.
* È utile quando si esaminano le perdite di memoria.
* Quando si esamina, verifica che il GC abbia rimosso tutti gli oggetti penzolanti dalla memoria, in modo che sia possibile misurare la memoria.

## <a name="analyzing-the-memory-usage-of-an-app"></a>Analisi dell'utilizzo della memoria di un'app

Gli strumenti dedicati consentono di analizzare l'utilizzo della memoria:

- Conteggio dei riferimenti a oggetti
- Misurazione dell'effetto del Garbage Collector sull'utilizzo della CPU
- Misurazione dello spazio di memoria usato per ogni generazione

Usare gli strumenti seguenti per analizzare l'utilizzo della memoria:

* [DotNet-Trace](/dotnet/core/diagnostics/dotnet-trace): può essere usato nei computer di produzione.
* [Analizzare l'utilizzo della memoria senza il debugger di Visual Studio](/visualstudio/profiling/memory-usage-without-debugging2)
* [Profilare l'utilizzo della memoria in Visual Studio](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a>Rilevamento dei problemi di memoria

È possibile usare Gestione attività per avere un'idea della quantità di memoria usata da un'app ASP.NET. Valore di memoria di gestione attività:

* Rappresenta la quantità di memoria utilizzata dal processo ASP.NET.
* Include gli oggetti Living dell'app e altri consumer di memoria, ad esempio l'utilizzo della memoria nativa.

Se il valore di memoria di gestione attività aumenta a tempo indefinito e non si rende mai Flat, l'app presenta una perdita di memoria. Le sezioni seguenti illustrano e spiegano diversi modelli di utilizzo della memoria.

## <a name="sample-display-memory-usage-app"></a>App di esempio per visualizzare l'utilizzo della memoria

L' [app di esempio Memoryleak](https://github.com/sebastienros/memoryleak) è disponibile in GitHub. App MemoryLeak:

* Include un controller di diagnostica che raccoglie la memoria in tempo reale e i dati GC per l'app.
* Dispone di una pagina di indice che Visualizza i dati di memoria e GC. La pagina di indice viene aggiornata ogni secondo.
* Contiene un controller API che fornisce diversi modelli di carico di memoria.
* Non è uno strumento supportato, tuttavia, può essere usato per visualizzare i modelli di utilizzo della memoria delle app ASP.NET Core.

Eseguire MemoryLeak. La memoria allocata aumenta lentamente fino a quando non si verifica un catalogo globale. La memoria aumenta perché lo strumento alloca l'oggetto personalizzato per l'acquisizione dei dati. La figura seguente mostra la pagina di indice MemoryLeak quando si verifica un GC di generazione 0. Il grafico Mostra 0 RPS (richieste al secondo) perché non è stato chiamato alcun endpoint API dal controller API.

![grafico precedente](memory/_static/0RPS.png)

Nel grafico vengono visualizzati due valori per l'utilizzo della memoria:

- Allocata: quantità di memoria occupata dagli oggetti gestiti
- [Working set](/windows/win32/memory/working-set): set di pagine nello spazio degli indirizzi virtuali del processo che attualmente risiede nella memoria fisica. Il working set visualizzato è lo stesso valore visualizzato in Gestione attività.

### <a name="transient-objects"></a>Oggetti temporanei

L'API seguente crea un'istanza di stringa di 10 KB e la restituisce al client. Per ogni richiesta, un nuovo oggetto viene allocato in memoria e scritto nella risposta. Le stringhe vengono archiviate come caratteri UTF-16 in .NET in modo che ogni carattere accetti 2 byte di memoria.

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

Il grafico seguente viene generato con un carico relativamente piccolo in per illustrare il modo in cui le allocazioni di memoria sono interessate dal GC.

![grafico precedente](memory/_static/bigstring.png)

Il grafico precedente Mostra:

* RPS 4K (richieste al secondo).
* Le raccolte GC di generazione 0 si verificano ogni due secondi.
* Il working set è costante a circa 500 MB.
* CPU è il 12%.
* Il consumo di memoria e la versione (tramite GC) è stabile.

Il grafico seguente viene considerato alla velocità effettiva massima che può essere gestita dal computer.

![grafico precedente](memory/_static/bigstring2.png)

Il grafico precedente Mostra:

* RPS 22K
* Le raccolte GC di generazione 0 si verificano più volte al secondo.
* Le raccolte di generazione 1 vengono attivate perché l'app ha allocato significativamente più memoria al secondo.
* Il working set è costante a circa 500 MB.
* La CPU è pari al 33%.
* Il consumo di memoria e la versione (tramite GC) è stabile.
* CPU (33%) non è sovrautilizzato, quindi il Garbage Collection può essere aggiornato con un numero elevato di allocazioni.

### <a name="workstation-gc-vs-server-gc"></a>GC workstation rispetto a GC server

Il Garbage Collector di .NET prevede due modalità diverse:

* **GC workstation**: ottimizzato per il desktop.
* **GC server**. GC predefinito per le app ASP.NET Core. Ottimizzato per il server.

La modalità GC può essere impostata in modo esplicito nel file di progetto o nel *runtimeconfig.js* file dell'app pubblicata. Il markup seguente mostra l'impostazione `ServerGarbageCollection` nel file di progetto:

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

`ServerGarbageCollection`Per modificare il file di progetto, è necessario ricompilare l'app.

**Nota:** Il Garbage Collection server **non** è disponibile nei computer con un singolo core. Per altre informazioni, vedere <xref:System.Runtime.GCSettings.IsServerGC>.

La figura seguente mostra il profilo di memoria in un RPS da 5K usando il GC della workstation.

![grafico precedente](memory/_static/workstation.png)

Le differenze tra il grafico e la versione del server sono significative:

- Il working set scende da 500 MB a 70 MB.
- Il GC esegue raccolte di generazione 0 più volte al secondo anziché ogni due secondi.
- GC scende da 300 MB a 10 MB.

In un tipico ambiente server Web, l'utilizzo della CPU è più importante della memoria, quindi il GC del server è migliore. Se l'utilizzo della memoria è elevato e l'utilizzo della CPU è relativamente basso, il GC della workstation potrebbe essere più efficiente. Ad esempio, la densità elevata ospita diverse app Web in cui la memoria è limitata.

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a>GC con contenitori Docker e Small

Quando più app in contenitori sono in esecuzione in un solo computer, GC workstation potrebbe essere più preforme rispetto a GC server. Per ulteriori informazioni, vedere [esecuzione con GC del server in un contenitore di piccole dimensioni](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) e [esecuzione con GC del server in uno scenario di contenitori di piccole dimensioni parte 1: limite rigido per l'heap GC](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).

### <a name="persistent-object-references"></a>Riferimenti a oggetti permanenti

Il Garbage Collector non può liberare oggetti a cui si fa riferimento. Gli oggetti a cui viene fatto riferimento ma che non sono più necessari generano una perdita di memoria. Se l'app alloca spesso oggetti e non riesce a liberarli dopo che non sono più necessari, l'utilizzo della memoria aumenterà nel tempo.

L'API seguente crea un'istanza di stringa di 10 KB e la restituisce al client. La differenza con l'esempio precedente è che a questa istanza viene fatto riferimento da un membro statico, il che significa che non è mai disponibile per la raccolta.

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

Il codice precedente:

* È un esempio di una perdita di memoria tipica.
* Con chiamate frequenti, causa l'aumento della memoria dell'app finché il processo non si arresta in modo anomalo con un' `OutOfMemory` eccezione.

![grafico precedente](memory/_static/eternal.png)

Nell'immagine precedente:

* Il test di carico l' `/api/staticstring` endpoint causa un aumento lineare della memoria.
* Il GC tenta di liberare memoria man mano che aumenta la quantità di memoria, chiamando una raccolta di generazione 2.
* Il Garbage Collector non può liberare la memoria persa. Allocato e working set aumentare con il tempo.

Per alcuni scenari, ad esempio la memorizzazione nella cache, è necessario che i riferimenti agli oggetti vengano mantenuti fino a quando non viene forzato il rilascio della memoria. La <xref:System.WeakReference> classe può essere utilizzata per questo tipo di codice di memorizzazione nella cache. Un `WeakReference` oggetto viene raccolto sotto pressione di memoria. L'implementazione predefinita di <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> utilizza `WeakReference` .

### <a name="native-memory"></a>Memoria nativa

Alcuni oggetti .NET Core si basano sulla memoria nativa. La memoria nativa **non** può essere raccolta dal GC. L'oggetto .NET che utilizza la memoria nativa deve liberarlo utilizzando codice nativo.

.NET fornisce l' <xref:System.IDisposable> interfaccia per consentire agli sviluppatori di rilasciare la memoria nativa. Anche se <xref:System.IDisposable.Dispose*> non viene chiamato, le classi implementate correttamente chiamano `Dispose` quando viene eseguito il [finalizzatore](/dotnet/csharp/programming-guide/classes-and-structs/destructors) .

Osservare il codice seguente:

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) è una classe gestita, quindi qualsiasi istanza verrà raccolta alla fine della richiesta.

La figura seguente mostra il profilo di memoria durante la chiamata `fileprovider` continua dell'API.

![grafico precedente](memory/_static/fileprovider.png)

Il grafico precedente mostra un problema evidente con l'implementazione di questa classe, poiché continua ad aumentare l'utilizzo della memoria. Si tratta di un problema noto che viene rilevato in [questo problema](https://github.com/dotnet/aspnetcore/issues/3110).

La stessa perdita può verificarsi nel codice utente, in uno dei seguenti:

* Non è stata rilasciata correttamente la classe.
* Si dimentica di richiamare il `Dispose` metodo degli oggetti dipendenti che devono essere eliminati.

### <a name="large-objects-heap"></a>Heap oggetti grandi

I cicli di allocazione di memoria/disponibili frequenti possono frammentare la memoria, soprattutto quando si allocano blocchi di memoria di grandi dimensioni. Gli oggetti vengono allocati in blocchi di memoria contigui. Per attenuare la frammentazione, quando il GC libera memoria, trys la deframmentazione. Questo processo è denominato **compattazione**. La compattazione comporta lo stato di trasferimento degli oggetti. Lo spostato di oggetti di grandi dimensioni impone una riduzione delle prestazioni. Per questo motivo il GC crea una zona di memoria speciale per oggetti di _grandi dimensioni_ , denominata [heap degli oggetti grandi](/dotnet/standard/garbage-collection/large-object-heap) (LOH). Gli oggetti che sono maggiori di 85.000 byte (circa 83 KB) sono:

* Inserito nell'heap oggetti grandi.
* Non compattato.
* Raccolta durante i cataloghi globali di generazione 2.

Quando l'heap oggetti grandi è pieno, il GC attiverà una raccolta di generazione 2. Raccolte di generazione 2:

* Sono intrinsecamente lenti.
* Comportano inoltre il costo di attivazione di una raccolta in tutte le altre generazioni.

Il codice seguente comprime immediatamente l'oggetto LOH:

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode>Per informazioni sulla compattazione dell'heap oggetti grandi, vedere.

Nei contenitori che usano .NET Core 3,0 e versioni successive, l'heap oggetti grandi viene compattato automaticamente.

Nell'API seguente viene illustrato questo comportamento:

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

Il grafico seguente mostra il profilo di memoria della chiamata all' `/api/loh/84975` endpoint, in carico massimo:

![grafico precedente](memory/_static/loh1.png)

Il grafico seguente mostra il profilo di memoria della chiamata all' `/api/loh/84976` endpoint, allocando *solo un byte*:

![grafico precedente](memory/_static/loh2.png)

Nota: la `byte[]` struttura presenta byte di overhead. Per questo motivo 84.976 byte attiva il limite di 85.000.

Confronto tra i due grafici precedenti:

* Il working set è simile per entrambi gli scenari, circa 450 MB.
* Il sotto richieste LOH (84.975 byte) Mostra per lo più le raccolte di generazione 0.
* Le richieste over LOH generano raccolte di generazione 2 costanti. Le raccolte di generazione 2 sono costose. È necessaria una quantità maggiore di CPU e la velocità effettiva scende quasi al 50%.

Gli oggetti temporanei di grandi dimensioni sono particolarmente problematici perché comportano l'Gen2.

Per ottenere prestazioni ottimali, è necessario ridurre al minimo l'utilizzo di oggetti di grandi dimensioni. Se possibile, suddividere oggetti di grandi dimensioni. Il middleware di [memorizzazione](xref:performance/caching/response) nella cache delle risposte, ad esempio, in ASP.NET Core suddivide le voci della cache in blocchi inferiori a 85.000 byte.

I collegamenti seguenti illustrano l'approccio ASP.NET Core per mantenere gli oggetti sotto il limite dell'heap oggetti grandi:

* [ResponseCaching/Streams/StreamUtilities. cs](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [ResponseCaching/MemoryResponseCache. cs](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

Per altre informazioni, vedere:

* [Heap Large Object individuato](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Heap oggetti grandi](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a>HttpClient

L'uso errato <xref:System.Net.Http.HttpClient> di può causare una perdita di risorse. Risorse di sistema, ad esempio connessioni di database, socket, handle di file e così via:

* Sono più limitate della memoria.
* Sono più problematiche quando si verifica una perdita di memoria.

Gli sviluppatori .NET esperti sanno di chiamare <xref:System.IDisposable.Dispose*> sugli oggetti che implementano <xref:System.IDisposable> . La mancata eliminazione degli oggetti che implementano in `IDisposable` genere comporta la perdita di memoria o di risorse di sistema perse.

`HttpClient` implementa `IDisposable` , ma **non** deve essere eliminato a ogni chiamata. È invece `HttpClient` consigliabile riutilizzare.

L'endpoint seguente crea e Elimina una nuova  `HttpClient` istanza per ogni richiesta:

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

Sotto carico vengono registrati i messaggi di errore seguenti:

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

Anche se le `HttpClient` istanze vengono eliminate, la connessione di rete effettiva richiede del tempo per essere rilasciata dal sistema operativo. Creando continuamente nuove connessioni, si verifica l' _esaurimento delle porte_ . Ogni connessione client richiede una propria porta client.

Un modo per evitare l'esaurimento delle porte consiste nel riutilizzare la stessa `HttpClient` istanza:

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

L' `HttpClient` istanza viene rilasciata quando l'app viene arrestata. Questo esempio mostra che non tutte le risorse eliminabili devono essere eliminate dopo ogni uso.

Per un modo migliore per gestire la durata di un'istanza, vedere quanto segue `HttpClient` :

* [Gestione di HttpClient e durata](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [Blog di HTTPClient Factory](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a>Pooling di oggetti

Nell'esempio precedente è stato illustrato come l' `HttpClient` istanza può essere resa statica e riutilizzata da tutte le richieste. Il riutilizzo impedisce l'esaurimento delle risorse.

Pool di oggetti:

* Usa il modello di riutilizzo.
* È progettato per gli oggetti che sono costosi da creare.

Un pool è una raccolta di oggetti pre-inizializzati che possono essere riservati e rilasciati tra thread. I pool possono definire regole di allocazione quali limiti, dimensioni predefinite o tasso di crescita.

Il pacchetto NuGet [Microsoft. Extensions. Objectpool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contiene classi che consentono di gestire tali pool.

L'endpoint API seguente crea un'istanza di un `byte` buffer che viene compilato con numeri casuali per ogni richiesta:

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

Il grafico seguente mostra la chiamata all'API precedente con carico moderato:

![grafico precedente](memory/_static/array.png)

Nel grafico precedente, le raccolte di generazione 0 avvengono approssimativamente una volta al secondo.

Il codice precedente può essere ottimizzato raggruppando il `byte` buffer usando [ \<T> ArrayPool](xref:System.Buffers.ArrayPool`1). Un'istanza statica viene riutilizzata tra le richieste.

Ciò che è diverso con questo approccio è che un oggetto in pool viene restituito dall'API. Ciò significa che:

* L'oggetto è fuori dal controllo non appena si ritorna dal metodo.
* Non è possibile rilasciare l'oggetto.

Per configurare l'eliminazione dell'oggetto:

* Incapsula la matrice in pool in un oggetto eliminabile.
* Registrare l'oggetto in pool con [HttpContext. Response. RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).

`RegisterForDispose` si occuperà di chiamare `Dispose` sull'oggetto di destinazione in modo che venga rilasciato solo quando la richiesta HTTP è stata completata.

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

Se si applica lo stesso carico della versione non in pool, viene restituito il grafico seguente:

![grafico precedente](memory/_static/pooledarray.png)

La differenza principale è costituita dai byte allocati e, di conseguenza, un numero minore di raccolte di generazione 0.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Garbage Collection](/dotnet/standard/garbage-collection/)
* [Informazioni sulle diverse modalità GC con il Visualizzatore di concorrenza](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [Heap Large Object individuato](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Heap oggetti grandi](/dotnet/standard/garbage-collection/large-object-heap)
