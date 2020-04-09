---
title: Scenari Blazor avanzati di ASP.NET Core
author: guardrex
description: Informazioni sugli scenari Blazoravanzati in , incluso come incorporare la logica RenderTreeBuilder manuale in un'app.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 5edbbe36e8389bac0335594b1e4331aee1c02867
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659453"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a>ASP.NET gli scenari avanzati di Core Blazor

Di [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

## <a name="blazor-server-circuit-handler"></a>Blazor Gestore circuito server

Blazor Server consente al codice di definire un *gestore di circuito*, che consente l'esecuzione di codice sulle modifiche apportate allo stato del circuito di un utente. Un gestore di circuito `CircuitHandler` viene implementato derivando e registrando la classe nel contenitore dei servizi dell'app. L'esempio seguente di un gestore di circuito tiene traccia delle connessioni SignalR aperte:The following example of a circuit handler tracks open SignalR connections:

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> _circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => _circuits.Count;
}
```

I gestori di circuito vengono registrati tramite DI. Le istanze con ambito vengono create per ogni istanza di un circuito. Utilizzando `TrackingCircuitHandler` l'esempio precedente, viene creato un servizio singleton perché è necessario tenere traccia dello stato di tutti i circuiti:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

Se i metodi di un gestore di circuito personalizzato generano un'eccezione non gestita, l'eccezione viene irreversibile per il circuito Blazor Server. Per tollerare le eccezioni nel codice di un gestore o nei metodi chiamati, eseguire il wrapping del codice in una o più istruzioni [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) con la gestione e la registrazione degli errori.

Quando un circuito termina perché un utente si è disconnesso e il framework sta pulendo lo stato del circuito, il framework elimina l'ambito DI del circuito. L'eliminazione dell'ambito elimina tutti i servizi <xref:System.IDisposable?displayProperty=fullName>DI con ambito circuito che implementano . Se un servizio DI genera un'eccezione non gestita durante l'eliminazione, il framework registra l'eccezione.

## <a name="manual-rendertreebuilder-logic"></a>Logica Manuale di RenderTreeBuilder

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder`fornisce metodi per la modifica di componenti ed elementi, inclusa la compilazione manuale dei componenti nel codice C.

> [!NOTE]
> L'utilizzo di `RenderTreeBuilder` per creare componenti è uno scenario avanzato. Un componente in formato non valido (ad esempio, un tag di markup non chiuso) può causare un comportamento indefinito.

Si consideri il componente seguente, `PetDetails` che può essere incorporato manualmente in un altro componente:

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

Nell'esempio seguente, il `CreateComponent` ciclo nel `PetDetails` metodo genera tre componenti. Quando `RenderTreeBuilder` si chiamano i`OpenComponent` metodi `AddAttribute`per creare i componenti ( e ), i numeri di sequenza sono numeri di riga del codice sorgente. L'algoritmo di differenza Blazor si basa sui numeri di sequenza corrispondenti a righe di codice distinte, non su chiamate distinte. Quando si crea `RenderTreeBuilder` un componente con metodi, codificare gli argomenti per i numeri di sequenza. **L'utilizzo di un calcolo o di un contatore per generare il numero di sequenza può causare una riduzione delle prestazioni.** Per ulteriori informazioni, vedere la sezione Numeri di sequenza relativi ai numeri di riga del [codice e non all'ordine](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) di esecuzione.

`BuiltContent`Componente:

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> I tipi `Microsoft.AspNetCore.Components.RenderTree` in consentono l'elaborazione dei *risultati* delle operazioni di rendering. Questi sono i dettagli interni dell'implementazione del quadro Blazor. Questi tipi devono essere considerati *instabili* e soggetti a modifiche nelle versioni future.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>I numeri di sequenza si riferiscono ai numeri di riga del codice e non all'ordine di esecuzione

I file dei componenti Razor (*.razor*) vengono sempre compilati. La compilazione è un potenziale vantaggio rispetto all'interpretazione del codice perché l'istruzione di compilazione può essere usata per inserire informazioni che migliorano le prestazioni dell'app in fase di esecuzione.

Un esempio chiave di questi miglioramenti riguarda i numeri di *sequenza.* I numeri di sequenza indicano al runtime quali output provengono da quali righe di codice distinte e ordinate. Il runtime utilizza queste informazioni per generare diffs albero efficiente in tempo lineare, che è molto più veloce di quanto sia normalmente possibile per un algoritmo generale diff albero.

Si consideri il seguente file del componente Razor (*.razor*):

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

Il codice precedente viene compilato in qualcosa di simile al seguente:The preceding code compiles to something like the following:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Quando il codice viene eseguito `someFlag` per `true`la prima volta, se è , il generatore riceve:

| Sequenza | Type      | Data   |
| :------: | --------- | :----: |
| 0        | Nodo testo | First (Primo)  |
| 1        | Nodo testo | Second |

Si `someFlag` supponga `false`che diventa , e il markup viene eseguito nuovamente il rendering. Questa volta, il costruttore riceve:

| Sequenza | Type       | Data   |
| :------: | ---------- | :----: |
| 1        | Nodo testo  | Second |

Quando il runtime esegue un diff, vede `0` che l'elemento in sequenza è stato rimosso, pertanto genera il seguente script di *modifica*semplice:

* Rimuovere il primo nodo di testo.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>Il problema con la generazione di numeri di sequenza a livello di codice

Si supponga invece di aver scritto la logica del generatore di albero di rendering seguente:Imagine instead that you wrote the following render tree builder logic:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Ora, il primo output è:

| Sequenza | Type      | Data   |
| :------: | --------- | :----: |
| 0        | Nodo testo | First (Primo)  |
| 1        | Nodo testo | Second |

Questo risultato è identico al caso precedente, quindi non esistono problemi negativi. `someFlag`è `false` sul secondo rendering e l'output è:

| Sequenza | Type      | Data   |
| :------: | --------- | ------ |
| 0        | Nodo testo | Second |

Questa volta, l'algoritmo diff vede che si sono verificate *due* modifiche e l'algoritmo genera il seguente script di modifica:

* Modificare il valore del primo `Second`nodo di testo in .
* Rimuovere il secondo nodo di testo.

La generazione dei numeri di sequenza ha `if/else` perso tutte le informazioni utili su dove erano presenti i rami e i cicli nel codice originale. Questo si traduce in un calo **due volte più a lungo** di prima.

Questo è un esempio banale. Nei casi più realistici con strutture complesse e profondamente annidate, e soprattutto con i cicli, il costo delle prestazioni è in genere più elevato. Invece di identificare immediatamente quali blocchi di loop o rami sono stati inseriti o rimossi, l'algoritmo diff deve ricorsione profondamente negli alberi di rendering. Ciò comporta in genere la creazione di script di modifica più lunghi perché l'algoritmo diff è disinformato su come le strutture vecchie e nuove sono correlate tra loro.

### <a name="guidance-and-conclusions"></a>Orientamenti e conclusioni

* Le prestazioni dell'app risentono se i numeri di sequenza vengono generati in modo dinamico.
* Il framework non può creare automaticamente i propri numeri di sequenza in fase di esecuzione perché le informazioni necessarie non esistono a meno che non vengano acquisite in fase di compilazione.
* Non scrivere blocchi lunghi di `RenderTreeBuilder` logica implementata manualmente. Preferire i file *.razor* e consentire al compilatore di gestire i numeri di sequenza. Se non si riesce `RenderTreeBuilder` a evitare la logica manuale, dividere lunghi blocchi di codice in parti più piccole racchiuse nelle `OpenRegion` / `CloseRegion` chiamate. Ogni regione ha il proprio spazio separato di numeri di sequenza, quindi è possibile riavviare da zero (o qualsiasi altro numero arbitrario) all'interno di ogni area.
* Se i numeri di sequenza sono hardcoded, l'algoritmo diff richiede solo che i numeri di sequenza aumentino di valore. Il valore iniziale e le lacune sono irrilevanti. Un'opzione legittima consiste nell'utilizzare il numero della riga di codice come numero di sequenza, oppure iniziare da zero e aumentare di uno o centinaia (o qualsiasi intervallo preferito). 
* Blazorusa i numeri di sequenza, mentre altri framework dell'interfaccia utente con espansione ad albero non li usano. La differenza è molto più veloce quando Blazor vengono utilizzati i numeri di sequenza e ha il vantaggio di una fase di compilazione che gestisce automaticamente i numeri di sequenza per gli sviluppatori che creano file *con estensione razor.*

## <a name="perform-large-data-transfers-in-opno-locblazor-server-apps"></a>Eseguire trasferimenti Blazor di dati di grandi dimensioni nelle app serverPerform large data transfers in Server apps

In alcuni scenari, è necessario trasferire grandi quantità Blazordi dati tra JavaScript e . In genere, i trasferimenti di dati di grandi dimensioni si verificano quando:Typically, large data transfers occur when:

* Le API del file system del browser vengono utilizzate per caricare o scaricare un file.
* È necessaria l'interoperabilità con una libreria di terze parti.

In Blazor Server, è possibile impedire il passaggio di singoli messaggi di grandi dimensioni che possono causare problemi di prestazioni.

Quando sviluppi codice che trasferisce dati tra BlazorJavaScript e:

* Suddividere i dati in parti più piccole e inviare i segmenti di dati in sequenza fino a quando tutti i dati non vengono ricevuti dal server.
* Non allocare oggetti di grandi dimensioni in JavaScript e nel codice di C.
* Non bloccare il thread dell'interfaccia utente principale per lunghi periodi durante l'invio o la ricezione di dati.
* Liberare la memoria utilizzata al termine o all'annullamento del processo.
* Applicare i seguenti requisiti aggiuntivi per motivi di sicurezza:
  * Dichiarare il file o la dimensione massima dei dati che è possibile superare.
  * Dichiarare la velocità di caricamento minima dal client al server.
* Dopo che i dati vengono ricevuti dal server, i dati possono essere:
  * Memorizzati temporaneamente in un buffer di memoria fino a quando non vengono raccolti tutti i segmenti.
  * Consumato immediatamente. Ad esempio, i dati possono essere archiviati immediatamente in un database o scritti su disco man mano che ogni segmento viene ricevuto.

La classe del caricatore di file seguente gestisce l'interoperabilità JS con il client. La classe uploader utilizza l'interoperabilità JS per:

* Eseguire il polling del client per l'invio di un segmento di dati.
* Interrompere la transazione in caso di timeout del polling.

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private readonly int _segmentSize = 6144;
    private readonly int _maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> _thisReference;
    private List<IMemoryOwner<byte>> _uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await _jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)_segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await _jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < _maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(_segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, _segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          _uploadedSegments.Add(current);
        }

        var segments = _uploadedSegments;
        _uploadedSegments = null;

        return new SegmentedStream(segments, _segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (_uploadedSegments != null)
        {
            foreach (var segment in _uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

Nell'esempio precedente:

* L'oggetto `_maxBase64SegmentSize` `8192`è impostato su `_maxBase64SegmentSize = _segmentSize * 4 / 3`, calcolato da .
* Le API di gestione della memoria .NET Core di basso livello `_uploadedSegments`vengono utilizzate per archiviare i segmenti di memoria nel server in .
* Un `ReceiveFile` metodo viene utilizzato per gestire il caricamento tramite l'interoperabilità JS:A method is used to handle the upload through JS interop:
  * La dimensione del file viene determinata in byte tramite l'interoperabilità JS con `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.
  * Il numero di segmenti da ricevere `numberOfSegments`viene calcolato e memorizzato in .
  * I segmenti vengono richiesti `for` in un `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`ciclo tramite l'interoperabilità JS con . Tutti i segmenti tranne l'ultimo devono essere 8.192 byte prima della decodifica. Il client è costretto a inviare i dati in modo efficiente.
  * Per ogni segmento ricevuto, i <xref:System.Convert.TryFromBase64String*>controlli vengono eseguiti prima della decodifica con .
  * Un flusso con i dati viene <xref:System.IO.Stream> `SegmentedStream`restituito come nuovo ( ) al termine del caricamento.

La classe di flusso segmentata espone l'elenco dei <xref:System.IO.Stream>segmenti come non ricercabile in sola lettura:

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> _sequence;
    private long _currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            _sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        _sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(_currentPosition + count < _sequence.Length ? 
            count : _sequence.Length - _currentPosition);
        var data = _sequence.Slice(_currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        _currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

Il codice seguente implementa le funzioni JavaScript per ricevere i dati:The following code implements JavaScript functions to receive the data:

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```
